Protocolo para asignar direcciones IP 

## Tipos de mensajes

- **Discover**
	- Broadcast que envía el host al conectarse a la red en búsqueda de un servidor DHCP
- **Offer**
	- El servidor DHCP le ofrece una IP de su pool de direcciones IP
- **Request**
	- El host le solicita dicha dirección IP al servidor DHCP
- **Ack**
		- El servidor DHCP reconoce que el host ahora tiene esa dirección IP y la quita de su pool de direcciones

# Configuración DHCP

1. Crear un pool de direcciones
2. Especificar red 
3. Especificar IP del default gateway
4. Especificar servidor DNS
```
server(config)#ip dhcp pool <pool_name>
server(dhcp-config)#network <id_red> <mask>
server(dhcp-config)#default-router <default_gateway>
server(dhcp-config)#dns-server <ip>
```

## Habilitar DHCP en un cliente
Recibir una IP para una interfaz en un router cliente
```
client(config)#interface <interface>
client(config-if)#ip address dhcp
client(config-if)#no shut
```

## Checar clientes DHCP
```
DHCP#show ip dhcp binding
```
## Excluir direcciones IP
```
DHCP(config)#ip dhcp excluded-address <ip inicio> <ip fin>
```

# DHCP Relay
Se utiliza cuando se requiere hacer DHCP en redes diferentes (ya que los broadcasts no pasan por routers).

Se define un pool para las subredes que necesiten servicio
```
DHCP(config)#ip dhcp pool <pool>
DHCP(dhcp-config)#network <id subred>
```

Se debe hacer ruteo estático hacia la subred en cuestión
```
DHCP(config)#ip route <subred> <mask> <ip>
```

En el router intermedio, debemos decirle por cual interfaz va a recibir los mensajes DHCP.

```
Router(config)#interface <interface>
Router(config)#ip helper-address <ip DHCP server>
```