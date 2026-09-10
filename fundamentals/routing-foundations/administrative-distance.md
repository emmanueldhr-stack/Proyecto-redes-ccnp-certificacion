\# Administrative Distance (Distancia Administrativa)



\## 📌 Descripción General



La \*\*Administrative Distance (AD)\*\* es un valor numérico que indica el nivel de confianza que tiene un router sobre una fuente de rutas. Se utiliza cuando un router aprende la \*\*misma red\*\* desde múltiples fuentes (por ejemplo, una ruta estática, OSPF, EIGRP, RIP, BGP, etc.). La fuente con menor AD es considerada más confiable y su ruta es la que se instala en la tabla de enrutamiento.



Este documento forma parte de un repositorio de aprendizaje de redes. Aquí se explica qué es la AD, cómo se usa, su relación con Longest Prefix Match y la métrica, y ejemplos prácticos de configuración y verificación.



\---



\## ❓ WHY (¿Por qué?)



Elegir entre múltiples rutas hacia la misma red.



Cuando un router conoce una misma red por varias fuentes, necesita un criterio para decidir cuál ruta usar. La AD resuelve ese problema: es el mecanismo que permite comparar la "confiabilidad" de diferentes protocolos o métodos de enrutamiento.



Sin AD, el router no sabría si confiar más en una ruta estática, en OSPF, en EIGRP o en RIP. La AD establece un orden de preferencia claro.



\---



\## 📄 WHAT (¿Qué es?)



Mecanismo de confianza entre diferentes fuentes de rutas.



La AD es un valor entero que va de \*\*0 a 255\*\*. Cuanto menor es el valor, más confiable es la fuente. Cuando existen varias rutas hacia la misma red, el router compara sus AD y elige la de menor valor.



Características principales:

\- Es \*\*local al router\*\*. No se propaga por la red.

\- Solo se compara entre rutas que apuntan a la \*\*misma red de destino\*\*.

\- Si dos rutas tienen la misma AD, se usa la \*\*métrica\*\* como criterio de desempate.

\- Si la métrica también empata, se puede hacer balanceo de carga (ECMP) si el protocolo y la configuración lo permiten.

\- Un valor de AD \*\*255\*\* significa que la ruta es considerada inválida y no se instala en la tabla de enrutamiento.



\---



\## 🧠 Conceptos Clave



\### Administrative Distance (AD)

Valor de confianza asignado a una fuente de rutas. Menor AD = mayor prioridad.



\### Fuente de ruta

Origen de la información de enrutamiento. Puede ser:

\- Directamente conectada

\- Ruta estática

\- OSPF

\- EIGRP

\- RIP

\- BGP

\- etc.



\### Longest Prefix Match (LPM)

Mecanismo que elige la ruta más específica (la máscara más larga) para una dirección IP de destino. \*\*LPM elige la red.\*\*



\### Métrica

Valor que usa un protocolo de enrutamiento para elegir el mejor camino dentro de la misma fuente. Por ejemplo, OSPF usa costo, EIGRP usa una métrica compuesta, RIP usa saltos. \*\*La métrica elige el mejor camino dentro de una misma fuente.\*\*



\### Routing Table / RIB

Tabla de enrutamiento donde se instalan las mejores rutas. El router consulta esta tabla para reenviar paquetes.



\### Floating Static Route

Ruta estática con una AD mayor que la del protocolo dinámico principal. Se usa como respaldo: solo se activa si la ruta principal desaparece.



\### Equal Cost Multipath (ECMP)

Cuando dos o más rutas hacia la misma red tienen la misma AD y la misma métrica, el router puede instalar varias y balancear el tráfico entre ellas.



\---



\## 📊 Tabla de Valores de AD por Defecto (Cisco)



| Fuente de ruta | AD por defecto |

|---|---|

| Directamente conectada | 0 |

| Ruta estática | 1 |

| EIGRP summary route | 5 |

| External BGP (eBGP) | 20 |

| EIGRP interno | 90 |

| OSPF | 110 |

| RIP | 120 |

| EIGRP externo | 170 |

| Internal BGP (iBGP) | 200 |

| Desconocida / inválida | 255 |



Nota: Estos valores son los predeterminados en equipos Cisco. Pueden variar ligeramente según el fabricante o la versión.



\---



\## ⚖️ Proceso de Selección de Ruta



Cuando un router debe reenviar un paquete, sigue este orden lógico:



1\. \*\*Longest Prefix Match\*\*: busca en la tabla de enrutamiento la ruta más específica que coincida con la IP de destino. Aquí se elige la \*\*red\*\*.

2\. \*\*Administrative Distance\*\*: si hay varias rutas hacia esa misma red, elige la fuente más confiable (menor AD).

3\. \*\*Métrica\*\*: si la AD empata, elige el mejor camino según la métrica del protocolo.

4\. \*\*ECMP\*\*: si la métrica también empata, puede instalar varias rutas y balancear.



Importante: La AD \*\*no compite entre redes distintas\*\*. Primero se aplica Longest Prefix Match. Por ejemplo, una ruta /32 siempre gana sobre una /24 para una IP que pertenece a esa /32, sin importar la AD.



\---



\## 📦 Packet Flow / Route Selection Flow



```text

IP destino

&#x20;     ↓

Longest Prefix Match

(elije la red)

&#x20;     ↓

¿Varias fuentes para la misma red?

&#x20;     ↓

Administrative Distance

(elije la fuente)

&#x20;     ↓

Métrica

(elije el mejor camino dentro de la misma fuente)

&#x20;     ↓

Forwarding

