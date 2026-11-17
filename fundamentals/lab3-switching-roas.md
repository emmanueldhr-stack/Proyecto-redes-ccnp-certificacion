# Laboratorio 3: Switching, VLANs y ROAS (Router-on-a-Stick)

## Objetivo
Configurar una red de dos switches interconectados por un enlace Trunk, con dos VLANs (10 y 20) y un router realizando enrutamiento inter-VLAN mediante ROAS. Ademas, se configura una VLAN de gestion (99) para administrar los switches.

## Topologia y Direccionamiento
- **SW1**: Gi0/1 hacia R1 (Trunk), Gi0/2 hacia SW2 (Trunk). IP Gestion:192.168.99.11
- **SW2**: Gi0/1 hacia SW1 (Trunk). Fa0/1 en VLAN 10 (PC3), Fa0/2 en VLAN 20 (PC4). IP Gestion> 192.168.99.12
- **R1**: Subinterfaces Gi0/0.10 (192.168.10.1), Gi0/0.20 (192.168.20.1), Gi0/0.99 (192.168.99.1)
- **PCs**: PC1 (192.168.10.10), PC2 (192.168.20.10), PC3 (192.168.10.11), PC4 (192.168.20.11).

## Configuracion Clave

### Switches
- Creacion de las VLANs 10, 20 y 99.
- Configuracion de puertos access en SW2 (Fa0/1 y Fa0/2)
-Configuracion de enlaces Trunk en SW1 (Gi0/1 y Gi0/2) y SW2 (Gi0/2) con native VLAN 99.
- Asignacion de IP de Gestion a la SVI Vlan99 en ambos Switches.
- 'ip default-gateway 192.168.99.1' en ambos switches.

### Router (ROAS)
- 'encapsulation dot1Q [vlan]' en las subinterfaces.
- IPs de gateway para cada VLAN (incluyendo la 99).

## Verificacion y lecciones aprendidas
- **Ping PC1 a PC3**: Exitoso (misma VLAN, distinto switch)
- **Ping PC1 a PC4**: Exitoso (distinta VLAN, Distinto switch). El ROAS esta funcionando.
- **Ping R1 a PC1**: Exitoso. Confirma que el enlace Trunk entre el router y SW1 funciona.
- **Nota de Packet tracer:** Se observo una limitante del simulador al hacer ping desde PC a la SVI de un Switch remoto, pero la conectividad general y enrutamiento estan demostrados.
- **Leccion de ingeniero:** Los puertos access deben configurarse explicitamente en su VLAN correspondiente. Si se dejan en la VLAN 1 por defecto, el trafico no llegara al router aunque el trunk y el ROAS esten bien configurados. La VLAN de gestion (99) permite administrar los switches de forma remota.

