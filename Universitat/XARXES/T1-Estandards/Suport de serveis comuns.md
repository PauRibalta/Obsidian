Una **xarxa de comunicacions** es pot entendre com una **infraestructura que crea canals de comunicació** perquè **les aplicacions** que s’executen en **hosts diferents** es puguin parlar entre elles.

Al dibuix:
![[Pasted image 20260205154801.png]]
- Cada **host** té una **aplicació**.

- La xarxa (el “núvol”) no és important pels detalls interns, sinó pel fet que **proporciona un canal** entre aplicacions.

- El **canal** (la línia vermella) és una abstracció: fa veure a les aplicacions que estan connectades directament, encara que per sota hi hagi molts elements intermedis.

**Cal decidir quines funcionalitats bàsiques han d’oferir aquests canals** a totes les aplicacions.