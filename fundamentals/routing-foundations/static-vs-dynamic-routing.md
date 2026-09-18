Static vs Dynamic Routing (Enrutamiento Estático vs Dinámico)



📌 Descripción General



El enrutamiento es el proceso mediante el cual los routers deciden por dónde reenviar los paquetes hacia su destino. Existen dos enfoques principales para poblar la tabla de enrutamiento: \*\*Static Routing\*\* (configuración manual) y \*\*Dynamic Routing\*\* (aprendizaje automático mediante protocolos).



Este documento forma parte del repositorio de fundamentos de enrutamiento. Aquí se comparan ambos enfoques, sus ventajas, desventajas y cuándo conviene usar cada uno.



\---



❓ WHY (¿Por qué?)



Las redes necesitan comunicarse incluso cuando cambian las topologías.



Los enlaces pueden caer, aparecer nuevas redes o cambiar los caminos disponibles. Un buen diseño de enrutamiento debe garantizar conectividad, adaptación a cambios y escalabilidad. La elección entre enrutamiento estático y dinámico determina qué tan bien responde la red ante estos escenarios.



\---



📄 WHAT (¿Qué es?)



Existen dos enfoques principales:



\- \*\*Static Routing\*\*

\- \*\*Dynamic Routing\*\*



Ambos conviven en la tabla de enrutamiento y se complementan. De hecho, lo más común en redes reales es una \*\*combinación de ambos\*\*.



\---



🧱 Static Routing (Enrutamiento Estático)



Rutas configuradas manualmente por el administrador. El router no aprende nada por sí solo: solo usa lo que se le indica explícitamente.



✅ Ventajas



\- \*\*Simple\*\*: fácil de entender y configurar en redes pequeñas.

\- \*\*Predecible\*\*: el camino del tráfico está totalmente controlado.

\- \*\*Bajo consumo\*\*: no usa CPU ni ancho de banda para intercambiar información.

\- \*\*Segura\*\*: no hay riesgo de rutas inyectadas por terceros.

\- \*\*Útil como respaldo\*\*: las floating static routes complementan protocolos dinámicos.



❌ Desventajas



\- \*\*Manual\*\*: cada cambio requiere intervención del administrador.

\- \*\*Difícil de escalar\*\*: en redes grandes se vuelve inmanejable.

\- \*\*Sin adaptación automática\*\*: si un enlace cae, la ruta permanece hasta que se elimine manualmente.

\- \*\*Propensa a errores humanos\*\*: una ruta mal escrita puede romper la conectividad.

\- \*\*No detecta fallos\*\*: no reacciona ante cambios de topología.



\---



🔄 Dynamic Routing (Enrutamiento Dinámico)



Protocolos que aprenden rutas automáticamente, calculan los mejores caminos y actualizan la tabla de enrutamiento ante cambios de topología. Ejemplos: \*\*OSPF, EIGRP, RIP, BGP, IS-IS\*\*.



✅ Ventajas



\- \*\*Aprende rutas automáticamente\*\*: no requiere configuración por cada red.

\- \*\*Detecta fallos\*\*: reacciona ante caídas de enlaces y recalcula caminos.

\- \*\*Escala mejor\*\*: ideal para redes medianas y grandes.

\- \*\*Puede usar ECMP\*\*: aprovecha múltiples caminos equivalentes.

\- \*\*Convergencia automática\*\*: mantiene la red consistente sin intervención manual.

\- \*\*Intercambio de información\*\*: los routers se comunican entre sí para compartir rutas.



❌ Desventajas



\- \*\*Más complejo\*\*: requiere conocimiento del protocolo y su configuración.

\- \*\*Consume más recursos\*\*: usa CPU, memoria y ancho de banda para los mensajes de routing.

\- \*\*Puede ser vulnerable\*\*: sin autenticación, alguien podría inyectar rutas falsas.

\- \*\*Troubleshooting más difícil\*\*: hay más variables en juego.

\- \*\*Convergencia no instantánea\*\*: puede haber pérdida de paquetes durante la transición.



\---



⚙️ HOW (¿Cómo funciona cada uno?)



Static Routing



```text

Administrador

&#x20;     ↓

Configura ruta

&#x20;     ↓

Router utiliza ruta

El administrador define manualmente la red de destino, la máscara y el next-hop. El router simplemente obedece.



Dynamic Routing

text

Router

&#x20;     ↓

Aprende rutas

&#x20;     ↓

Calcula mejores caminos

&#x20;     ↓

Actualiza tabla

El router intercambia información con sus vecinos, aplica métricas y algoritmos, y actualiza la tabla de enrutamiento de forma automática.




📊 Comparación Rápida

Criterio	Static	Dynamic

Configuración	Manual	Automática

Escalabilidad	Baja	Alta

Adaptación a fallos	No	Sí

Consumo de recursos	Bajo	Mayor

Complejidad	Baja	Alta

Predecibilidad	Alta	Media

Uso de ECMP	Sí (manual)	Sí (automático)

Detección de fallos	No	Sí

Ideal para	Redes pequeñas, rutas específicas, respaldo	Redes medianas y grandes


🎯 ¿Cuándo usar cada uno?

Usar Static Routing cuando:

La red es pequeña o tiene topología simple.



Se necesita una ruta específica (por ejemplo, hacia un servidor).



Se quiere una ruta de respaldo (floating static route).



Se requiere control total del camino.



El enlace es punto a punto y estable.



Usar Dynamic Routing cuando:

La red es mediana o grande.



La topología cambia con frecuencia.



Se necesita redundancia y alta disponibilidad.



Se requiere escalabilidad.



Hay múltiples caminos posibles hacia los destinos.



En la práctica: combinar ambos

Lo más común es usar dynamic routing como base y static routing para casos específicos:



Ruta por defecto hacia el ISP.



Rutas de respaldo.



Rutas hacia redes que no se quieren anunciar.



📚 Lessons Learned (Lecciones Aprendidas)

Los protocolos dinámicos existen para resolver:



Escalabilidad: manejar muchas redes sin configuración manual.



Adaptación automática: reaccionar ante cambios de topología.



Redundancia: ofrecer múltiples caminos y detectar fallos.



Reglas clave:



Static es simple, predecible y de bajo consumo, pero no escala.



Dynamic aprende, adapta y escala, pero es más complejo y consume recursos.



En redes reales, se combinan ambos enfoques según las necesidades.



La elección depende del tamaño de la red, la criticidad y los requisitos de disponibilidad.



💡 Buenas Prácticas y Notas Adicionales

Documentar siempre las rutas estáticas y su propósito.



No usar solo rutas estáticas en redes grandes; el mantenimiento se vuelve inviable.



No depender exclusivamente de protocolos dinámicos si se requiere control fino del tráfico.



Usar autenticación en protocolos dinámicos (OSPF, EIGRP, BGP) para evitar rutas falsas.



Configurar floating static routes como respaldo de rutas dinámicas.



Revisar periódicamente la tabla de enrutamiento con show ip route.



Considerar el impacto de la convergencia en aplicaciones críticas.



🔗 Referencias

Documentación oficial de Cisco: Static vs Dynamic Routing



CCNA 200-301 Official Cert Guide, Volumen 1 y 2 (Wendell Odom)



Apuntes personales del curso de redes.

