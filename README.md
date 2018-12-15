# ssh2proxy

Convierte equipo remoto al que conectamos por SSH en un Proxy a traves del cual navegaremos

ssh -f $1 -L 8080:localhost:22 -N

-L

suministra los parametros para el tunel 


-f

ejecuta la orden en segundo plano (background)


-N

indica que no se debe iniciar una terminal tras la conexion

Con Putty:

<img  style="float:left" alt="route logo" src="https://github.com/hackingyseguridad/ssh2proxy/blob/master/ssh2proxy.png"> 



# www.hackingyseguridad.com
