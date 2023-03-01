# Introducción
Spanning tree trabaja en **Capa 2**.

Spanning tree es un protocolo para evitar **bucles** en un contexto en el que se tienen 2 o más switches conectados entre sí mismos.

Esto ocurre cuando se busca crear redundancia en capa 2, si se tienen 2 switches conectados entre sí y un host lanza un broadcast, el broadcast se enviaría en bucle.

Spanning tree logra esto dando una  "dirección" a los broadcasts.

![[Screen+Shot+2017-11-27+at+10.56.png]]

# BPDU
Spanning tree usa Bridge Protocol Data Unit (BPDUs) que son mensajes que envían información de la topología.

# Prioridad y Root Bridge
La prioridad es un parámetro que se configura en los switches y se usa para elegir a un **Root Bridge**.

Un **Root Bridge** es el switch por el que pasan los broadcasts, para escogerlo, se toma el switch con el número de prioridad **más bajo**. 

**La prioridad debe ser múltiplo de 4096**

> La prioridad default y más alta es 32768

Si todos los switches tienen la misma prioridad, se elige el switch con **la dirección MAC más pequeña**.

## Puertos designados y Root Port

Los **puertos designados (D)** son los puertos del *Root Bridge* por los que pasan los broadcasts.

Los **Root port (R)** son los puertos en los switches adyacentes al *Root Bridge* tienen el camino más corto a los puertos designados.

# Bridge ID
El bridge ID determina cual puerto (que no sea puerto designado o root port) se tiene que bloquear para evitar el bucle.

Se calcula de la siguiente manera:

$$
BridgeID = Prioridad + MAC
$$

"Gana" el que tenga el bridge ID menor, es decir, el que tenga el bridge ID mayor se tiene que bloquear.

# Configuración
## Asignar prioridad a switch
La prioridad debe ser múltiplo de 4096
```
SW(config)#spanning-tree vlan <vlan> priority <priority no.>
```

Ejemplo
```
SW(config)#spanning-tree vlan 1 priority 28672
```

### Prioridad Primary y Secondary

**Primary**
Asigna la prioridad del Root bridge actual  menos 4096.
```
SW(config)#spanning-tree vlan 1 root primary
```

**Secondary**
Asigna la prioridad del Root bridge actual  menos 8192 ($2 * 4096$)
```
SW(config)#spanning-tree vlan 1 root secondary
```
