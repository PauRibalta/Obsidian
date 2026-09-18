## 1. Software Development Process

> **Software Process:** the set of activities used to develop, deploy and maintain software.

> **Software Lifecycle:** describes how these activities are organized over time.

Main lifecycle examples:

- **Waterfall**
    
- **Agile**
    
- **DevOps**
    

The lifecycle should be **designed according to the specific case**:

- **XP** → small/medium teams with vague or rapidly changing requirements.
    
- **Critical applications** → require a more structured approach.
    
- **DevOps** → suitable for software offered **as a service**.
    

---

# 2. Waterfall Model

> **Waterfall Model:** a linear software lifecycle where development progresses through predefined phases with **no backtracking**.

Proposed in **1970**.

### Phases

**Feasibility → Requirements → Design → Implementation & Unit Testing → Integration & System Testing → Deployment → Maintenance**

Each phase ends with a **go/no-go decision**.

### Feasibility

Determines whether the project should proceed.

Includes:

- **Buy vs. make**
    
- Cost-benefit analysis
    
- Alternatives
    
- Costs and resources
    
- Feasibility document
    

### Requirements Analysis & Specification

- Analyse the **domain**
    
- Identify requirements
    
- Derive the **specification**
    
- Identify stakeholders
    

### Design

Defines:

- System **architecture**
    
- Components/modules
    
- Relations between components
    
- Responsibilities
    

→ Produces a **system design document**.

### Implementation & Unit Testing

- Each elementary module is implemented.
    
- Each module is tested by the developer.
    
- Programs should include documentation.
    

### Integration & System Testing

- Modules are integrated into subsystems.
    
- Subsystems are tested.
    
- The complete system is tested for overall properties.
    
- Example: **response time**.
    
- Alpha/beta testing may be used.
    

### Rough effort distribution

> **Very rough estimate:**

- **40%** → Requirements + Design
    
- **30%** → Implementation
    
- **30%** → Testing
    

The distribution can vary significantly between projects.

### Deployment

> Distribute the application and manage its **installation and configuration**.

### Maintenance

> Changes made to software **after delivery**.

Software does not physically deteriorate → maintenance is an unfortunate term.

A malfunction is usually caused by something already present in the software.

**Maintenance represents >50% of total costs.**

|Type|Purpose|
|---|---|
|**Corrective**|Fix errors|
|**Adaptive**|Adapt to environmental changes|
|**Perfective**|Add functionality or improve non-functional properties|
|**Preventive**|Reduce future problems|

Approximate distribution:

- Corrective → **20%**
    
- Adaptive → **20%**
    
- Perfective → **50%**
    

---

# 3. Cost of Delayed Corrections

The later an error is detected, the more expensive it is to correct.

Approximate relative cost:

**Requirements (1) → Design (5) → Code (10) → Unit Test (20) → Acceptance Test (50) → Maintenance (100)**

→ Important reason for detecting problems **as early as possible**.

---

# 4. Why Software Evolves

Software changes because:

- **Context changes**
    
    - e.g. mergers, new regulations such as GDPR.
        
- **Requirements change**
    
    - User/business needs evolve.
        
- **Specifications may be wrong**
    
    - Domain may have been misunderstood.
        
- **Requirements may initially be unknown**
    
    - Some needs only become clear during development/use.
        

A cited European company survey found that around **20% of user requirements became obsolete after one year**.

> **Goal:** anticipate change instead of simply suffering it.

---

# 5. Waterfall Limitations

Waterfall works best when:

- The domain is **perfectly understood**.
    
- Requirements are **known and stable**.
    

This situation is **rare**.

Main problem:

- Changes discovered late are expensive.
    
- Iterations are often necessary.
    

→ Software should be designed to support **cheap and reliable change**.

---

# 6. Agile Lifecycle

> **Agile:** lifecycle focused on adapting to change through **iterative and incremental development**.

Main characteristics:

- Adaptation to changing requirements.
    
- Successive releases.
    
- Incremental development.
    
- Continuous feedback.
    
- Working software delivered progressively.
    

Examples:

- **Scrum**
    
- **XP (Extreme Programming)**
    

---

# 7. Scrum

> **Scrum:** iterative and incremental Agile framework.

### Roles

- **Scrum Master**
    
- **Product Owner**
    
- **Development Team** (~7 people)
    

