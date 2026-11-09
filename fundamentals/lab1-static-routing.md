# Laboratorio 1: Enrutamiento Estatico Completo

## Topologia

- **R1**: LAN 192.168.1.0/24 (Gi0/0), WAN 10.0.0.0/30 (Gi0/1)
- **R2**: WAN 10.0.0.0/30 (Gi0/0), WAN 10.0.0.4/30 (Gi0/1)
- **R3**: WAN 10.0.0.4/30 (Gi0/0), LAN 192.168.3.0/24 (Gi0/1)

## Configuracion de Rutas Estaticas

- **R1**: 'ip route 192.168.3.0 255.255.255.0 10.0.0.2' y 'ip route 10.0.0.4 255.255.255.252 10.0.0.2'
- **R3**: 'ip route 192.168.1.0 255.255.255.0 10.0.0.5' y 'ip route 10.0.0.0 255.255.255.252 10.0.0.5'
- **R2**: 'ip route 192.168.1.0 255.255.255.0 10.0.0.1' y 'ip route 192.168.3.0 255.255.255.0 10.0.0.6'

## Verificacion

- **Ping PC1 a PC2**: Exitoso (4/4)
- **Tracert PC1 a PC2**: Muestra el camino PC1 -> R1 -> R2 -> R3 -> PC2.
- **Ping entre Routers**: Todos los routers pueden alcanzar las interfaces LAN y WAN de los demas tras aplicar las rutas especificas de los demas para las subredes /30

## Leccion de ingenieria
Para que un ping funcione de extremo a extremo, **la ruta de la ida y la ruta de vuelta deben existir en las tablas de enrutamiento**. El ping de un router a otro requiere que ambos routers tengan rutas hacia las subredes WAN que los interconectan.
