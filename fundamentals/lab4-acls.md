# Laboratorio 4: ACLs Extendidas (Filtrado de trafico inter-VLAN)

## Objetivo
Implementar una lista de Control de Acceso (ACL) extendida para denegar la comunicacion entre un host especifico (PC1 en VLAN 10) y otro host especifico (PC4 en VLAN 20), permitiendo el resto del trafico.

## Topologia y Direccionamiento

- **PC1**: 192.168.10.10 (VLAN 10) - Origen del trafico a filtrar.
- **PC2**: 192.168.20.10 (VLAN 20) - Debe ser alcanzable desde PC1.
- **PC4**: 192.168.20.11 (VLAN 20) - Debe ser Bloqueado desde PC1.
- **R1**: Subinterfaz Gi0/0.10 (Gateway 192.168.10.1) y Gi0/0.20 (Gateway 192.168.20.1).

## Configuracion Clave
- **ACL 100 (Extendida)**:
	-'access-list 100 deny ip host 192.168.10.10 host 192.168.20.11' (bloqueo especifico).
	-'access-list 100 permit ip any any' (Permite todo lo demas, crucial para no bloquear la red).
- **Aplicacion**: 'interface Gigabitethernet0/0.10' -> 'ip access-group 100 in' (Se aplica cerca del origen).

## Verificacion y lecciones aprendidas

- **Ping PC1 a PC4**: Falla con 'Destination host unreacheble'. El Router R1 descarta el paquete y notifica al origen.
- **Ping PC1 a PC2**: Funciona correctamente, demostrando que el router sigue respondiendo a su propia IP.
- **Ping PC1 a Gateway**: Funciona, confirmando que el router sigue respondiendo a su propia IP.
- **Lecciones de ingenieria**: El orden de las reglas en una ACL es fundamental. Si Hubieramos puesto el 'permit ip any any' antes del 'deny', el trafico bloqueado habria pasado porque la primera regla que coincide es la que se aplica. Ademas, el uso de 'host' es una forma deficiente de especificar una unica IP (equivale a una mas cara wildcard 0.0.0.0).

