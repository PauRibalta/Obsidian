## 1. Waterfall Model

The **Waterfall model** defines a **linear sequence of predefined phases**, with no backtracking.

### Phases

**Feasibility → Requirements → Design → Implementation & Unit Testing → Integration & Testing → Deployment & Maintenance**

**Advantages: structured, predictable, documentation-heavy**

### Key characteristics

- Highly **structured** and predictable.
    
- Requires requirements to be **well-known and stable**.
    
- Uses **go/no-go decisions** between phases.
    
- Changes are difficult and expensive to accommodate.
    

### Main limitations

- Assumes **stable requirements**.
    
- Feedback arrives **late**.
    
- Change is difficult to manage.
    
- Backtracking and iterations are common in practice.
    
- Poor fit for **dynamic or uncertain domains**.
    

---

## 2. Deployment & Maintenance

### Deployment

Putting the application into operation and managing its different **installations and configurations**.

### Maintenance

Changes made **after delivery**.

> Software does not physically deteriorate. If a malfunction appears, the underlying cause was already present.

Maintenance can represent **more than 50% of total costs**.

### Types of Maintenance

| Type           | Purpose                                                                                 | Effort put into |
| -------------- | --------------------------------------------------------------------------------------- | --------------- |
| **Corrective** | Fix defects                                                                             | 20%             |
| **Adaptive**   | Adapt to changes in the operating environment                                           | 20%             |
| **Perfective** | Extend functionality / improve non-functional requirements                              | 50%             |
| **Preventive** | Improve the software to prevent future problems (e.g. refactoring). Used only sometimes |                 |

---

## 3. Cost of Delayed Corrections

> **The later an error is discovered, the more expensive it is to correct.**

Errors originating in **requirements** can be particularly expensive because many subsequent design and implementation decisions depend on them.

**Requirements (1) → Design (5) → Code (10) → Unit Test (20) → Acceptance Test (50) →   Maintenance (100)**

Earlier detection generally means **lower correction cost**.

---

## 4. Why Does Software Evolve?

Software changes because of:

- **Changes in context** → e.g. company mergers, regulations.
    
- **Changes in requirements** → new demands after a system version is released.
    
- **Wrong specifications** → incomplete or inaccurate requirements.
    
- **Requirements not initially known**.
    

> **Key principle:** anticipate change rather than simply reacting to it.

---

# 5. Agile

Agile emerged as a response to the **rigidity of Waterfall**.

### Agile values

- **Individuals & interactions** over processes & tools.
    
- **Working software** over comprehensive documentation.
    
- **Customer collaboration** over contract negotiation.
    
- **Responding to change** over following a plan.
    

### Main characteristics

- **Iterative**
    
- **Incremental**
    
- Frequent delivery of working software.
    
- Requirements can evolve throughout development.
    

---

## 6. Scrum

Scrum organizes development into short **sprints**, typically **2–4 weeks**.

### Roles

- **Product Owner**
    
- **Scrum Master**
    
- **Development Team**
    

### Workflow

**Product Backlog → Sprint Backlog → Product Increment**

- **Product Backlog:** prioritized work defined by the Product Owner.
    
- **Sprint Backlog:** tasks selected and expanded by the team for the sprint.
    
- **Increment:** potentially releasable product produced by the sprint.
    
- **Daily Scrum:** short meeting for team coordination.
    

> Requirements are **frozen during a sprint**, but can change **between sprints**.

![[Pasted image 20260917105643.png|532]]

---

# 7. DevOps

**DevOps = Development + Operations**

A development approach focused on **continuous implementation and delivery**.

### Main idea

Reduce the distance between **development and deployment**.

It promotes:

- Communication
    
- Collaboration
    
- Integration
    
- Automation
    
- Continuous Delivery
    
- Continuous monitoring
    

### DevOps team

A single team contains **heterogeneous skills** and is involved in both development and deployment.

### Main goal

Instead of delivering a packaged product to install:

> Deliver and maintain a **running, usable service**.

Strong focus on:

- Automated deployment
    
- Monitoring
    
- Continuous releases
    
- Feedback from operation back to development
    

---

# 8. Waterfall vs Agile vs DevOps

![[Pasted image 20260917110043.png]]



---

# 9. Software Quality Goals

Important quality attributes:

- **Correctness** → meets the specification.
    
- **Reliability** → performs consistently.
    
- **Maintainability** → easy to modify and fix.
    
- **Usability** → effective for intended users.
    
- **Performance**
    
- **Portability**
    
- **Interoperability**
    

---

# 10. Choosing a Life Cycle

The appropriate life cycle depends on the **context and product**.
### Waterfall

Useful when requirements are **stable** and high structure is required.
### Agile

Useful when requirements are **unclear or rapidly changing** and fast delivery is important.
### DevOps

Useful for **continuously delivered services**, especially cloud/SaaS applications.

---

# 11. Rapid Prototyping (Apendix)

Used when requirements are **unclear or changing**.

- **Throw-away prototype** → used to clarify requirements, then discarded.
    
- **Evolutionary prototype** → progressively refined into the final system.
    

**Purpose:** reduce the risk of misunderstanding requirements.

---

# 12. Verification vs Validation (Apendix)

### Verification

> **Does the software meet its specification?**

### Validation

> **Does the software meet user expectations?**

If the specification correctly represents user expectations, verification and validation should align.

---

# Exam Essentials

- **Waterfall** = linear, structured, stable requirements, difficult to change.
    
- **Agile** = iterative + incremental + responds to change.
    
- **Scrum** = short **sprints** + Product Owner + Scrum Master + Development Team.
    
- **Sprint** → requirements are frozen during the sprint but adaptable between sprints.
    
- **DevOps** = Development + Operations → continuous delivery, automation and monitoring.
    
- **Maintenance** is a major part of software costs.
    
- Maintenance types: **Corrective, Adaptive, Perfective, Preventive**.
    
- **The later an error is detected, the more expensive it is to fix.**
    
- Software evolves because of **context changes, requirement changes, wrong specifications and unknown requirements**.
    
- **Verification** = meets specification.
    
- **Validation** = meets user expectations.
    
- Life-cycle choice depends on the **context and product**.