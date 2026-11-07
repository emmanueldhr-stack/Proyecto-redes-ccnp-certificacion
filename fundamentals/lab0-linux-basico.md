# Laboratorio 0: Fundamentos de Linux, Redes y Enrutamiento

## Objetivo:

Comprender el direccionamiento IP en Linux, el enrutamiento estatico/dinamico y la captura de trafico a nivel de sistema operativo.

## Evidencia: Captura tcp dump

Al hacer ping desde el host (192.168.0.103) a la VM (192.168.0.104), se capturo el trafico ICMP bidireccional:
- IP 192.168.0.103 > 192.168.0.104: ICMP echo request
- IP 192.168.0.104 > 192.168.0.103 ICMP echo reply

## Failure Injection: Borrado de la ruta por defecto
- Al ajustar 'ip route del default', el sistema perdio la capacidad de enrutar paquetes a redes externas, devolviendo 'Network is unreachable'
- **Conclusion de ingenieria:** Tener una IP no garantiza la conectividad. La tabla de enrutamiento es la que le indica al sistema operativo por donde enviar los paquetes. Sin una ruta por defecto, el trafico hacia Internet se descarta.

##Configuracion Estatica
Se edito el archivo de Netplan ('/etc/netplan/50-cloud-init.yaml') para deshabilitar DHCP y asignar la IP estatica '192.168.0.200/24', configurando explicitamente el gateway '192.168.0.1' y los DNS de Google y Cloudflare. LA conectividad fue verificada con exito
