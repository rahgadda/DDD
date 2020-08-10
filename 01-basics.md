# Basics

## Overview

### Strategic Design
- **Domain Driven Design**(DDD) is a collaboration between technical and domain experts.
- **Domain** is the world of the business we are working with and the problems we want to solve. DDD use domains to break apps into smaller areas according to their *business rules*, *processes* and *existing systems integrations*.
- A Domain is divided into **Sub Domain**, so each sub domains focus smaller problems. These are of three types:
  - **Core domains** are where the money is or primary business is; Example *Underwriting in Lending*.
  - **Supporting domains** support your core business; Example *Revaluation of asset in lending*.
  - **Generic domains** are the ones you need, but don't care a lot about, so you would probably buy them of the shelf; Example *Identity and Access* or *Reporting*.
  ![](./01-images/01-Domains.png)
- **Domain Model** is organized and structured knowledge of the problem. It is an object model of the domain that incorporates both behavior and data; Example *Identifying pricing in lending*.
- **Context** is the setting in which a word or statement appears that determines its meaning. Statements about a model can only be understood in a context; Example *policy has a different meaning in insurance vs identity domain*.
- **Bounded Context** is a logical boundary where a certain sub-domain makes sense, while the others don't. Each bounded context contains a domain model that represents a particular subdomain of the larger application. Example *Origination has Application and Collateral Details in Underwriting and Funding sub-domains and there is a logical boundary between these sub-domains*.
  - Relationships between bounded contexts is to classify the contexts as **upstream** and **downstream** contexts. These are managed using **supplier** and **customer** teams.
  ![](./01-images/02-BoundedContext.png)
  - **Open Host Service** (OHS) is a formal communication protocol (API) between two subsystems.
  - **Published Language** (PL) is a about publishing the API in a form that other teams can use to write clients.
  - **Anti-Corruption Layer** (ACL) lets the downstream team decides to create an abstraction layer that protects the downstream context from changes in the upstream context.
  - **Shared Kernel** is a common code base that is shared between both contexts. The kernel can be modified by any of the teams, but not without consulting the other team first.
  ![](./01-images/03-SharedKernel.png)
  - **Conformist context** are in an upstream-downstream relationship. Upstream team has no motivation to accommodate the downstream team’s needs than the downstream team decides to conform to the model of the upstream team, whatever it happens to be.
  - **Separate Ways** does not perform any integration at all. It depends on the philosophy of; when the benefit of the integration between two contexts is no longer worth the effort, it is better to cut the contexts loose from each other and let them evolve independently. In this, services provided by the upstream context that the downstream context actually used are re-implemented inside the downstream context.
- **Context Map** is designated as the primary tool used to make context boundaries explicit. The context map is responsible for defining an explicit boundary between bounded contexts and makes sure they have a right contact point.
- **Ubiquitous language** is a universal language in a given sub-domain that helps to communicate between software developers and domain experts to connect all the activities . *Event Storming* can be used to achieve a fast cycle of business process learning.

![](./01-images/04-StrategicDDD.png)

### Tactical Design
- Design is an iterative process, it starts with strategic design, followed by tactical design.
- Tactical design is much more hands-on and closer to the actual code; it deals with classes and modules.
- A **Value Object** is an object whose value is of importance.
  - Value objects are not only containers of data - they can also contain business logic.
  - These are immutable i.e. cannot be changed once they are created.
  - They do not have distinct identity, so only their properties can be used to distinguish between two instances.
  - Two Value Objects with the exact same properties can be considered equal.
  - For complex value objects, consider using the *builder* or *essence* pattern.
- An **Entity** is an object whose identity is of importance.
  - It is a simple entity in a domain model that can be related to a single row in a relational database that are differentiated by an id.
  - It can also contain business logic.
  - Every entity has a unique ID that is assigned when the entity is created and remains unchanged throughout the lifespan of the entity.
  - Entities are mutable i.e. can change attribute values once they are created except ID. Not all attribute are allowed to be changed; domain experts determine the attributes that can be changed.
- Value objects are easier to work with because they are immutable and small. Therefore, we should aim for a design with few entities and many value objects.
- It is not easy to model something as a value object or as an entity. 
  - In invoice system, the street address is just something you print on the invoice. In this street address is a Value Object.
  - In public utility system for gas line, the street address is an entity and it may even be split up into smaller entities like building or apartment.
- An **Aggregate** is a group of entities and value objects that have certain characteristics as given below:
  - The aggregate is created, retrieved and stored as a whole.
  - The aggregate is always in a consistent state. All state-altering operations are performed through the aggregate root.
  - The aggregate is owned by an entity called the aggregate root, whose ID is used to identify the aggregate itself.
  ![](/01-images/05-Aggregate.png)
  - An aggregate can be referenced from the outside through its root only.
  - Local entities cannot be referenced from outside the aggregate.
  - While referencing another aggregate directly, create a value object that wraps the ID of the aggregate root and use that as the reference.
  - To make changes to multiple aggregates use domain events to attain eventual consistency.
  ![](/01-images/06-AggregateCommunication.png)
- In an invoicing application, invoice is an aggregate containing invoice and item details.
- A **Domain Event** is anything that happens in the domain model that may be of interest to other parts of the system. 
  - Entity and Value Object are used to describe the static state.
  - Using Domain Events, business models will have ability to describe things that happen and change the state of the model.
  - Domain events can be coarse-grained i.e. specific aggregate root is created or a process is started
  - Domain events can be fine-grained i.e. particular attribute of a particular aggregate root is changed
  - Domain events have certain characteristics as given below:
    - They are immutable
    - They have a timestamp when the event in question occurred.
    - They may have a unique ID that helps to distinguish one event from another. 
    - They are published by aggregate roots or domain services.
    - Domain event publisher and listener belong to different transaction.
  - *Event sourcing* design pattern can be used to implement Domain events.