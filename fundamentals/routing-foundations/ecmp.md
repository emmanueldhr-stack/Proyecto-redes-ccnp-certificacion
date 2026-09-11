❓ WHY (¿Por qué?)


Permitir múltiples rutas equivalentes.



En redes con topologías redundantes, es común que existan varios caminos con el mismo costo hacia un destino. Sin ECMP, el router elegiría solo uno de ellos y desperdiciaría la capacidad del resto. ECMP permite aprovechar todos los caminos disponibles, mejorando el rendimiento y la resiliencia.



📄 WHAT (¿Qué es?)


Equal Cost Multi-Path.



ECMP es la capacidad de un router de instalar y utilizar varias rutas hacia la misma red cuando todas tienen el mismo costo (misma AD y misma métrica). El router distribuye el tráfico entre esas rutas según un algoritmo de balanceo (generalmente basado en hash de campos del paquete, como IP origen, IP destino, puerto, etc.).



Características principales:



Se activa solo cuando hay empate en LPM, AD y métrica.



Puede ser per-packet o per-destination (lo más común es per-destination para evitar reordenamiento de paquetes).



Es soportado por la mayoría de los protocolos de enrutamiento dinámico (OSPF, EIGRP, BGP) y también por rutas estáticas.



El número máximo de rutas ECMP depende de la plataforma y del protocolo (por ejemplo, Cisco IOS suele soportar hasta 16 rutas por defecto, pero varía).



🧠 Conceptos Clave


Equal Cost (Igual Costo)

Dos o más rutas hacia la misma red tienen la misma distancia administrativa y la misma métrica.



Longest Prefix Match (LPM)

Mecanismo que elige la red más específica. Debe coincidir en todas las rutas candidatas para que ECMP tenga sentido.



Administrative Distance (AD)

Valor de confianza de la fuente. Si las rutas provienen de fuentes distintas, deben tener la misma AD para que ECMP se active.



Métrica

Valor interno del protocolo. Si las rutas provienen del mismo protocolo, deben tener la misma métrica.



Balanceo de carga (Load Balancing)

Distribución del tráfico entre las rutas ECMP. Puede ser por paquete, por flujo o por destino, según la configuración y la plataforma.



Hash de flujo

Algoritmo que usa campos del paquete (IP origen, IP destino, protocolo, puertos) para asignar un flujo a una de las rutas ECMP. Garantiza que los paquetes de un mismo flujo sigan la misma ruta, evitando reordenamiento.



⚙️ Cómo Funciona ECMP


El router aprende varias rutas hacia la misma red por la misma fuente (mismo protocolo) o por fuentes con la misma AD.



Verifica que todas tengan la misma métrica.



Si hay empate, en lugar de elegir una, instala todas en la tabla de enrutamiento como rutas ECMP.



Al reenviar un paquete, el router aplica un algoritmo de hash para seleccionar una de las rutas.



Los paquetes de un mismo flujo (misma combinación de IP origen, IP destino, puertos, etc.) suelen usar la misma ruta para evitar reordenamiento.



📊 Ejemplo de Tabla de Enrutamiento con ECMP


text

O     192.168.2.0/24 \[110/2] via 192.168.1.2, 00:00:12, GigabitEthernet0/0

&#x20;                    \[110/2] via 192.168.1.6, 00:00:12, GigabitEthernet0/1

En este ejemplo, OSPF ha instalado dos rutas hacia 192.168.2.0/24 con la misma AD (110) y la misma métrica (2). El router balanceará el tráfico entre ambas.




⚙️ Configuración (Ejemplo en Cisco IOS)


ECMP con OSPF

OSPF soporta ECMP de forma nativa. Por defecto, Cisco IOS instala hasta 4 rutas ECMP. Se puede aumentar con:



bash

R1(config)# router ospf 1

R1(config-router)# maximum-paths 8

ECMP con EIGRP

bash

R1(config)# router eigrp 100

R1(config-router)# maximum-paths 8

