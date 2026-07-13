### ssh2proxy

**Convierte una conexión SSH en un proxy de navegación**, reenviando el tráfico de tu navegador a través de un equipo remoto al que te conectas por SSH — sin necesidad de instalar software adicional, solo usando las capacidades de *port forwarding* del propio protocolo SSH.

[![Túnel SSH](https://github.com/hackingyseguridad/ssh2proxy/raw/master/tunel_ssh.png)](https://github.com/hackingyseguridad/ssh2proxy/blob/master/tunel_ssh.png)

[![Licencia](https://img.shields.io/badge/licencia-GPL--3.0-blue.svg)](LICENSE)
[![Shell](https://img.shields.io/badge/lenguaje-Shell-89e051.svg)](https://github.com/hackingyseguridad/ssh2proxy/search?l=shell)

---

### Tabla de contenidos

- [¿Qué es esto?](#qué-es-esto)
- [Tabla resumen: comando y flags](#tabla-resumen-comando-y-flags)
- [Requisitos](#requisitos)
- [Uso desde consola Linux](#uso-desde-consola-linux)
- [Uso con PuTTY (Windows) + configuración del navegador](#uso-con-putty-windows--configuración-del-navegador)
- [Configuración necesaria en el servidor SSH](#configuración-necesaria-en-el-servidor-ssh)
- [Tabla resumen: opciones de sshd_config](#tabla-resumen-opciones-de-sshd_config)
- [Verificación del túnel](#verificación-del-túnel)


---

### ¿Qué es esto?

Un túnel SSH con reenvío de puertos permite usar un servidor remoto (al que tienes acceso por SSH) como salida de tu tráfico de navegación. Es útil para:

| Escenario | Por qué usarlo |
|---|---|
| Redes restringidas (wifi de hotel, oficina, universidad) | Sales a Internet a través del servidor remoto, evitando filtros locales |
| Acceder a servicios internos | Llegar a paneles o puertos que solo escuchan en `localhost` del servidor remoto |
| Navegación cifrada en redes no confiables | Todo el tráfico entre tú y el servidor va cifrado por el túnel SSH |
| Pruebas con distinta IP de salida | Navegar con la IP pública del servidor remoto |

> Técnicamente se trata de un **proxy SOCKS** creado con la opción `-D` de SSH, o de un **reenvío de puerto local** con `-L`, según el objetivo. Ambos se explican más abajo.

---

### Tabla resumen: comando y flags

| Flag | Significado | Uso en este proyecto |
|---|---|---|
| `-L 8080:localhost:22` | Reenvío de puerto **local**: el puerto 8080 de tu máquina se redirige a `localhost:22` visto desde el servidor remoto | Ejemplo base del repositorio |
| `-D <puerto>` | Crea un **proxy SOCKS dinámico** en el puerto indicado: cualquier tráfico enviado a él sale a Internet a través del servidor remoto | Recomendado para navegar (ver más abajo) |
| `-f` | Ejecuta el túnel en **segundo plano** (background), liberando la terminal | Para dejar el túnel corriendo sin ocupar la consola |
| `-N` | No abre una sesión de shell tras conectar, solo mantiene el reenvío activo | Evita abrir una terminal innecesaria en el servidor remoto |
| `-C` | Comprime el tráfico del túnel | Opcional, útil en conexiones lentas |

---

### Requisitos

- Cliente SSH (OpenSSH en Linux/macOS, o PuTTY en Windows).
- Acceso SSH (usuario + clave o contraseña) a un servidor remoto.
- Permisos en el servidor para habilitar reenvío de puertos (ver [Configuración necesaria en el servidor SSH](#configuración-necesaria-en-el-servidor-ssh)).
- Navegador o sistema operativo configurable con proxy SOCKS/HTTP.

---

### Uso desde consola Linux

### Reenvío de puerto local (ejemplo del proyecto)

```bash
ssh -f $1 -L 8080:localhost:22 -N
```

Donde `$1` es el host remoto (`usuario@servidor`). Este ejemplo concreto redirige tu puerto local `8080` al puerto `22` (SSH) visto desde el servidor remoto — útil, por ejemplo, para acceder al SSH de una máquina que solo es alcanzable desde ese servidor.

### Proxy SOCKS dinámico (recomendado para navegar)

Para usar el servidor remoto como salida de todo tu tráfico de navegación, la forma habitual es un proxy SOCKS dinámico:

```bash
ssh -f -N -D 1080 usuario@servidor
```

Esto abre un proxy SOCKS5 en `localhost:1080` de tu máquina. Cualquier aplicación configurada para usar ese proxy saldrá a Internet con la IP del servidor remoto.

---

### Uso con PuTTY (Windows) + configuración del navegador

1. Abre **PuTTY** → *Connection → SSH → Tunnels*.
2. En **Source port** indica el puerto local (por ejemplo `1080`).
3. Selecciona **Dynamic** y pulsa **Add**.
4. Conecta normalmente con tus credenciales SSH.
5. Configura el navegador para usar un proxy **SOCKS** apuntando a `localhost:1080`.

[![Configuración de proxy SOCKS en el navegador](https://github.com/hackingyseguridad/ssh2proxy/raw/master/ssh2proxy.png)](https://github.com/hackingyseguridad/ssh2proxy/blob/master/ssh2proxy.png)

---

### Configuración necesaria en el servidor SSH

En el servidor remoto (`/etc/ssh/sshd_config`) deben estar habilitadas estas opciones para que el reenvío de puertos funcione:

```
AllowTcpForwarding yes
GatewayPorts yes
TCPKeepAlive yes
```

Tras modificar el fichero, reinicia el servicio SSH:

```bash
sudo systemctl restart sshd
```

---

### Tabla resumen: opciones de sshd_config

| Opción | Qué hace | Nivel de exposición |
|---|---|---|
| `AllowTcpForwarding yes` | Permite que los clientes SSH creen túneles de reenvío de puertos (`-L`, `-R`, `-D`) | Necesario para todo lo anterior |
| `GatewayPorts yes` | Permite que los puertos reenviados sean accesibles desde **otras máquinas de la red**, no solo desde `localhost` del servidor | ⚠️ Amplía la superficie de exposición — actívalo solo si lo necesitas |
| `TCPKeepAlive yes` | Envía paquetes keep-alive a nivel TCP para evitar que el túnel se caiga por inactividad | Mejora estabilidad, sin impacto en seguridad |

> Si no necesitas que el puerto reenviado sea accesible desde otras máquinas de la red, dejar `GatewayPorts no` (valor por defecto) es más seguro.

---

### Verificación del túnel

Comprueba que el túnel está activo y escuchando:

```bash
ss -tunlp | grep 1080      # o el puerto que hayas usado
```

Comprueba la IP de salida a través del proxy (ejemplo con `curl` y SOCKS5):

```bash
curl --socks5 localhost:1080 https://ifconfig.me
```

Si la IP mostrada es la del servidor remoto, el túnel funciona correctamente.

---


#
http://www.hackingyseguridad.com/
#
