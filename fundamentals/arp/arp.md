\# ARP



\## WHY



Permitir que un host descubra la MAC asociada a una IP.



\## WHAT



Address Resolution Protocol.



\## Packet Flow



Host

↓

Necesita MAC

↓

ARP Request

↓

ARP Reply

↓

ARP Cache



\## Reglas



ARP Request = Broadcast



ARP Reply = Unicast



\## Lessons Learned



Conocer una IP no implica conocer una MAC.

