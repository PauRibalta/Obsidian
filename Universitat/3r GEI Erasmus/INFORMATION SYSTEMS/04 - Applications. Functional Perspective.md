One IS uses several applications 

### Modular approach

- 1 module / 1 SW component implements 1 functionality / (sub) process.
- + Flexibility (add, remove, substitute SW components without having much impact on the rest of components)
- Approaches:
	1. Buy (COTS, commercial off the shelf)
	2. Make (internal development / SW house)

### Application portfolio (functional perspective)

Set of applications used in an organization supporting the organization processes.


![[Pasted image 20261001140140.png|576]]

1. Primary processes of Porter's value chain
2. Supporting processes of Porter's value chain
3. Supporting strategy and control activities

### Evolution of Application Portfolio

![[Pasted image 20261001140752.png|302]]

**To implement this a syloed approach is used**
- Problems:
	1. The data is not updated at the same frequency
	2. We can have inconsistency between the data
	3. We can have redundancy, due to saving the same data multiple times

#### Syloed Applications : challenges

1. Reflect an incremental development of an IS
2. Strong functional separations
3. Specialised SW providers

![[Pasted image 20261001162902.png]]

### ERP - Enterprise Resource Planning

Software suite
Goals:
	1. Manage information flow between different sw components
	2. Avoid data redundancy
	3. Sub-system view
ERP's support all the processes in Porter's value chain using a modular approach:
- 1 Module --> 1 Process

#### ERP's characteristics:
1. Information uniqueness: single database for all modules, no redundancy or inconsistency.
2. Modularity: multiple (independent) modules, each module implements 1 process. We gain flexibility (add / remove modules at any time).
3. Prescriptivity (best practise): logic of the process defined by the modules.  Only for "supporting " processes.

### Modules selection:

**One-stop shopping:**
- All modules from the same provider.

**Best-of-the-breed:**
- Best module from each of the providers (additional effort for data sharing and integration).

---
**Total implementation:**
- Support all processes with ERP modules

**Partial implementation:**
- Combination of ERP modules, internal SW components and legacy SW.

### ERP Implementation

ERP is usually a COTS (commercial off the shelf) solution => buy

Steps:
1. Modules selection: 
	- Before using the modules we need to perform a parametrisation (mandatory) , as they are abstract. After it we obtain the implemented modules that are ready to use.
	- But in some specific cases we will need to do a personalization (optional and not always possible) of the modules so we have personalized modules 