### Sprint

A **Sprint** produces a potentially releasable product increment.

According to the course:

- Sprint duration: **4–7 weeks**.
    

### Workflow

**Product Backlog → Sprint Backlog → Product Increment**

- **Product Backlog**
    
    - Prioritized requirements/features.
        
    - Managed by the **Product Owner**.
        
- **Sprint Backlog**
    
    - Tasks selected and expanded by the team.
        
- During a Sprint, the backlog is **frozen**.
    
- Unsatisfied requirements return to the Product Backlog.
    

**Daily Scrum** → team coordination during the Sprint.

---

# 8. Extreme Programming (XP)

XP focuses on:

- **Time-to-market**
    
- Continuous adjustment
    
- Software that cannot be completely specified in advance.
    

Important practices:

### Test First

Testing is central to development.

### Pair Programming

Two developers work together on the same code.

### Continuous Refactoring

Continuously improve the internal structure of the code.

→ Helps avoid uncontrolled **code-and-fix** development.

---

# 9. DevOps

> **DevOps = Development + Operations**

Traditional separation:

**Developers**

- Design
    
- Implementation
    
- Maintenance
    
- Testing
    

**Operations**

- Installation
    
- Service operation
    
- Stability
    

DevOps reduces this separation through:

- Communication
    
- Collaboration
    
- Integration
    
- Continuous testing
    
- Continuous monitoring
    
- Quality assurance
    
- Automation
    

### Main idea

Development and Operations work as a **single heterogeneous team**.

Focus on:

- Continuous delivery
    
- Automated deployment
    
- Monitoring
    
- Easy and frequent releases
    
- Checking that the software operates correctly
    

> The output is not only software → it is a **service that is installed, functioning and usable**.

Especially relevant for **software as a service**.

---

# 10. Waterfall vs Agile vs DevOps

| Waterfall    | Agile                          | DevOps                      |                          |
| ------------ | ------------------------------ | --------------------------- | ------------------------ |
| Development  | Linear                         | Iterative/incremental       | Continuous               |
| Requirements | Stable                         | Can change                  | Continuously adapted     |
| Releases     | Mainly at end                  | Successive increments       | Continuous               |
| Main focus   | Structured process             | Adaptation to change        | Development + Operations |
| Suitable for | Well-known/stable requirements | Changing/vague requirements | Software as a service    |

> Lifecycle choice depends on the **specific product and organization**.

---

# 11. Process vs Product

> **Product = WHAT** is produced.  
> **Process = HOW** it is produced.

Both are important.

Both have **quality**.

> **Process quality influences product quality.**

---

# 12. Software Quality

Quality can be considered at different levels:

### Internal Quality

Related to **how the software is internally structured**.

### External Quality

Related to qualities **perceived by the user**.

**Process → Product**

The quality of the development process can influence the quality of the final product.

---

## 12.1 Correctness

> A software is **correct** if it satisfies its **specifications**.

If specifications are formal:

- Correctness can potentially be verified **mathematically**.
    
- It can be proved as a theorem.
    
- Or falsified through **counterexamples/testing**.
    

Correctness is defined as:

- **YES / NO**
    
- There is no formal concept of a "degree of correctness".
    
- However, violations can have different **seriousness**.
    

 If the specification itself is wrong:  
→ **Verification ≠ validation**.

---

## 12.2 Reliability

> **Reliability:** probability of having **no malfunction during a certain period of time**.

Informally:

> The user can **trust the software**.

---

## 12.3 Robustness

> **Robustness:** software behaves **reasonably under unexpected circumstances**.

Examples:

- Incorrect input.
    
- Hardware malfunctions.
    

---

## 12.4 Performance

> **Performance:** efficient use of resources.

Resources include:

- Memory
    
- Processors
    
- Network bandwidth
    

Can be evaluated through:

- **Complexity analysis**
    
- Performance evaluation
    
- Simulation/model-based evaluation
    

Performance can affect:

- **Scalability**
    
- **Usability**
    

> **Scalability:** the solution continues to work when the scale of relevant characteristics changes.

Example:

- Small local network → Internet-scale system.
    

---

## 12.5 Usability

> **Usability:** expected users find the software **easy to use**.

Important:

- Define the **expected users**.
    
- Different users may have different usability requirements.
    

Related terms:

- **Ergonomic**
    
