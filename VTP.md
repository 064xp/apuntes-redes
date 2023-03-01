# Introducción
VTP es un protocolo que cumple la función de propagar la configuración de VLANs por toda una red y mantenerla sincronizada en todos los switches.

> VTP es un protocolo propietario de Cisco

El switch **servidor** es el que tiene todas las configuraciones, las cuales serán sincronizadas a los clientes.


# Configuración
**Definir dominio VTP**
```
switch(config)#vtp domain <dominio>
```

**Establecer modo de operación VTP**
```
switch(config)#vtp mode { server | transparent | client}  
```
- **Server:** Contiene la configuración
- **Client:** Recibe las configuraciones del server
- **Transparent:** No aplica las configuraciones, pero si reenvía las configuraciones a switches adyacentes.

**Establecer versión de VTP**
```
switch(config)#vtp version {1 | 2 | 3}
```

**Establecer contraseña de VTP**
```
switch(config)#vtp password password
```

# Troubleshooting
**Información de estado de VTP**
```
Switch(config)#show vtp status
```

**Ver contadores de mensajes de anuncio**
```
Switch(config)# show vtp counters
```

**Ver interfaces de switch**
```
Switch(config)# show interfaces switchport
```
