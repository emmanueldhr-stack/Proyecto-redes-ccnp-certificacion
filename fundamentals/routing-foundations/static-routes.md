# Static Routes (Rutas Estáticas)

## 📌 Descripción General

En redes, los routers necesitan saber cómo llegar a redes que no están directamente conectadas a ellos. Las **rutas estáticas** son una forma manual de indicar al router el camino hacia esas redes remotas. A diferencia de los protocolos de enrutamiento dinámico, aquí el administrador configura explícitamente cada ruta.

Este documento forma parte de un repositorio de aprendizaje de redes. Aquí se explica qué son las rutas estáticas, cómo funcionan, cómo configurarlas y cuándo conviene usarlas.

---

## ❓ WHY (¿Por qué?)

Permitir a un router llegar a redes que no están conectadas directamente.

Cuando un router recibe un paquete cuyo destino no está en ninguna de sus interfaces directamente conectadas, necesita una "ruta" para saber a dónde enviarlo. Sin una ruta (estática o dinámica), el router descarta el paquete y envía un mensaje ICMP de "Destination Unreachable".

Las rutas estáticas son esenciales para:
- Redes pequeñas o con topología simple.
- Definir rutas de respaldo (backup) o de último recurso (default route).
- Proporcionar control total sobre el enrutamiento.

---

## 📄 WHAT (¿Qué es?)

Ruta configurada manualmente por un administrador.

Una ruta estática es una entrada en la tabla de enrutamiento que se introduce de forma manual. Especifica:
- **Red de destino**: la red remota a la que se quiere llegar (ej: 192.168.2.0/24).
- **Máscara de subred**: define el tamaño de la red.
- **Next-Hop**: la dirección IP del siguiente router al que se debe enviar el paquete (o la interfaz de salida).

El router confía ciegamente en esta configuración; no verifica si la ruta es correcta o si el next-hop es alcanzable, a menos que se utilicen mecanismos adicionales como el seguimiento de estado (tracking).

---

## 🧠 Conceptos Clave

### Remote Network (Red Remota)
Cualquier red que no está directamente conectada al router. Para llegar a ella, el paquete debe ser reenviado a través de otro router (next-hop).

### Next Hop (Siguiente Salto)
La dirección IP del siguiente router en el camino hacia la red de destino. Debe ser alcanzable a través de una de las interfaces directamente conectadas del router actual.

### Routing Table (Tabla de Enrutamiento)
Base de datos interna del router donde se almacenan todas las rutas conocidas (estáticas, dinámicas, directamente conectadas). El router consulta esta tabla para decidir por cuál interfaz y hacia qué next-hop enviar cada paquete.

Otros conceptos importantes:
- **Interfaz de salida**: en lugar de especificar un next-hop, se puede indicar la interfaz por la que debe salir el paquete.
- **Distancia administrativa (AD)**: valor de confianza de la ruta. Las rutas estáticas tienen AD=1 (muy confiable). Una AD menor es preferida sobre una mayor.
- **Default Route (Ruta por defecto)**: ruta estática especial con destino 0.0.0.0/0, usada cuando no hay una ruta más específica.

---

## 📦 Packet Flow (Flujo de un Paquete)

Cuando un router recibe un paquete, sigue estos pasos:

1. **IP destino**: Extrae la dirección IP de destino del encabezado IP del paquete.
2. **Routing Table**: Busca en su tabla de enrutamiento una entrada que coincida con la red de destino (la coincidencia más específica gana).
3. **Static Route**: Si la entrada es una ruta estática, el router la selecciona.
4. **Next-Hop**: Lee la dirección del next-hop asociado a esa ruta.
5. **Forwarding**: Reenvía el paquete a través de la interfaz hacia ese next-hop. El proceso se repite en cada router hasta llegar al destino.

```text
IP destino
      ↓
Routing Table
      ↓
Static Route
      ↓
Next-Hop
      ↓
Forwarding

## Limitaciones

Las rutas estáticas requieren administración manual.

Si el next-hop deja de estar disponible:

- La ruta puede seguir existiendo.
- El tráfico puede perderse.
- No existe adaptación automática.

## Recursive Lookup

El router debe conocer cómo llegar al next-hop antes de reenviar el tráfico.

## Packet Walk Multi-Router
 
La IP destino permanece igual durante todo el recorrido.

Las direcciones MAC cambian en cada salto.

## Lessons Learned

IP = destino final

MAC = siguiente salto