\# Routing Metrics (Métricas de Enrutamiento)



\## 📌 Descripción General



Una \*\*métrica\*\* es un valor numérico que usa un protocolo de enrutamiento para comparar diferentes caminos hacia la misma red de destino y elegir el mejor. A diferencia de la Administrative Distance (que compara fuentes distintas), la métrica compara caminos dentro de la \*\*misma fuente\*\* o el mismo protocolo.



Este documento forma parte del repositorio de fundamentos de enrutamiento. Aquí se explica qué es una métrica, cómo funciona en distintos protocolos, y cómo el router la usa en el proceso de selección de ruta.



\---



\## ❓ WHY (¿Por qué?)



Un protocolo puede aprender varias rutas hacia la misma red.



Necesita decidir cuál camino es mejor.



Sin una métrica, el protocolo no tendría forma de comparar dos caminos aprendidos por la misma fuente. La métrica proporciona un criterio objetivo para elegir el mejor.



\---



\## 📄 WHAT (¿Qué es?)



Una métrica es un valor utilizado para comparar caminos dentro del mismo protocolo.



Características principales:

\- Es \*\*interna al protocolo\*\*: cada protocolo define su propia métrica.

\- Solo se compara entre rutas de la \*\*misma fuente\*\* (mismo protocolo y misma AD).

\- \*\*Menor valor suele significar mejor ruta\*\* (aunque no siempre; depende del protocolo).

\- No se propaga entre protocolos distintos.

\- Si la métrica empata, el router puede usar \*\*ECMP\*\* para balancear el tráfico.



\---



\## 🧠 Conceptos Clave



\### RIP



\*\*Métrica = Saltos (Hop Count)\*\*



\- Cuenta el número de routers por los que pasa el paquete hasta el destino.

\- Máximo 15 saltos; 16 se considera infinito (red inalcanzable).

\- Simple pero limitada: no considera ancho de banda ni latencia.



\### OSPF



\*\*Métrica = Coste\*\*



\- Se calcula a partir del ancho de banda de la interfaz.

\- Fórmula por defecto en Cisco: `Cost = 100 Mbps / ancho de banda (Mbps)`.

\- Menor coste = mejor camino.

\- Se puede ajustar manualmente con `ip ospf cost`.



\### EIGRP



\*\*Métrica compuesta.\*\*



\- Combina varios factores:

&#x20; - Ancho de banda (bandwidth)

&#x20; - Retardo (delay)

&#x20; - Carga (load) — opcional

&#x20; - Confiabilidad (reliability) — opcional

\- Por defecto solo usa \*\*ancho de banda\*\* y \*\*retardo\*\*.

\- Usa el algoritmo DUAL para garantizar rutas sin bucles.



\### Otros protocolos



| Protocolo | Métrica principal |

|---|---|

| RIP | Saltos (Hop Count) |

| OSPF | Coste (basado en ancho de banda) |

| EIGRP | Métrica compuesta (BW + Delay por defecto) |

| IS-IS | Coste (configurable) |

| BGP | Atributos (AS-Path, Local Preference, MED, etc.) |



\---



\## ⚙️ HOW (¿Cómo funciona?)



\### Ejemplo práctico



El router aprende la misma red por OSPF desde dos caminos distintos:



```text

10.10.10.0/24 → OSPF Cost 20

10.10.10.0/24 → OSPF Cost 5


Proceso

Mismo prefijo: ambas rutas apuntan a la misma red.



Misma AD: ambas provienen de OSPF (AD = 110).



Métrica decide: se compara el coste de cada camino.



Resultado

text

Cost 5 gana.

La ruta con coste 5 es la mejor y se instala en la tabla de enrutamiento como ruta OSPF.



🧠 Proceso Mental del Router

text

IP destino

&#x20;     ↓

Longest Prefix Match

&#x20;     ↓

Administrative Distance

&#x20;     ↓

Métrica

&#x20;     ↓

Forwarding

Reglas clave:



Longest Prefix Match elige la red.



Administrative Distance elige la fuente.



Métrica elige el mejor camino dentro de la misma fuente.



ECMP entra en juego si todo empata.



📊 Ejemplo en la Tabla de Enrutamiento

El formato \[AD/Métrica] muestra la AD y la métrica de cada ruta.



text

O    10.10.10.0/24 \[110/5] via 192.168.1.2, 00:00:12, GigabitEthernet0/0

Interpretación:



O → ruta OSPF.



\[110/5] → AD = 110, métrica (coste) = 5.



via 192.168.1.2 → next-hop.



Si existiera otra ruta OSPF con coste 20 hacia la misma red, no se instalaría porque la de coste 5 es mejor.



🔍 Verificación

Comandos útiles en Cisco IOS:



bash

show ip route

show ip route 10.10.10.0

show ip ospf interface

show ip eigrp topology

show ip protocols


✅ Importancia y Casos de Uso

Selección del mejor camino: dentro de un protocolo, la métrica determina qué ruta se instala.



Ingeniería de tráfico: se puede modificar la métrica para forzar que un camino sea preferido.



Balanceo ECMP: si dos rutas tienen la misma métrica, se puede balancear el tráfico.



Convergencia: los protocolos usan la métrica para recalcular el mejor camino cuando cambia la topología.




📚 Lessons Learned (Lecciones Aprendidas)

La métrica compara caminos.



Solo participa cuando la fuente es la misma.



Menor coste suele significar mejor ruta.



La métrica es específica de cada protocolo.



La AD elige la fuente; la métrica elige el camino dentro de esa fuente.



Si la métrica empata, se puede activar ECMP.



Modificar la métrica es una herramienta válida de ingeniería de tráfico.




💡 Buenas Prácticas y Notas Adicionales

No confundir métrica con Administrative Distance:



AD → compara fuentes distintas.



Métrica → compara caminos dentro de la misma fuente.



Documentar cualquier ajuste manual de métrica (por ejemplo, ip ospf cost).



Verificar siempre con show ip route que la ruta esperada sea la instalada.



En OSPF, ajustar el auto-cost reference-bandwidth en redes de alta velocidad para evitar costes idénticos.



En EIGRP, recordar que la métrica compuesta usa K-values; por defecto K1=1 y K3=1.




🔗 Referencias

Documentación oficial de Cisco: Routing Metrics



CCNA 200-301 Official Cert Guide, Volumen 1 y 2 (Wendell Odom)



Apuntes personales del curso de redes.

