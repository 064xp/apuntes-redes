Protocolo de **[[Ruteo Dinámico#Métricas|link-state]]**.
Métrica es **path cost**, deriva mucho ancho de banda.

**Dirección Multicast:** 224.0.0.5 (DR) y 224.0.0.6 (DROTHER)

Mantiene 3 tablas:
- **Tabla de vecinos:** Información de los vecinos
- **Tabla topológica (Link state database):** Información del link state
- **Tabla de ruteo:** Información de las mejores rutas a un destino

Usa el concepto de ***areas***, en donde el area 0 es el backbone.

## Paquetes OSPF
1. Hello
	1. Descubre vecinos y adyacencias entre ellos.
2. Database Description (DBD)
	1. Contiene una lista abreviada de la LSDB, que los routers receptores comparan contra sus LSDBs.
3. Link-State Request (LSR)
	1. Los router receptores puede solicitar más información de cualquier entrada de la LSDB. 
	2. Todos los routers de estado de enlace de una area deben tener la misma LSDB
4. Link-State Update (LSU)
	1. Responden a los LSR y anuncian nueva información
	2. Una LSU contiene una o más LSAs
5. Link-State Acknowledgement (LSAck)
	1. Se envía para reconocer que se recibió un LSA.
	2. El cuerpo de este paquete está vacío.

## Bases de Datos OSPF
1. **Base de Datos de Adyacencia:** Crea la tabla de vecinos.
2. **Base de datos de enlace:** Crea la tabla de la topología.
3. **Base de datos de reenvío:** Crea la tabla de ruteo.

## Algoritmo
OSPF utiliza el algoritmo Shortest Path First de Djikstra para encontrar la ruta mas corta de cada nodo a todos los demás. 

En base al arbol final de SPF, crea la tabla de ruteo.


## Funcionamiento de estado de enlace
1. **Establecimiento de adyacencias de vecinos** 
	1. Los routers OSPF comienzan a enviar y recibir paquetes hello, cuando reciben estos mensajes, los routers intentan crear una adyacencia con los routers que enviaron esos paquetes.
2. **Intercambio de anuncios de estado de enlace**
	1.  Después de establecer adyacencias, los routers intercambian LSA (anuncios de estado de enlace), los cuales contienen el estado y costo de cada enlace.
		1. Se saturan a los routers adyacentes hasta que todos tengan todos los LSAs.
3. Crear la base de datos de estado de vínculo
	1. En base a los LSA recibidos, se crea la tabla de topología (LSDB) en cada router.
4. Ejecución del algoritmo SPF
	1. En base a la LSDB, se ejecuta el algoritmo SPF, el cual crea el arbol SPF en el cual se encuentran los caminos mas cortas entre nodos.
5. Elija la mejor ruta
	1. En base al arbol SPF, se elijen las mejores rutas y se insertan en la tabla de ruteo.


## Configuración Básica OSPF

Escoger un process ID (entre 1 y 65,535)
```
Router(config)# router ospf 100
```
Especificar: 
- Id de red
- Wildcard
- area
```
Router(config-router)# network 172.16.16.0 0.0.0.255 area 0
```
## Intervalos "Hello" y "Dead"

**Configurar intervalos Hello y Dead**
Los intervalos se configuran sobre las interfaces.
```
router(config-if)# ip ospf hello-interval <segundos>
router(config-if)# ip ospf dead-interval <segundos>
```

Un paquete **hello** contiene información de las redes conectadas al router.

El intervalo **dead** determina si un vecino está desactivado si no se recibe un paquete "hello" en dicho intervalo.

Por defecto el intervalo muerto es 4 veces mayor al intervalo hello. 
Por defecto:
- **Intervalo Hello:** 10 segundos
- **Intervalo Dead:** 40 segundos

## DR y BDR
El DR es el encargado de mantener la base de datos de LSAs (Link State Advertisement) y reenviarlos.

### Router ID
El router ID es un identificador de 32 bits que tiene cada router. Se escribe en formato decimal punteado.

**Configurar router ID OSPF**
```
Router(config)# router ospf 1
Router(config-router)# router-id 1.1.1.1
```

### Prioridad
Número entre 0 y 255, se escoge el router con prioridad más alta.

Este se aplica sobre la interfaz específica, de tal manera que podemos tener diferentes prioridades en areas diferentes si es que el router se encuentra entre 2 o más areas.

#### Nota
Si la prioridad es 0, el router no puede ser elegido como DR o BDR.

```
Router(config)#interface <int>
Router(config-if)#ip ospf priority <0-255>
```

### Proceso Elección DR/BDR
Para elegir al DR y BDR se consideran lo siguientes criterios, en este orden secuencial.

1. **Prioridad** 
2. **Router ID:** Si la prioridad es la misma, se escogen los routers con los router IDs más altos.
3. **Dirección IP:** Si el router ID es el mismo, escoge las direcciones IPs más grandes.

#### Escoger nuevo DR y BDR

Ya que se escoge el DR y BDR, aunque se conecte un nuevo router con mayor prioridad, no se hará el proceso de elección de DR y BDR nuevamente.

Para realizar esto, podemos, por ejemplo, eliminar las adyacencias con

```
Router# clear ip ospf process
```


El router **DR** es el encargado de armar la **LSDB** y después la envía a los demás. El router **BRR** es el respaldo.
Se especifica quien es el DR mediante el Router ID.

#### Nota
En caso de tener todos el mismo router ID, se basa en la dirección IP más grande para escoger el DR y el segundo más grande para el BDR.

### Verificación de las Adyacencias DR/BDR
```
show ip ospf neighbor
```

### Necesidad de DR
En redes multiacceso se necesita un DR para evitar inundación de LSAs.

adyacencias = n(n -1) / 2
n = número de routers

- **FULL/DROTHER:** Router DR o BDR adyacencia con un router DROTHER
- **FULL/DR:** Router BDR con adyacencia al DR.
- **FULL/BDR:** Router DR con adyacencia BDR
- **DROTHER/2-WAY:** Router DROTHER conectado a otro router DROTHER.

## Estados de Adyacencia 
- **down:** 
	- El router envía un Hello por sus interfaces habilitadas con OSPF (mensaje multicast 224.0.0.5).
- **init:** 
	- Otro router que recibe el hello del router 1 le contesta con otro hello (mensaje unicast). Agrega a R1 a su tabla de vecinos.
- **Two Way:** 
	- R1 recibe el hello de R2, como su router id está en la tabla de vecinos de R2, entran al estado two way.
- **ExStart:** 
	- En redes punto a punto, los dos routers deciden qué router iniciará el intercambio de paquetes DBD y deciden sobre el número de secuencia de paquetes DBD inicial.​
- **Estado de Intercambio:**
	- Los routers intercambian paquetes DBD.
	- Si se necesita info adicional, se envían LSRs y se pasa a estado LOADING, si no, se va a FULL.
- **Estado Loading:** 
	- Se usan LSR y LSUs para obtener información adicional.
	- Se procesan las rutas con SPF
- **Full:** Las LSDB de los routers estan completamente sincronizadas.

Si una adyacencia no es FULL, significa que hay un problema en la creación de adyacencias.

## Recuperación Falla DR
Cuando el DR falla, el BDR se entera ya que el DR deja de enviar mensajes Hello en el periodo Dead.

Cuando se detecta baja del DR, el BDR se promueve a DR y se busca un nuevo BDR.

--- 

## OSPF de Área Única y OSPF de Multiarea
- **Área Única:** Todos los routers están en la misma área, por buenas prácticas es la área 0.
- **Multiarea:** Se implementa en varias áreas de manera jerárquica. Todas las áreas se deben conectarse al área troncal (area 0).
	- Los routers que interconectan las áreas se llaman router fronterizos de área (ABR).

La utilidad de tener múltiples áreas es mejorar el rendimiento en redes más grandes, se puede seccionar la red para que las bases de datos OSPF sean más pequeñas.

Operaciones intensivas de recursos se ejecutan solo cuando hay un cambio dentro de la misma área, por ejemplo, cuando hay un cambio en la topología, se ejecuta el algoritmos SPF nuevamente (lo cual es intensivo en el CPU).

### Ventajas de una topología Multiarea
- **Más pequeñas**
- **Sobrecarga de actualizaciones de estado de enlace reducida**
- **Menor frecuencia de cálculos de SPF**

### Tipos de Routers OSPF

- **Backbone Router** Pertenece al area 0
- **Internal Router** Todas sus interfaces pertenecen a la misma área
- **Area Border Router (ABR)** Contiene más de dos áreas diferentes
- **Autonomous System Border Router (ASBR)** Conecta otros protocolos.

## Tipos de Redes OSPF
### Red OSPF Multiacceso
Un router controla la distribución de los LSA. Este router (DR) deberá ser determinado por el administrador de la red.

Los routers se pueden conectar **a un mismo switch** para formar una red multiacceso.

El DR usa la dirección multicast 224.0.0.5 para enviar LSA.

Tambien se tiene a un BDR de respaldo, si deja de enviar actualizaciones el DR, entrará el BDR a tomar su lugar.

Todos los demás router se vuelve DROTHER (routers que no son DR ni BDR) y usan la dirección multicast 224.0.0.6.

Los DROTHER ahora envían sus LSAs únicamente al DR y BDR, el DR se encarga de reenviar estos LSAs al resto de los routers OSPF. Esto se hace para evitar latencia y saturación de la red.

![[Demo-Role-DR-3107241887.gif]]


## OSPF Multiarea
En redes más grandes, se deben implementar múltiples áreas.

Requiere un diseño jerárquico. El área principal se denomina "red troncal" o "backbone" (area 0).

### Por qué implementar topologías multiarea
- Tablas de routing extensas
- LSDB muy grandes
- Cálculos frecuentes del algoritmo SPF
- Routing escalable pare hacer redes más escalables y eficaces.

Básicamente, al tener una sola área con una red grande, se hacen uso de muchos recursos de red (inundación de LSAs), de memoria (LSDBs Grandes) y procesamiento (Cálculo de SPF frecuente).

### Ventajas OSPF Multiarea
- Tablas de routing más pequeñas
- Menor sobrecarga de actualización de estado de enlace (LSA)
- Menor frecuencia de cálculos de SPF

![[Ventajas-OSPF-multiárea-660303553.png]]

### Recomendaciones de CISCO
1. Un área no debe tener mas de 50 routers
2. Un router no debe estar en más de tres áreas
3. Ningún router debe tener más de  60 vecinos

## Tipos de routers OSPF

1. **Router Interno**
	1. Todas sus interfaces en la misma área. Comparten LSDB con todos los routers de su área.
2. **Router de Respaldo**
	1. Router que se encuentra en la backbone pero no es ABR.
	2. Sirven para mantener copias de la LSDB.
3. **Route de área perimetral (ABR)**
	1. Sus interfaces conectan a diferentes áreas.
4. **Router limítrofe del sistema autónomo (ASBR)**
	1. Tiene al menos una interfaz conectada a una internetwork externa (otro sistema autónomo o que no es OSPF).
	2. Importa información de una red NO OSPF a una OSPF o viceversa (**Redistribución de rutas**).

 ## Tipos de LSA 
 1. **Tipo 1**
	 1. Inundan el área que los origina
	 2. Contiene la lista de interfaces con conexión directa, tipos de enlace y estado de enlace.
 2. **Tipo 2**
	 1. Existe en redes de diversos accesos y redes sin diversos accesos ni difusión.
	 2. Contiene
		 1. ID de router
		 2. Dirección IP y ID del DR
 3. **Tipo 3**
	 1. Se usa para anunciar redes de otras áreas
	 2. Lo envía un ABR
 4. **Tipo 4**
	 1. Se usa en conjunto con el tipo 5 para identificar un ASB y anunciar redes externas.
	 2. El ABR genera un LSA de resumen tipo 4 cuando existe un ASBR en la red. Con este, se le asigna una ruta al ASBR.
 5. **Tipo 5**
	 1. Anuncian rutas a redes que se encuentran afuera del sistema autónomo de OSPF.