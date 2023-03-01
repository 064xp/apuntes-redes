Hot Standby Router Protocol es un protocolo propietario de cisco que nos permite tener redundancia en routers.

# Funcionamiento
Dos routers hacen un router virtual, ya que un router estará **active**  y el otro **standby**.

Si el router **active** no contesta los mensajes hello, se activará el router **standby**.

El router maestro se selecciona mediante su prioridad, la **prioridad por defecto** es **100**.

## Preempt Option
Si se cae el router maestro, y **no** se tiene habilitado el preempt option, entrará el router standby pero en el caso que regrese el maestro, no regresará a funcionar, sino que se quedará el router de respaldo funcionando.

# Comandos
## Habilitar HSRP en una interfaz

- Grupo <0-4095>
- IP del router virtual
```
R0(config)#int <int>
R0(config-if)#standby <group> ip <ip virtual>
```

## Determinar Prioridad
```
R0(config)#int <int>
R0(config-if)#standby <group> priority <priority>
```

## Habilitar Preempt Option
```
R0(config-if)#standby <group> preempt
```
## Establecer Versión HSRP
La versión 2 soporta IPv6.
```
R0(config-if)#standby version <version>
```

## Ver Configuration Standby
```
R0#show standby
```