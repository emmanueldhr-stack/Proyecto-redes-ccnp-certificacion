\# Packet Walk Local



\## Escenario



PC-A -> PC-B



Misma red



\## Flujo



1\. PC-A determina que el destino es local.



2\. Consulta la ARP Cache.



3\. Si no existe la MAC:

&#x20;  - Envía ARP Request (Broadcast).



4\. El switch aprende:

&#x20;  MAC origen + puerto.



5\. El switch hace flooding.



6\. PC-B responde con ARP Reply.



7\. El switch aprende la MAC de PC-B.



8\. PC-A actualiza la ARP Cache.



9\. Se envía tráfico Unicast.

