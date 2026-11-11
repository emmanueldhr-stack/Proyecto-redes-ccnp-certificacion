# Laboratorio 2: OSPF Single-area (area 0) - Enrutamiento dinamico
# 1. Objetivo
Migrar una topologia de tres routers (previamente configurada con rutas estaticas) a un entorno de enrutamiento dinamico utilizando OSPFv2. El objetivo es demostrar como los routers intercambian informaci[on de topologia automaticamente, como calculan la ruta mas corta (SPF) y como reaccionan ante fallos en los enlaces (convergencia).

## 2. Topologia y Direccionamiento

- **R1**: LAN '192.168.1.0/24' (Gi0/0), WAN '10.0.0.0/30' (Gi0/1)
- **R2**: WAN '10.0.0.0/30' (Gi0/0), WAN '10.0.0.4/30' (Gi0/1)
- **R3**: WAN '10.0.0.4/30' (Gi0/0), LAN '192.168.3.0/24' (Gi0/1)
- **PC1**: '192.168.1.10/24' (Gateway: '192.168.1.1')
- **PC2**: '192.168.3.10/24' (Gateway> '192.168.3.1')

## 3. Proceso de Migracion y Configuracion

### 3.1 Limpieza del entorno anterior

Antes de habilitar OSPF, es crucial eliminar las rutas estaticas para evitar conflictos (el router prefiere la ruta estatica con AD 1 sobre OSPF con AD 110).
'''text
R1(config)# no ip route 192.168.3.0 255.255.255.0 10.0.0.2
R1(config)# no ip route 10.0.0.4 255.255.255.252 10.0.0.2
R3(config)# no ip route 192.168.1.0 255.255.255.0 10.0.0.5
R3(config)# no ip route 10.0.0.0 255.255.255.252 10.0.0.5
R2(config)# no ip route 192.168.1.0 255.255.255.0 10.0.0.1
R2(config)# no ip route 192.168.3.0 255.255.255.0 10.0.0.6

### 3.2 Configuracion Base de OSPF

Se habilito el proceso de OSPF (ID 1) en los tres routers. En lugar de usar el comando network de forma global, se anunciaron las subredes especificas utilizando Wildcard Mask (la inversa de la mascara de subred)

- R1:
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0

(Nota: la wildcard 0.0.0.3 corresponde a la mascara 0/30 del enlace WAN. La wildcard 0.0.0.255 corresponde a la mascara 0/24 de la mascara LAN)

- R2:
router ospf 1
network 10.0.0.0 0.0.0.3 area 0
network 10.0.0.4 0.0.0.3 area 0

- R3:
router ospf 1
network 10.0.0.4 0.0.0.3 area 0
network 192.168.3.0 0.0.0.255 area 0

## 4. Verificacion del plano de control

### 4.1 Adyacencia de Vecinos (Neighbors)
Para que OSPF funcione, los routers deben "saludarse" y sincronizar sus bases de datos (LSDB). Se verifico con 'show ip ospf neghbor'
en R1:
Neighbor ID   Pri   State            Dead time       Address           Interface
10.0.0.5        1   FULL/BDR         00:00:32        10.0.0.2          GigabitEthernet0/1

- *Estado Full:* Indica que la sincronizacion de la base de datos se completo con exito. Ambos routers tienen exactamente el mismo mapa de la red.

- *BDR (Backup Designated Router):* Enlaces punto a punto (como este), OSPF elige un DR (Designated Router) y un BDR para evitar que todos los routers se hablen entre si. En este caso, R2 es el BDR.

### 4.2. Tabla de Enrutamiento (Routing Table)

Se ejecuto 'show ip route' en R1:

O          10.0.0.4/30 [110/2] via 10.0.0.2, 00:30:48, GigabitEthernet0/1
O       192.168.3.0/24 [110/3] via 10.0.0.2, 00:28:24, GigabitEthernet0/1

- **Letra O**: Confirma que la ruta fue aprendida dinamicamente por OSPf.
- **Distancia admnistrativa**: [110]: Es el Valor de confianza por defecto de OSPF.
- Metrica [2] y [3]: Es el costo (Cost) calculado por el algoritmo SPF (Dijktra). Se calcula basandose en el ancho de banda (por defecto, 10^8 / ancho_banda_en_bps). En enlace GigabitEthernet tiene un costo de 1 por salto. Para llegar a la LAN de R3, el paquete da 3 saltos (R1 -> R2 -> R3)

## 5. Failure Injection y Convergencia
Para probar la resilencia del protocolo, se apago la interfaz 'Gi0/0' en R2 y luego se volvio a ancender, capturando los log del sistema en R1.

### 5.1. Log de Caida (fallo)

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to down
%OSPF-5-ADJCHG: Process 1, Nbr 10.0.0.5 on GigabitEthernet0/1, from FULL to Down, Neighbor Down: Interface down or detached

- **Consecuencia inmediata**: La tabla de enrutamiento de R1 perdio las rutas marcadas con 'O'. El trafico hacia la red '192.168.3.0' se volvio inalcanzable.

### 5.2. Log de Recuperacion (convergencia)

Al encender la interfaz (no shutdown), OSPF inicio el proceso de reconexion:

- **Estado LOADING**: Los Routers estan intercambiando paquetes DBD (Database Description) para comparar sus mapas.
- **Estado FULL**: La convergencia ha terminado. Las rutas 'O' volvieron a la tabla de enrutamiento.

## 6. Leccion de ingenieria Senior

El enrutamiento estatico es rigido; requiere intervencion manual ante cualquier cambio. OSPF es un protocolo de estado de enlace que:

- 1. Descubre vecinos automaticamente.
- 2. Construye un mapa topologico identico (LSDB) en todos los routers del area.
- 3. Calcula la ruta mas corta (SPF) sin intervencion humana.
- 4. Reacciona a los fallos en segundos, eliminando rutas invalidas y recalculando caminos alternativos (convergencia).
