Distancia Administrativa
**Interno:** 200
**Externo:** 20
# Introducción
Border Gateway Protocol

Es el protocolo de routing que hace que el internet funcione.

Es el protocolo utilizado por los provedores de servicio (ISPs). Es considerado complejo y difícil de configurar, pero tiene un énfasis en seguridad y escalabilidad que lo hace esencial.

Cuando se usa BGP dentro de una única red (Sistema autónomo) es interno o **iBGP**.

Cuando se usa BGP para conectar varios sistemas autónomos se considera BGP externo **eBGP**.

# Estados de BGP
1. **Idle** 
	1. Estado inicial de BGP, el dispositivo rechaza todas las conexiones con los vecinos y **solo va a iniciar una conexión TCP con su par BGP**.
2. **Connect**
	1. El equipo espera establecer una conexión TCP con el par BGP y comienza el **Timer Connect Retry**.
	2. Si se establece la conexión TCP, se envía un mensaje Open al equipo par y se cambia al estado **OpenSent**.
	3. Si falla, el equipo pasa el estado **ACTIVE**.
	4. Si se acaba el tiempo del timer, se intentará establecer conexión con otro par.
3. **Active**
	1. El equipo está intentando establecer una conexión TCP con el par.
	2. Si se establece la conexión se establece, se envía un mensaje **Open** al par y termina el **Timer Connect Retry**. Se cambia al estado **OpenSent**.
	3. Si no, se permanece en **ACTIVE**.
	4. Si se termina el timer y no recibe una respuesta, se regresa a **CONNECT**.
4. **OpenSent**
	1. Espera el mensaje **Open** del par y valida el nombre de AS, versión y contraseña de autenticación del mensaje recibido.
	2. Si el mensaje es valido, mande un **keepalive** y cambia al estado **OpenConfirm**.
	3. Si no, envía un mensaje de notificación al par y regresa a **Idle**.
5. **OpenConfirm**
	1. Espera un mensaje **keepalive** o notificación del par.
	2. Si recibe el **keepalive**, se cambia al estado **Establish**.
	3. Si recibe una notificación, se cambia a **Idle**.
6. **Establish**
	1. Intercambia mensaje **Update** (actualizaciones), **Keepalive**(detección de estado), **Route-refresh**(Actualizar rutas) y notificaciones.
	2. Si se recibe un Update o Keepalive es considerado que el par está trabajando correctamente.
	3. Si se recibe un mensaje inválido, se envía un mensaje de notificación y se regresa al estado **Idle**.
	4. Si se recibe un **Route-Refresh**, no cambia el estado.
	5. Si se recibe un mensaje de notificación, se regresa a **Idle**.
	6. Si se recibe una notificación para terminar, se termina la conexión TCP y se regresa a **Idle**.
  
## Flujo de los Estado BGP
![[bgp+states-235506452.png]]

# Comandos

Ver estadísticas generales BGP
```
Router# show bgp all summary
```