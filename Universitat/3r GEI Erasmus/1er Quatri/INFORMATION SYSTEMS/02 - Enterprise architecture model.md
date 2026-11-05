# The archimate model

Enterprise architecrure (EA): set of documents, standards, tools, models describing different aspects from different points of views.

![[Pasted image 20260917144628.png|481]]

Archimate is a graphical **language** that can be used to represent an EA.

### Strategic layer (+ management perspective)

##### Architectural layer (bridge between perspectives)

### Technological layer (+ development perspective)

The two layers have to be aligned, and we have to build a bridge bewteen these two layers, and we do that with the architectural layer.

The archimate model has a core with multiple layers around, but we will only focus on the core.

### Point of view

- Business layer
	- Which services are offered, to whom and which business functions are used to support these services.
- Application layer (developer or system architect)
	- Which applications are  needed to implement the business functions and which data.
- Technical layer
	- Platforms that we need to use in order to install and deploy the application.

The diferent PoV's are related.


#### Archimate uses a service approach

**Service:** approach that separates what is visible to the external part of the service and what is the implementation of the service itself. Separates visible functionalities from implementation.

Service and implementation should be separated but are related with ona another. As an implementation from one service can be used by a second service.

![[Pasted image 20260918153214.png]]

### Aspects in archimate:

Passive: what is involved in the action.
Active: who performs the action.
Behavioural: how the action is performed.

**Example:**
- John (active) ---->reads (behavioural) ---->a book (passive).

Acrhimate defines a language that is formed by lexicon + semantic + syntaxis.

**Example: (e-commerce)**

![[Pasted image 20260918154733.png]]
