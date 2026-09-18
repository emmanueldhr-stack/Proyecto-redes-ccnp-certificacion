\# Routing Foundations (Fundamentos de Enrutamiento)



\## 📌 Descripción General



Esta sección del repositorio reúne los conceptos fundamentales del enrutamiento IP. Aquí se documenta cómo un router toma decisiones para reenviar paquetes, qué tipos de rutas existen, cómo se comparan entre sí y cómo se instalan en la tabla de enrutamiento.



El objetivo es construir una base sólida antes de avanzar hacia protocolos de enrutamiento dinámico (OSPF, EIGRP, BGP) y temas avanzados como ingeniería de tráfico, MPLS o segment routing.



\---



\## 🎯 Objetivos de Aprendizaje



Al completar esta sección, se espera poder:



\- Explicar cómo un router toma decisiones de reenvío.

\- Diferenciar entre rutas conectadas, estáticas y dinámicas.

\- Aplicar Longest Prefix Match correctamente.

\- Entender el rol de la Administrative Distance y la métrica.

\- Identificar cuándo se activa ECMP.

\- Comprender el concepto de Next-Hop y Recursive Lookup.

\- Leer e interpretar una tabla de enrutamiento.



\---



\## 📚 Temas



| # | Tema | Descripción breve | Estado |

|---|---|---|---|

| 1 | Routing Table | Base de datos interna del router con todas las rutas conocidas. | ✅ |

| 2 | Connected Routes | Redes directamente conectadas a las interfaces del router. | ✅ |

| 3 | Static Routes | Rutas configuradas manualmente por el administrador. | ✅ |

| 4 | Default Route | Ruta de último recurso (0.0.0.0/0) cuando no hay coincidencia. | ✅ |

| 5 | Longest Prefix Match | Mecanismo que elige la ruta más específica. | ✅ |

| 6 | Administrative Distance | Valor de confianza entre distintas fuentes de rutas. | ✅ |

| 7 | Metrics | Valor interno de cada protocolo para elegir el mejor camino. | ✅ |

| 8 | ECMP | Uso de múltiples rutas equivalentes para balanceo y redundancia. | ✅ |

| 9 | Next-Hop | Siguiente router en el camino hacia la red de destino. | ✅ |

| 10 | Recursive Lookup | Resolución del next-hop a través de otra ruta. | ✅ |

| 11 | Forwarding | Proceso final de reenvío del paquete por la interfaz correcta. | ✅ |



\---



\## 🧠 Mental Model (Modelo Mental)



Cuando un router recibe un paquete, sigue este orden lógico:



```text

IP destino

&#x20;     ↓

Routing Table lookup

&#x20;     ↓

Longest Prefix Match

(elije la red)

&#x20;     ↓

Administrative Distance

(elije la fuente)

&#x20;     ↓

Metrics

(elije el mejor camino)

&#x20;     ↓

ECMP

(si todo empata, balancea)

&#x20;     ↓

Next-Hop / Recursive Lookup

&#x20;     ↓

Forwarding


Reglas clave:



Longest Prefix Match elige la red.



Administrative Distance elige la fuente.



Metrics elige el mejor camino dentro de la misma fuente.



ECMP entra en juego cuando todo empata.



📖 Desarrollo de los Temas

1\. Routing Table (Tabla de Enrutamiento)

Base de datos interna del router donde se almacenan todas las rutas conocidas. Incluye rutas conectadas, estáticas y dinámicas. El router la consulta en cada decisión de reenvío.



2\. Connected Routes (Rutas Conectadas)

Redes directamente conectadas a las interfaces activas del router. Tienen AD = 0 y siempre son las más confiables.



3\. Static Routes (Rutas Estáticas)

Rutas configuradas manualmente por el administrador. Tienen AD = 1 por defecto. Útiles en redes pequeñas, rutas específicas o rutas de respaldo.



4\. Default Route (Ruta por Defecto)

Ruta especial con destino 0.0.0.0/0. Se usa cuando no hay una ruta más específica que coincida con el destino. También conocida como "gateway of last resort".



5\. Longest Prefix Match (LPM)

Mecanismo que elige la ruta con la máscara más larga (más específica) para una IP de destino. LPM elige la red.



6\. Administrative Distance (AD)

Valor de confianza entre distintas fuentes de rutas. Menor AD = mayor prioridad. AD elige la fuente.



Valores comunes:



Connected = 0



Static = 1



eBGP = 20



EIGRP = 90



OSPF = 110



RIP = 120



iBGP = 200



7\. Metrics (Métricas)

Valor interno que usa cada protocolo para elegir el mejor camino dentro de la misma fuente. Ejemplos: costo (OSPF), ancho de banda + delay (EIGRP), saltos (RIP).



8\. ECMP (Equal Cost Multi-Path)

Cuando LPM, AD y métrica empatan, el router puede instalar varias rutas equivalentes y balancear el tráfico. Aporta balanceo de carga, redundancia y alta disponibilidad.



9\. Next-Hop (Siguiente Salto)

Dirección IP del siguiente router en el camino hacia el destino. Debe ser alcanzable desde una interfaz directamente conectada.



10\. Recursive Lookup (Búsqueda Recursiva)

Proceso por el cual el router resuelve el next-hop de una ruta consultando otra entrada en la tabla de enrutamiento. Ocurre cuando el next-hop no está directamente conectado.



11\. Forwarding (Reenvío)

Proceso final: el router envía el paquete por la interfaz de salida hacia el next-hop resuelto. Es el resultado de todo el proceso de decisión.



🔗 Relación entre los Temas

text

Routing Table

&#x20;  ├── Connected Routes

&#x20;  ├── Static Routes

&#x20;  │      └── Default Route

&#x20;  └── Dynamic Routes

&#x20;         ├── OSPF

&#x20;         ├── EIGRP

&#x20;         └── BGP



Selección de ruta:

&#x20;  LPM → AD → Metrics → ECMP



Reenvío:

&#x20;  Next-Hop → Recursive Lookup → Forwarding

⚙️ Comandos Útiles (Cisco IOS)

bash

show ip route

show ip route static

show ip route connected

show ip route 192.168.2.0

show ip protocols

show ip cef

show ip interface brief


📚 Lecciones Aprendidas

La tabla de enrutamiento es el corazón de las decisiones de forwarding.



LPM elige la red; AD elige la fuente; métrica elige el camino.



ECMP solo se activa cuando todo empata.



El next-hop debe ser alcanzable, ya sea directamente o por recursive lookup.



Las rutas conectadas y estáticas son la base de cualquier topología.



Entender estos fundamentos es esencial antes de estudiar protocolos dinámicos.



✅ Estado

Completado ✅



Todos los temas de esta sección han sido documentados y revisados. Esta base permite avanzar hacia:



Protocolos de enrutamiento dinámico (OSPF, EIGRP, BGP).



Ingeniería de tráfico y políticas de enrutamiento.



Redes avanzadas (MPLS, segment routing, SD-WAN).



🔗 Referencias

CCNA 200-301 Official Cert Guide, Volumen 1 y 2 (Wendell Odom)



Documentación oficial de Cisco: IP Routing



Apuntes personales del curso de redes.

