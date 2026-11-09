# Metodo de Subnetting: El numero magico

## Concepto
El subnetting se basa en calcular el tamano del bloque (block size) para encontrar los Network IDs. la formula es: 'Numero magico =256 - (Valor del octeto de la mascara)'

## Ejemplo Practico
- IP: 10.0.0.200/28 (255.255.255.240)
- Numero magico: 256 - 240 = 16
- Los network IDs saltan de 16: 0, 16, 32... 192, 208.
- El 200 cae en el bloque de 192
- Network ID: 10.0.0.192
- Broadcast: 10.0.0.207
- Rango de host: 10.0.0.193 a 10.0.0.206

## VLSM (Variable Length Subnet Mask)
Para no desperdiciar IPs, se ordenan las subredes de mayor a menor (ej. 100 host, 50 hosts, 20 host) y el siguiente Network ID siempre es el Broadcast de la red anterior +1.
