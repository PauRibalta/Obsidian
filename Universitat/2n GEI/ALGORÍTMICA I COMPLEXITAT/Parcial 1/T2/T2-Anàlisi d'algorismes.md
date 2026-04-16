## 1. Eficiència d'algorismes

La eficiència / cost d'un algorisme té dos aspectes:
1. Temps
2. Espai

Aquests depenen de diversos factors com el disseny de l'algorisme, la mida de la instància, el llenguatge de programació emprat...

#### Cost de temps: 
El temps d'execució depèn de la mida de l'input.


## 2. Notació asimptòtica

**Ordre de creixement:**
	**Què és?** És una manera de descriure **com creix el temps d’un algoritme** quan augmenta la mida de l’entrada *n*. 

### **𝑂-notation:** 

La **notació *O*** caracteritza un límit superior del comportament asimptòtic d’una funció.  
Dit d’una altra manera, indica que una funció no creix més ràpid que una certa taxa, determinada pel terme de grau més alt.

$$
O(g(n)) = \left\{ f(n) \;:\; \text{existeixen constants positives } c \text{ i } n_0 
\text{ tals que } 0 \le f(n) \le c\,g(n) \text{ per a tot } n \ge n_0 \right\}
$$
![[Pasted image 20260225114620.png]]
![[Pasted image 20260210175218.png]]

### **Ω-notation:** 

La **notació Ω** caracteritza un límit inferior del comportament asimptòtic d’una funció.  
Dit d’una altra manera, indica que una funció creix com a mínim tan ràpid com una certa taxa, determinada pel terme de grau més alt.
$$
\Omega(g(n)) = \{ f(n) \mid \text{existeixen constants positives } c \text{ i } n_0 \text{ tals que } 0 \le c\,g(n) \le f(n) \text{ per a tot } n \ge n_0 \}
$$
![[Pasted image 20260225114822.png]]
![[Pasted image 20260225115016.png]]

### **Notació Theta (Θ):**

La **notació Θ** caracteritza un límit ajustat (tight bound) del comportament asimptòtic d’una funció. 
Indica que una funció creix exactament a una determinada taxa, determinada pel terme de grau més alt.

$$
\Theta(g(n)) = \{ f(n) \mid \text{existeixen constants positives } c_1, c_2 \text{ i } n_0 \text{ tals que } 0 \le c_1 g(n) \le f(n) \le c_2 g(n) \text{ per a tot } n \ge n_0 \}
$$
![[Pasted image 20260225115301.png]]


### Notacions:
S'agafen el millor i el pitjor cas possible, el cost dels dos casos està parametritzada per la mida de la instància, a la que direm n.
- Millor cas: f(n)
- Pitjor cas: g(n)
Hem de tenir les cotes inferiors i superiors, per les quals ni el millor ni el pitjor cas creixeran més o menys que les cotes.
- Cota superior u(n) -----> 𝑂-notation
- Cota inferior l(n) -----> Ω-notation
#### Quicksort
- ##### Millor cas
	- O(n log n)  
	- Ω(n log n)  
	- Θ(n log n)  
- ##### Cas mitjà
	- O(n log n)  
	- Ω(n log n)  
	- Θ(n log n)  
- ##### Pitjor cas
	- O(n²)  
	- Ω(n²)  
	- Θ(n²)  

#### Mergesort  
- ##### Millor cas  
	- O(n log n)  
	- Ω(n log n)  
	- Θ(n log n)  
- ##### Cas mitjà  
	- O(n log n)  
	- Ω(n log n)  
	- Θ(n log n)  
- ##### Pitjor cas  
	- O(n log n)  
	- Ω(n log n)  
	- Θ(n log n)
#### Insertion Sort  
- ##### Millor cas  
	- O(n)  
	- Ω(n)  
	- Θ(n)  
- ##### Cas mitjà  
	- O(n²)  
	- Ω(n²)  
	- Θ(n²)  
- ##### Pitjor cas  
	- O(n²)  
	- Ω(n²)  
	- Θ(n²)

#### Escala d'infinits: 
- 1<ln(n)<n<nlog(n)<n^2< n^2 log(n) < n^k < 2^n < n^k < n! <n^n
