El mètode **Divideix i venceràs** és una estratègia per dissenyar algoritmes asimptòticament eficients basada en la **recursivitat**.

## Funcionament

Un algoritme divideix i venceràs segueix tres passos principals:

1. **Divideix**  
   Divideix el problema en un o més subproblemes més petits del mateix tipus.

2. **Vèncer (Conquer)**  
   Resol els subproblemes recursivament.

3. **Combina**  
   Uneix les solucions dels subproblemes per obtenir la solució final.

## Cas base

Si el problema és prou petit, es resol directament sense fer més crides recursives.

## Objectiu

L’objectiu principal és millorar la complexitat d’algoritmes ineficients.

Per exemple, reduir una complexitat $O(n^2)$ a una de més eficient $O(n \log n)$
