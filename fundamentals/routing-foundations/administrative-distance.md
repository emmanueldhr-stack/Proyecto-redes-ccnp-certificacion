❓ WHY (¿Por qué?)
Elegir entre múltiples rutas hacia la misma red.

Cuando un router conoce una misma red por varias fuentes, necesita un criterio para decidir cuál ruta usar. La AD resuelve ese problema: es el mecanismo que permite comparar la "confiabilidad" de diferentes protocolos o métodos de enrutamiento.

Sin AD, el router no sabría si confiar más en una ruta estática, en OSPF, en EIGRP o en RIP. La AD establece un orden de preferencia claro.

📄 WHAT (¿Qué es?)
Mecanismo de confianza entre diferentes fuentes de rutas.

La AD es un valor entero que va de 0 a 255. Cuanto menor es el valor, más confiable es la fuente. Cuando existen varias rutas hacia la misma red, el router compara sus AD y elige la de menor valor.

Características principales:

Es local al router. No se propaga por la red.

Solo se compara entre rutas que apuntan a la misma red de destino.

Si dos rutas tienen la misma AD, se usa la métrica como criterio de desempate.

Si la métrica también empata, se puede hacer balanceo de carga (ECMP) si el protocolo y la configuración lo permiten.

Un valor de AD 255 significa que la ruta es considerada inválida y no se instala en la tabla de enrutamiento.

🧠 Conceptos Clave
Administrative Distance (AD)
Valor de confianza asignado a una fuente de rutas. Menor AD = mayor prioridad.

Fuente de ruta
Origen de la información de enrutamiento. Puede ser:

Directamente conectada

Ruta estática

OSPF

EIGRP

RIP

BGP

etc.

Longest Prefix Match (LPM)
Mecanismo que elige la ruta más específica (la máscara más larga) para una dirección IP de destino. LPM elige la red.

Métrica
Valor que usa un protocolo de enrutamiento para elegir el mejor camino dentro de la misma fuente. Por ejemplo, OSPF usa costo, EIGRP usa una métrica compuesta, RIP usa saltos. La métrica elige el mejor camino dentro de una misma fuente.

Routing Table / RIB
Tabla de enrutamiento donde se instalan las mejores rutas. El router consulta esta tabla para reenviar paquetes.

Floating Static Route
Ruta estática con una AD mayor que la del protocolo dinámico principal. Se usa como respaldo: solo se activa si la ruta principal desaparece.

Equal Cost Multipath (ECMP)
Cuando dos o más rutas hacia la misma red tienen la misma AD y la misma métrica, el router puede instalar varias y balancear el tráfico entre ellas.

📊 Common Values (Valores Comunes de AD)
Fuente de ruta	AD por defecto
Directamente conectada (Connected)	0
Ruta estática (Static)	1
EIGRP summary route	5
External BGP (eBGP)	20
EIGRP interno	90
OSPF	110
RIP	120
EIGRP externo	170
Internal BGP (iBGP)	200
Desconocida / inválida	255
Valores rápidos:

Connected = 0

Static = 1

OSPF = 110

RIP = 120

Nota: Estos valores son los predeterminados en equipos Cisco. Pueden variar ligeramente según el fabricante o la versión.

⚖️ Proceso de Selección de Ruta
Cuando un router debe reenviar un paquete, sigue este orden lógico:

Longest Prefix Match: busca en la tabla de enrutamiento la ruta más específica que coincida con la IP de destino. Aquí se elige la red.

Administrative Distance: si hay varias rutas hacia esa misma red, elige la fuente más confiable (menor AD).

Métrica: si la AD empata, elige el mejor camino según la métrica del protocolo.

ECMP: si la métrica también empata, puede instalar varias rutas y balancear.

Importante: La AD no compite entre redes distintas. Primero se aplica Longest Prefix Match. Por ejemplo, una ruta /32 siempre gana sobre una /24 para una IP que pertenece a esa /32, sin importar la AD.

📦 Packet Flow / Route Selection Flow
text
IP destino
      ↓
