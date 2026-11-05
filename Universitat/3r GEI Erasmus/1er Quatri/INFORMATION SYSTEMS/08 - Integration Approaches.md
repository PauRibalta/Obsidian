![[Pasted image 20261105134317.png]]

### Approaches for integration

#### 1. Data-Level integration
![[Pasted image 20261105134243.png|460]]

Middleware guarantees consistency, avoids duplication and ensures data update propagation.

How:
- FORMAT: representation, structure and presentation about the data.
- SEMANTIC: currency, mesurement and units of the data.

#### 2. Application level integration
![[Pasted image 20261105135051.png|476]]

Captura 14:00

ADM and production can be decomposed into 3 logical elements:
- Presentation layer (Front-end)
- Logic layer (Back-end)
- Data layer (Back-end)

In closed systems each layer only interacts with near layers of the same application.
In open systems each layer interacts with the ones they do in a closed system and with the same layer of other applications.

With open systems we can guarantee integration, but with a closed system we don't know. This method is expensive as it requires some modifications.

#### 3. Presentation level integration

Captura 14:04

Only modify the presentation layer (front-end) to call back-end's of other applications.


### Integration models

#### 1. Point-to-point integration

![[Pasted image 20261105140756.png|394]]

Each application is connected with eachother applications with a customised connector. This model is not scalable as for each application we add we need to are more connectors (n^2), also is not flexible because if we update an app we will need to reconnct it.

#### 2. Hub-and-spoke

![[Pasted image 20261105141244.png]]

- All requests are handled by the hub. The hub coordinates the information flow.
- N connectors needed for each number of applications.

#### 3. Business process management system (BPMS)

For applications with complex interactions

1. Workflow component
	![[Pasted image 20261105142209.png|410]]
	The workflow component separates the app logic from the composition logic.

![[Pasted image 20261105142304.png|488]]

### Service oriented approach

Exploit standarization to enable integration

**Service:** self-contained application module implementing a functionality. Provides an interface to enable interaction

![[Pasted image 20261105143402.png|364]]

API (application programming interface):
- Where? (url)
- What? (method, operations)
- How? (input/output)
- Quality (throughput for example)
The api is independent from the programming language.

#### Actors:
- Service providers: provides functionalities and defines the methods.
- Service consumers: uses the service calling the api.

![[Pasted image 20261105144400.png|473]]

- Service requestor can also be named service consumer.
- In the binding we need to define an SLA (service level agreement)

### Service orchestration

Cooperation between complex applications using the software applications (SOA).

![[Pasted image 20261105145342.png|306]]

![[Pasted image 20261105145639.png|442]]

Captura pantalla 14:56

#### Dynamic service selection

![[Pasted image 20261105145848.png|441]]