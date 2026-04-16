Normalment, els **enllaços** (cables, connexions, canals de comunicació) poden transmetre **més dades** de les que els **nodes** (ordinadors, routers, dispositius) realment necessiten enviar o rebre.

**Multiplexació**: Tècnica que permet emprar un canal físic per múltiples comunicacions.

Tècniques de Multiplexació:
- FDMA (Frequency Division Multiple Access)
	- L’ample de banda del medi de transmissió ´es més gran que el dels senyals a transmetre.
- TDMA (Time Division Multiple Access)
	- La velocitat de transmissió del medi és més elevada que la dels senyals a transmetre.
- Multiplexat estadístic
	- Variant TDMA que permet estalviar recursos. La idea és la següent:
		- Suposem la informació en unitats de mida temporal T (paquets)
		- Suposem que les fonts (hosts) alliberen paquets aleatòriament
		- Tractarem de retardar-los (buffers) fins poder-los ubicar correctament
- CDMA (Code Division Multiple Access): 
	- A cada font es genera seqüencia pseudoaleatòria
	- S’empra principalment en sistemes de ràdio.
- WDMA (Wavelength Division Multiple Access)
	- Conceptualment igual que FDMA però aplicat a senyals lluminosos.
