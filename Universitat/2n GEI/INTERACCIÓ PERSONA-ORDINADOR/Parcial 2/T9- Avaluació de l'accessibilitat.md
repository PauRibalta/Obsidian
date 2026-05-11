### Definicions

##### Accessibilitat i discapacitat:
**Accessibilitat:** Característica d’un sistema per ser utilitzat pel major número de persones possible. Quan més gran és el número potencial de persones que el poden fer servir, més grau d’accessibilitat té el sistema.

**Discapacitat:** Dificultat o impediment d’una persona per realitzar una certa tasca, considerada quotidiana, degut a alteracions intel·lectuals o físiques.

##### Grups de discapacitats:
**Visuals:** Parcial (visió reduïda o incompleta) o total (persones invidents).

**Auditives:** Persones sordes o amb audició molt reduïda

**Motrius:** Associada amb les discapacitats físiques.

**Cognitives:** Discapacitat intel·lectual. Grup molt complex i casos molt particulars

##### Disseny accessible:
El disseny inclusiu és una part de l'universal
**Disseny universal:**  
- Un únic disseny adaptable i vàlid per a tot tipus de discapacitats i dispositius, sense necessitat de cap adaptació 
- També anomenat “Disseny per a tots”. Actualment, no es compleix però s’està treballant en aquesta línia.

**Disseny inclusiu:** 
- Un disseny adaptat a la persona 
- Cada tipus de discapacitat requereix un tipus de disseny diferent 
- Es té en compte les característiques de les persones amb la discapacitat 
- Fàcil d’usar no és igual a inclusiu

##### Accessibilitat digital:
L’accessibilitat digital és el resultat de la interacció de tots els components treballant conjuntament.
Si falla un component de la cadena, falla tota l’accessibilitat

