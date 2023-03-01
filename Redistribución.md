La redistribución es la publicación de rutas aprendidas por algún protocolo de ruteo dinámico hacia otros protocolos de ruteo dinámico.

- Se redistribuyen únicamente las rutas que están en la tabla de ruteo.

# Métrica
Cada protocolo tiene su métrica única, esto hace que al redistribuir, la métrica se establecerá en términos de la métrica del protocolo al que se traduce.

La métrica con la que un protocolo recibe rutas aprendidas por otro, se le denomina **métrica raíz**. 

Métrica raíz por defecto utilizada por cada protocolo:
- **RIP:** Infinito
- **EIGRP:** Infinito
- **OSPF:** 20

Se puede modificar esta métrica default con 
```
default metric
```
# En EIGRP
La métrica por defecto es **infinito**.

Si no especificamos una métrica, las rutas redistribuidas no aparecerán en la tabla de ruteo del dispositivo vecino.

También es importante definir:

- Bandwidth
- Delay
- Reliability
- Load
- MTU
```
Router(config)#router eigrp 100
Router(config-router)#redistribute static
Router(config-router)#redistribute rip
Router(config-router)#default-metric 10000 100 255 1 1500
```

# En OSPF
La métrica por defecto es 20 (no es necesario cambiar la métrica default).

Se debe especificar:
- Sistema Autónomo
- Métrica default OSPF 
- Considerar subredes, tipo de métrica
```
Router(config)#router ospf 1
Router(config-router)#redistribute static metric 200 subnets
Router(config-router)#redistribute eigrp 100 metric 500 subnets
```
