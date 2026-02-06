La efectivitat de la jerarquia de memòria es basa en el principi de localitat dels processos. 

- *Localitat temporal*: Els programes tendeixen a reutilitzar dades que s’han usat recentment.
- *Localitat espacial*: si es referència un element, els elements propers a ell tendiran també a ser referenciat.

**Definicions**
- *Bloc*: Mínima unitat d'informació que pot estar present o no en un dels nivells de la jerarquia.
- *Encert (hit):* Accés a una informació que es troba en el nivell buscat. 
- *Fallada (miss):* Accés a una informació que no es troba en el nivell buscat. 
- *Taxa d’encerts (hit rate):* fracció d'accessos a memòria que es troben en el nivell superior (o en general en un nivell concret).

Rendiment : $$T_{accés} = T_{encert} + f_{fallades} \times P_{fallada}$$