Exemple:​
Una persona cega vol accedir a un contingut web:
1.- Encendre el PC​
2.- Accedir al S.O.​
3.- Accedir a un navegador web (que ha de ser accessible: Com posa la URL? .Com avança?. Com retrocedeix?​
4.- El contingut de la web he de ser accessible.
I si aquesta persona cega vol ser creadora de contingut? ---> Eines de creació accessibles..​
Si falla un solo pas, el sistema complet deixa de ser accessible


### Tecnologia assistencial:

##### Definició:
- Conjunt d’eines SW i/o HW necessàries per a que una persona amb discapacitat pugui utilitzar les funcionalitats d’un sistema igual que una persona que no tingui discapacitat.
- Cada part implicada en tot el procés ha de fer la seva feina en termes d’accessibilitat per aconseguir l’objectiu.
##### Eines:
- Eines SW d’avaluació:
	- Accessibility Scanner (Android)
	- Llista W3.org
- Eines HW:
	- Teclats i apuntadors alternatius 
	- Reconeixement i sintetitzadors de veu i lectors de pantalla 
	- Ampliadors de pantalla 
	- Impressores i teclats de Braille 
	- Reconeixement de llengua de signes (encara no s’ha aconseguit)

**Sistemes de comunicació augmentativa i alternativa (SAAC):**
- Son taulers de comunicació per a millorar l’autonomia i la qualitat de vida de les persones sense llenguatge verbal i que no poden aprendre la llengua de signes, degut a discapacitats mentals, com l’autisme o la paràlisi cerebral.
- Hi ha sistemes SAAC físics (impresos en diferents tipus de materials) i digitals, configurables segons les necessitats.
- Poden indicar elecció, seqüències diàries, o històries socials per anticipar esdeveniments.
- Poden ser també fitxes individuals, preparades per fixar en un plafó.

### Documents digitals:

##### Definició:
- **Accessibilitat digital:** Concepte que apareix als anys 90
- Grau en que un producte pot ser utilitzat per una persona amb algun tipus de discapacitat de forma equivalent a com ho faria una persona sense discapacitat
- L’accessibilitat és la usabilitat d’un producte, servei, entorn o eina per a persones amb el ventall més ampli possible de capacitats

### Normativa i pautes:

W3C → WAI → WCAG
- W3C: World Wide Web Consistorium: 
	- Organització internacional d'estandardització en web  
	- WAI: Web Accessibility Initiative: 
		- Grup de treball permanent de la W3C, dedicat a fer accessible la web per a tothom 
		- WCAG: Web Content Accessibility Guidelines: 
			- Normes i pautes estàndards d’accessibilitat web proposades per la WAI
##### WCAG:
- Pautes que expliquen com fer contingut web accessible per a persones amb discapacitat o d’edat avançada 
- Hi ha diferents versions de WCAG, per ordenació cronològica 
- Dintre de cada versió, hi ha tres nivells de detall o de concreció. Quan més alt el nivell, més pautes s’han d’acomplir per aconseguir-ho
	- Nivell A: el mínim exigit per que es consideri accessible.
	- Nivell AA: el mínim exigit per l'estat, hi ha una ISO que ho regula
	- Nivell AAA: més del que s'exigeix.

**Estructura**:
- Principi: 
	- Categoria fonamental que agrupa totes les pautes relacionades amb una gran àrea d’accessibilitat 
	- Són el primer nivell en la jerarquia de les WCAG
- Pauta: 
	- Agrupació de criteris que cal complir per obtenir un cert nivell d’accessibilitat 
	- Són el segon nivell en la jerarquia de les WCAG
- Criteri:
	- Enunciat comprovable i mesurable que s’ha de complir per a assolir un cert nivell d’accessibilitat a les WCAG 
	- El seu compliment es determina amb proves objectives 
	- Són el tercer i darrer nivell de la jerarquia de les WCAG

**Evolució:**
- WCAG 2.0 
	- Publicada el 11/12/2008 
	- Té 12 pautes. 
	- És el mínim que hauria d’acomplir una web accessible 
	- Aprovada per la ISO/IEC 40500:2012
- WCAG 2.1 
	- Publicada el 5/06/2018 i actualitzada el 21/09/2023, el 12/12/2024 i el 06/05/2025 
	- Afegeix una pauta i 17 criteris nous 
	- És plenament vigent 
	- L’administració pública d’Espanya requereix aquest nivell per a les seves pàgines web
- WCAG 2.2 
	- Publicada el 5/10/2023 i actualitzada el 12/12/2024 
		- Afegeix 9 criteris nous 
		- En procés d’aprovació per la ISO

**Principis:**
- Perceptible
	- L’usuari ha de poder percebre la interfície amb algun dels sentits. Cal oferir alternatives als sentits principals  
	- Exemples:
		- 1.1.1 (A) - Contingut no textual: Tota imatge o element ha de tenir una alternativa textual. Si una imatge no apareix, s’ha de mostrar un text 
			- - 1.1.1- Text alternatiu a les imatges, per si de cas un dia es perd la imatge i es mostra la web amb la imatge trencada​
		- 1.3.1 (A) - Informació i relacions: El contingut ha d’estar marcat correctament. Els tipus de dades d’entrada han de correspondre amb l’element adequat de la web
			- - 1.3.1- Per exemple, formats d’entrada per als DNI, per a les contrasenyes, per a les dates, etc​
		- 1.4.3 (AA) - Contrast mínim: El text ha de tenir un contrast mínim amb el fons 
			-  1.4.3- Per exemple, text clar sobre fons mes clar o text fosc sobre fons fosc​
		- 1.4.9 (AAA) - Imatges de text: No s’han d’usar imatges amb text si es pot fer servir un text, tret de logos o altres casos molt específics
			- 1.4.9- No fer servir una imatge si es pot posar un text. La imatge pot acabar pixelada o poc clara segons la resolució de la pantalla
- Comprensible
	- L’usuari ha de poder comprendre la informació mostrada i el funcionament de la interfície
	- Exemples:
		- 2.4.2 (A) - Títol de pàgina: Cada pàgina ha de tenir un títol descriptiu, inclús a les pestanyes del navegador 
			- 2.4.2- Per exemple, a diferents seccions de la pàgina, no hi ha títol i no se sap el que cal fer. O no hi ha cap títol a la pestanya del navegador​
		- 2.4.4 (A) - Propòsit de l’enllaç (en context): L’objectiu de l’enllaç s’ha d’entendre segons el context on està. Ha de quedar clar a on dirigeix, segons el context 
			- 2.4.4- Per exemple “fes click aquí per al registre”. Si estem en una pàgina web que podem registrar-nos com a usuaris, o registrar un producte, no queda clar a que es refereix el “registre”​
		- 2.4.9 (AAA) - Propòsit de l’enllaç (només enllaç): L’objectiu de l’enllaç s’ha d’entendre només amb el seu text, sense dependre de context addicional
			- 2.4.9- En l’exemple anterior, “click aquí” no significa res per als lectors de pantalla
- Operable
	- L’usuari ha de poder interactuar amb tots els elements de la interfície. Cal donar alternatives a les operacions comuns 
	- Exemples:
		- 3.1.2 (AA) – Idioma de les parts: Si hi ha paraules en un altre idioma, ha d’haver una alternativa en l’idioma de la pàgina i al codi s’ha d’indicar per que el lector de pantalla ho pronuncií correctament 
			- 3.1.2 – Cal evitar paraules en altres idiomes, a no ser que siguin comunament acceptades o pertanyin al context especialitzat de la pàgina web (per exemple, es pot fer servir la paraula “web”)​
		- 3.1.4 (AAA) - Abreviatures: Cal proporcionar el significat de les abreviatures el primer com que apareixen a la pàgina 
			- 3.1.4 – Per exemple “Introdueix el codi postal (CP)”. A partir de llaors, ja es pot fer servir “CP”​
		- 3.3.1 (A) - Identificació d’errors: Identificar clarament en quin camp s’ha produït l’error i donar una descripció molt clara dels motius de l’error 
			- 3.3.1 - “S’ha produït un error” –> Quin? Com? A on?
		- 3.3.2 (A) - Etiquetes o instruccions: Tots els camps d’un formulari han de tenir una etiqueta clara o instruccions de com omplir-lo
			- 3.3.2 - Sempre una mínima informació de que cal posar a un camp
- Robust 
	- Els continguts s’han de poder mostrar independentment de la tecnologia, incloent la tecnologia assistencial.
	- Exemples:
		- 4.1.1 (A) - Anàlisi sintàctica: La pàgina web ha d’estar ben formada, per que els lectors de pantalla pugin interpretar-les
			- 4.1.1 – Evidentment, no hi pot haver errors de sintaxi en el codi de la pàgina. Encara que aquests errors no es vegin a la IU, poden confondre als lectors de pantalla

### Avaluació

##### Metodologia:
- Web Accessibility Conformance Evaluation Methodology 
- Metodologia d’avaluació de la WCAG:
	1. Definició de l’abast (Que s’inclou, quina versió es farà servir (2.0, 2.1, 2.2) i nivell de conformitat (A, AA o AAA)​)
	2. Exploració del lloc web (Identificar les pàgines web claus del lloc, les funcionalitats clau, tipus de continguts, funcionalitats i tecnologies web necessàries​)
	3. Seleccionar una mostra representativa (Segurament no serà possible avaluar totes les pàgines. Cal seleccionar només algunes i indicar el criteri de selecció)
	4. Avaluar la mostra (Determinar si compleix amb els criteris segons la versió i el nivell de conformitat triat en el pas 1. Si no, fer recomanacions per que es compleixin en un futur)
	5. Fer un informe dels resultats de l’avaluació (Recopilar en un informe tot lo anterior)

##### Easy checks
- **Definició:**
	- Proves ràpides per avaluar l’acompliment de requisits bàsics d’accessibilitat web

- Proposats per la WAI, de la W3C per fer una primera avaluació ràpida d’accessibilitat
- NO substitueix a una anàlisi en profunditat de l’accessibilitat, seguint la normativa WCAG amb els nivells de conformitat

##### Eines
- Existeixen eines automatitzades que ajuden a l’avaluació d’usabilitat de les pàgines web
- Aquestes eines comproven el codi de la pàgina, però és necessària la revisió d’experts en usabilitat per a errors de context i validació de resultats
  
- **WAVE Web Accessiblity Evaluation Tool:**
	- Desenvolupada per WebAIM 
	- Extensió de navegador o eina online 
	- Errors i avisos directament sobre la pàgina analitzada
- **Lighthouse**
	- Integrada en Chrome Navigator 
	- Genera informes automàtics 
	- Dona recomanacions basades en les WCAG


