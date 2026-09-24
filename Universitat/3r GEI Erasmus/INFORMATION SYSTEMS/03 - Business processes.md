Process: representation of the activities of an organization and describe how they are executed.

In the business layer of the archimate model we have the processes. Inside a business process we can have different business functions.
And in the aplicational layer we can connect the functions.

### Process model
Specification of a business process that reveals what the system should do, who uses it and which data is needed.
We need to find a relation between BP activities ans software applications, and also the data these applications need to use.

A process model gives us:
- Alignment: between the business and IT perspectives. 
- Automation: from BP activities to SW apps
- Integration: BPM shows the connections among SW components that implementing BP functions.
- Quality monitoring: metrics ( time, cost, errors, bottlenecks) usefull to optimise SW apps.

### BPMN---> Business Process Modeling Notation

- Show catalog to the user (Shop)
- The client selects the product (Client)
- The client sends the order (Client)
- Process the order (Shop)
- Ship the order (Shop)
- The client recieves the order (Client)
- The client pays (Client)
- Send invoice (Shop)

**This example is not bpmn, is an informal way to represent the steps.**

#### What should I model?
- Activities, Events, Decision points
- Roles, Organizations involved

![[Pasted image 20260924141819.png|513]]

![[Pasted image 20260924142023.png|313]]![[Pasted image 20260924142347.png|375]]

Model: abstract representation of a process.
Instance: specific execution of a BP.
1 model ---> several instances

### Application components
An IS must support BPs: partially with SW applications

Evolution of SW components:
- 70's: 
	- Strong dependency between HW and SW
	- First operating systems were created
- 80's:
	- Databases and DBMS.
- After the 80's:
	- Graphical user interfaces

Today's format: 
- GUI: formed by GUI and APP ----> front-end
- APP logic: formed by DNMS, database OS, HW ----> back-end

### Applications architectural aspects
Each application composed of 3 layers (Logical layers) 
- Presentation (P): how it interacts with users (GUI, CLI (command line interface)).
- Application logic (L): functionalities implementation.
- Data access layer (D): interaction (read / write) with data.
- P and L are the front-end and D is the back-end.

![[Pasted image 20260924150935.png|595]]