- **User-friendly**
    

Often related to the **user interface**:

- Textual interface
    
- Graphical interface
    

Usability is:

- Largely **subjective**
    
- Difficult to evaluate precisely.
    

---

## 12.6 Other Quality Attributes

### Maintainability

Ease of modifying and maintaining the software.

### Reusability

Ability to reuse software components in other contexts.

### Portability

Ability to adapt software to different **target environments**.

### Interoperability

Ability to **coexist and cooperate with other applications**.

---

# 13. Productivity

> **Productivity:** quantity produced per unit of **effort**.

### Effort

Measured in:

> **Person-month (pm)**

**People and months are NOT interchangeable.**

Adding more people does not necessarily reduce development time proportionally.

### Measuring quantity

Possible measures:

- **Lines of code**
    
- Variations in lines of code
    
- **Function points**
    

---

# 14. Productivity Data

Example from the course:

**135 Hewlett Packard projects**

(excluding requirements analysis)

→ **350 NCSS/person-month**

Where:

> **NCSS = Non-Comment Source Statements**

> **pm = person-month**

Important:

- Extremely large variation between individuals.
    
- Extremely large variation due to the **group effect**.
    

### Brooks' Law

> **"Adding people to a late project makes the project late."**

→ Adding people to an already delayed project can introduce additional coordination/communication effort.

---

# 15. Lifecycle Selection

There is **no single lifecycle suitable for every project**.

|Situation|Appropriate characteristics|
|---|---|
|Stable, well-known requirements|Structured approach|
|Vague/changing requirements|Agile / XP|
|Small/medium team|XP can be suitable|
|Critical application|More structured approach|
|Software as a service|DevOps can be suitable|

> **The lifecycle must be designed according to the specific case.**

---

# 16. Verification vs Validation

### Verification

> Does the software satisfy its **specifications**?

### Validation

> Does the software satisfy the **client's expectations**?

If the specifications correctly capture the client's expectations:

**Verification ≈ Validation**

If the specification is wrong:

**Correct software ≠ useful software**

---

# 17. Rapid Prototyping

A **prototype** is an early version of a system used to explore:

- Requirements
    
- Design
    
- Feasibility
    
- User interaction
    

Useful when requirements are unclear.

---

# Exam Essentials

- **Waterfall:** linear, predefined phases, no backtracking.
    
- Waterfall proposed in **1970**.
    
- Waterfall is suitable mainly when requirements are **known and stable**.
    
- Waterfall phases: **Feasibility → Requirements → Design → Implementation & Unit Testing → Integration & System Testing → Deployment → Maintenance**.
    
- Rough effort: **40% Requirements + Design / 30% Implementation / 30% Testing**.
    
- Maintenance accounts for **>50% of total costs**.
    
- Main maintenance types: **corrective, adaptive, perfective, preventive**.
    
- Software evolves because **context, requirements, specifications and knowledge change**.
    
- **Agile:** iterative + incremental + adaptation to change.
    
- **Scrum:** Product Backlog → Sprint Backlog → Product Increment.
    
- Scrum roles: **Scrum Master, Product Owner, Team**.
    
- Course Sprint duration: **4–7 weeks**.
    
- **XP:** Test First, Pair Programming, Continuous Refactoring.
    
- **DevOps = Development + Operations**, emphasizing collaboration, automation, continuous delivery and monitoring.
    
- **Product = WHAT; Process = HOW.**
    
- **Process quality influences product quality.**
    
- **Internal quality** → internal structure.
    
- **External quality** → user-perceived quality.
    
- **Correctness** → satisfies specifications.
    
- **Reliability** → probability of no malfunction for a given period.
    
- **Robustness** → reasonable behaviour under unexpected circumstances.
    
- **Performance** → efficient resource usage; affects scalability/usability.
    
- **Usability** → ease of use for expected users.
    
- Other qualities: **maintainability, reusability, portability, interoperability**.
    
- **Productivity = quantity produced / effort**.
    
- Effort measured in **person-months**.
    
- **People and months are not interchangeable.**
    
- Productivity can be measured using **LOC/NCSS or function points**.
    
- **Brooks' Law:** adding people to a late project makes the project late.
    
- **Verification:** satisfies specifications.
    
- **Validation:** satisfies client expectations.
    
- **Lifecycle choice depends on the specific product and organization.**