Longest Prefix Match
(elije la red)
      ↓
¿Varias fuentes para la misma red?
      ↓
Administrative Distance
(elije la fuente)
      ↓
Métrica
(elije el mejor camino dentro de la misma fuente)
      ↓
Forwarding
⚙️ Configuración y Ejemplos
Ejemplo 1: Ruta estática con AD por defecto
bash
R1(config)# ip route 192.168.2.0 255.255.255.0 192.168.1.2
La AD por defecto de una ruta estática es 1. En show ip route se vería:

text
S    192.168.2.0/24 [1/0] via 192.168.1.2
El formato [AD/Métrica] indica que la AD es 1 y la métrica es 0.

Ejemplo 2: Ruta estática con AD personalizada
bash
R1(config)# ip route 192.168.2.0 255.255.255.0 192.168.1.2 200
Aquí la AD se establece en 200. Si existe una ruta OSPF (AD 110) hacia la misma red, OSPF será preferida. Esta ruta estática solo se usaría si OSPF desaparece.

Ejemplo 3: Floating Static Route (ruta de respaldo)
bash
R1(config)# ip route 0.0.0.0 0.0.0.0 192.168.1.2 200
Si el router aprende una ruta por defecto por OSPF (AD 110), OSPF gana. Si OSPF falla, la ruta estática flotante (AD 200) se instala como respaldo.

🔍 Verificación
Comandos útiles en Cisco IOS:

bash
show ip route
show ip route 192.168.2.0
show ip route static
show ip protocols
Ejemplo de salida show ip route:

text
Gateway of last resort is 192.168.1.2 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 192.168.1.2
      192.168.1.0/24 is directly connected, GigabitEthernet0/0
O     192.168.2.0/24 [110/2] via 192.168.1.2, 00:00:12, GigabitEthernet0/0
S     192.168.3.0/24 [200/0] via 192.168.1.2
Interpretación:

S* → ruta estática por defecto.

O → ruta OSPF, AD 110.

S con [200/0] → ruta estática con AD modificada a 200.

✅ Importancia y Casos de Uso
Selección entre protocolos: permite que un router prefiera OSPF sobre RIP, o una ruta estática sobre OSPF.

Rutas de respaldo: las floating static routes usan AD alta para activarse solo si la ruta principal falla.

Ingeniería de tráfico: se puede modificar la AD para forzar que una fuente sea preferida sobre otra.

Migraciones: durante una migración de protocolo, se puede ajustar la AD para controlar qué protocolo se usa primero.

Diseño de red: ayuda a mantener un orden de confianza coherente en toda la topología.

📚 Lessons Learned (Lecciones Aprendidas)
Longest Prefix Match: elige la red.

Administrative Distance: elige la fuente.

La AD solo se compara entre rutas que apuntan a la misma red.

Menor AD = mayor confiabilidad.

La AD es local al router y no se propaga.

Si la AD empata, se usa la métrica.

Si la métrica empata, se puede usar ECMP.

Una AD de 255 hace que la ruta no se instale.

Las rutas directamente conectadas tienen AD 0 y siempre son las más confiables.

Las rutas estáticas tienen AD 1 por defecto.

Las floating static routes son una aplicación práctica muy útil de la AD.

💡 Buenas Prácticas y Notas Adicionales
No modificar la AD sin una razón justificada y documentada.

Usar floating static routes para respaldo en lugar de depender solo de protocolos dinámicos.

Recordar que la AD no reemplaza al Longest Prefix Match; primero se elige la red más específica.

Verificar siempre con show ip route que la ruta esperada sea la que se instaló.

Documentar cualquier cambio de AD en la configuración del router.

En entornos multivendor, verificar los valores de AD equivalentes, ya que pueden variar.

La AD es una herramienta de decisión local; no afecta a otros routers.

🔗 Referencias
Documentación oficial de Cisco: Administrative Distance

CCNA 200-301 Official Cert Guide, Volumen 1 y 2 (Wendell Odom)

Apuntes personales del curso de redes.