ECMP con rutas estáticas

Se pueden configurar varias rutas estáticas hacia la misma red con la misma AD y métrica:



bash

R1(config)# ip route 192.168.2.0 255.255.255.0 192.168.1.2

R1(config)# ip route 192.168.2.0 255.255.255.0 192.168.1.6

Ambas rutas tienen AD 1 y métrica 0, por lo que se instalan como ECMP.



Balanceo de carga

En algunas plataformas se puede configurar el tipo de balanceo:



bash

R1(config)# ip cef

R1(config)# interface GigabitEthernet0/0

R1(config-if)# ip load-sharing per-packet

O per-destination (por defecto en la mayoría de los casos):



bash

R1(config-if)# ip load-sharing per-destination

🔍 Verificación

Comandos útiles en Cisco IOS:



bash

show ip route

show ip route 192.168.2.0

show ip cef 192.168.2.0

show ip protocols

Ejemplo de salida show ip route:



text

O     192.168.2.0/24 \[110/2] via 192.168.1.2, 00:00:12, GigabitEthernet0/0

&#x20;                    \[110/2] via 192.168.1.6, 00:00:12, GigabitEthernet0/1

La presencia de dos entradas via para la misma red indica ECMP activo.



✅ Beneficios


Balanceo de carga: distribuye el tráfico entre múltiples caminos, aprovechando mejor el ancho de banda disponible.



Redundancia: si una de las rutas falla, el tráfico puede seguir fluyendo por las demás sin interrupción.



Alta disponibilidad: mejora la resiliencia de la red al eliminar puntos únicos de fallo en el camino.



Mejor utilización de recursos: evita que enlaces redundantes queden inactivos.



Simplicidad: no requiere configuración compleja en muchos casos; los protocolos dinámicos lo activan automáticamente al detectar rutas equivalentes.



⚠️ Limitaciones y Consideraciones


Reordenamiento de paquetes: si se usa balanceo per-packet, los paquetes pueden llegar desordenados. Por eso se prefiere per-destination o per-flow.



Asimetría: no garantiza que el tráfico de ida y vuelta siga el mismo camino.



Dependencia de la plataforma: el número máximo de rutas ECMP y los algoritmos de hash varían según el fabricante y el modelo.



Complejidad de troubleshooting: al haber múltiples caminos, diagnosticar problemas puede ser más complicado.



No siempre es óptimo: en algunos casos, el balanceo puede no ser perfecto si los flujos no se distribuyen uniformemente.




📚 Lessons Learned (Lecciones Aprendidas)


Si LPM, AD y Métrica empatan, el router puede instalar varias rutas.



ECMP es el mecanismo que permite usar múltiples rutas equivalentes simultáneamente.



El balanceo se basa en un hash de campos del paquete para mantener la coherencia de los flujos.



No todos los protocolos ni plataformas soportan la misma cantidad de rutas ECMP.



Es fundamental verificar con show ip route que las rutas ECMP estén instaladas.



ECMP mejora el rendimiento y la resiliencia, pero también introduce consideraciones de diseño.



La configuración de maximum-paths controla cuántas rutas ECMP se pueden instalar.




💡 Buenas Prácticas y Notas Adicionales


Usar balanceo per-destination o per-flow en lugar de per-packet para evitar reordenamiento.



Documentar las rutas ECMP y su propósito en la red.



Verificar que los enlaces tengan capacidades similares para que el balanceo sea efectivo.



Considerar el impacto en aplicaciones sensibles al orden de los paquetes.



En entornos multivendor, verificar el soporte y la configuración de ECMP.



Ajustar maximum-paths según las necesidades y capacidades del equipo.



Recordar que ECMP solo se activa cuando hay empate en LPM, AD y métrica.




🔗 Referencias

Documentación oficial de Cisco: Equal Cost Multi-Path (ECMP)



CCNA 200-301 Official Cert Guide, Volumen 1 y 2 (Wendell Odom)



Apuntes personales del curso de redes.





