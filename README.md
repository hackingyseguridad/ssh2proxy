## ssh2proxy

Convierte equipo remoto al que conectamos por SSH en un Proxy a traves del cual navegaremos

<img  style="float:left" alt="ssh tunel " src="https://github.com/hackingyseguridad/ssh2proxy/blob/master/tunel_ssh.png"> 


## tunel ssh desde consola linux

ssh -f $1 -L 8080:localhost:22 -N

-L

suministra los parametros para el tunel 


-f

ejecuta la orden en segundo plano (background)


-N

indica que no se debe iniciar una terminal tras la conexion

## tunel ssh con Putty y configuracion en el navegador:

<img  style="float:left" alt="Configuracion Proxy Socks en el navegador " src="https://github.com/hackingyseguridad/ssh2proxy/blob/master/ssh2proxy.png"> 


## Incluimos en servidor A:  /etc/ssh/sshd_config

AllowTcpForwarding yes

GatewayPorts yes

TCPKeepAlive yes

## Persistencia:

Para persistencia, el binario o script debe colocarse en rutas que permitan su ejecución continua, incluso después de reinicios:

### Rutas camufladas (Paso de persistencia)
/usr/lib/systemd/, /usr/local/bin/, /etc/cron.hourly/ (se confunde con archivos legítimos).

Nombres engañosos como rsyslogd, ntpupdate, kworker.

### Sistemas con servicios de persistencia

Systemd (crear un servicio oculto).

Cronjobs (tareas programadas).

rc.local (ejecución en inicio, en sistemas antiguos).

Profile.d o bashrc (para usuarios específicos).



http://www.hackingyseguridad.com
