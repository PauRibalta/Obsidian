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

**𝑂-notation**: 

$$
O(g(n)) = \{\, f(n) \mid \exists \, c > 0,\; n_0 > 0 \text{ tal que } 0 \le f(n) \le c\,g(n)\ \forall\, n \ge n_0 \,\}
$$
![[Pasted image 20260210175218.png]]

**Ω-notation**: 