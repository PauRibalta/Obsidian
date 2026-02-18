
**Què és?** Memòria de capacitat reduïda i ràpida, situada en el nivell immediatament inferior al processador. Serveix per **guardar dades i instruccions que s’usen sovint**, així el processador no ha d’anar sempre a buscar-les a la memòria principal (RAM), que és més lenta.

**Paraula** = És la quantitat de bits que la CPU pot tractar “de cop”.

- Cache hit (encert). La posició referenciada pel processador és a la MC. 
- Cache miss (fallada). La posició referenciada no és a la MC. Cal iniciar un cicle d’accés a la memòria principal

Format de l'adreça de MP =[número bloc | paraula del bloc]

**Tag** = part de l’adreça de memòria que serveix per identificar **quina línia de memòria principal està guardada a la cache**.

### Configuracions 
... (TO DO)

### Estructura MC/MP

La MP està formada per M blocs de 2^w paraules. Dins el bloc hi ha paraules d'una llargada concreta. Un bloc és la mínima quantitat d’informació que es transfereix entre MC i MP.![[Pasted image 20260205173707.png]]

### Operació de lectura

Aquest esquema mostra **què passa quan la CPU vol llegir una dada de la memòria cache**.
![[Pasted image 20260205174853.png]]
#### 1. La CPU demana una dada
La CPU envia una **adreça**, que es divideix en:

- **Direcció del bloc** → quin bloc és  
- **Paraula** → quina paraula dins del bloc  

#### 2. Es busca a la cache
Es mira al **directori de la cache (CD)** si aquest bloc hi és.

#### 3. Es comprova si hi és

#### Si hi és (hit)
1. S’obté el bloc de la cache  
2. Se selecciona la paraula  
3. S’envia al processador  
4. S’actualitza l’estat (LRU, etc.)

#### Si no hi és (miss)
1. Es va a la memòria principal (RAM)  
2. Es porta el bloc sencer  
3. S’ha de decidir on posar-lo → **algorisme de substitució**  
4. Es guarda a la cache  
5. Es selecciona la paraula  
6. S’envia a la CPU  

#### Resum ràpid

Quan la CPU vol llegir:

1️⃣ Mira a la cache  
2️⃣ Si hi és → dona la dada (hit)  
3️⃣ Si no → va a RAM (miss)  
4️⃣ Canvia blocs si cal  
5️⃣ Guarda i continua  

 La cache accelera l’accés a memòria guardant blocs freqüents i utilitza algorismes de correspondència i substitució per gestionar-los.


### Algorismes de correspondència
**Per què serveixen?** Indiquen a quins slots de MC pot ser copiat un bloc determinat de MP.

Dubtes : MP % MC = posició --> en quin algorisme s'aplica?

- Directa : no és el bloc al que està, sinó a la posició que està dins del bloc. posició = MP % MC
- Completament associativa
- Associativa per conjunts

$$
\text{Taxa d'encerts (\%)} = \frac{\text{Nombre d'encerts}}{\text{Nombre total d'intents}} \times 100
$$
