# Common Architecture Terminologies

## Introduction
This document provides definitions and explanations of common terms used in software architecture. Understanding these terms is essential for effective communication and implementation of architectural concepts.

## A

### Abstraction
The process of hiding implementation details and showing only functionality to users. Abstraction lets you focus on what an object does instead of how it does it.

### Architecture Decision Record (ADR)
A document that captures an important architectural decision made along with its context and consequences.

### API (Application Programming Interface)
A set of definitions, protocols, and tools for building application software that specifies how software components should interact.

## B

### Backend
The server-side of an application that handles business logic, data processing, and database operations.

### Bounded Context
A central pattern in Domain-Driven Design that defines explicit boundaries within which a domain model exists.

### Bridge Pattern
A structural design pattern that decouples an abstraction from its implementation so that the two can vary independently.

## C

### CQRS (Command Query Responsibility Segregation)
An architectural pattern that separates read and update operations for a data store.

### Circuit Breaker
A design pattern used to detect failures and encapsulate logic to prevent a failure from constantly recurring.

### Coupling
The degree of interdependence between software modules; a measure of how closely connected two routines or modules are.

## D

### Database Sharding
A type of database partitioning that separates large databases into smaller, faster, more easily managed parts called shards.

### Dependency Injection
A technique in which an object receives other objects that it depends on, rather than creating them internally.

### Domain Model
A conceptual model of a domain that incorporates both behavior and data.

## E

### Event Sourcing
A way of storing application state by storing the sequence of events that led to that state.

### Eventual Consistency
A consistency model used in distributed computing that guarantees that, if no new updates are made to a given data item, eventually all accesses to that item will return the last updated value.

### Elasticity
The ability of a system to automatically scale up or down based on demand.

## F

### Facade Pattern
A structural design pattern that provides a simplified interface to a complex subsystem.

### Frontend
The client-side of an application that users interact with directly.

### Fallback
A backup operation that is performed when a primary operation fails.

## H

### High Availability
The ability of a system to operate continuously without failing for a designated period of time.

### Horizontal Scaling
The ability to increase capacity by connecting multiple hardware or software entities so that they work as a single logical unit.

### Hot Deployment
The ability to make changes to a running system without requiring a restart.

## I

### Idempotency
The property of certain operations whereby they can be applied multiple times without changing the result beyond the initial application.

### Interface
A shared boundary across which two or more separate components exchange information.

### Integration Pattern
A general, reusable solution to a commonly occurring problem in software integration.

## L

### Load Balancer
A device that distributes network or application traffic across multiple servers.

### Latency
The time delay between the cause and the effect of some physical change in the system being observed.

### Loose Coupling
A design goal that seeks to reduce the inter-dependencies between components of a system.

## M

### Microservices
An architectural style that structures an application as a collection of loosely coupled services.

### Message Queue
A form of asynchronous service-to-service communication used in serverless and microservices architectures.

### Modularity
The degree to which a system's components may be separated and recombined.

## N

### N-Tier Architecture
A client-server architecture where presentation, application processing, and data management functions are physically separated.

### Non-Functional Requirements
Requirements that specify criteria that can be used to judge the operation of a system, rather than specific behaviors.

### Node
A basic unit of a data structure, such as a linked list or tree data structure.

## O

### Orchestration
The automated configuration, coordination, and management of computer systems and software.

### OAuth
An open standard for access delegation, commonly used as a way for internet users to grant websites or applications access to their information.

### ORM (Object-Relational Mapping)
A programming technique for converting data between incompatible type systems using object-oriented programming languages.

## P

### Proxy Pattern
A structural pattern that provides a placeholder for another object to control access to it.

### Persistence Layer
The layer of an application responsible for storing and retrieving data.

### Pipeline
A set of data processing elements connected in series, where the output of one element is the input of the next one.

## R

### REST (Representational State Transfer)
An architectural style for providing standards between computer systems on the web.

### Redundancy
The inclusion of extra components that can be used in case of failure in the primary components.

### Registry Pattern
A pattern where a well-known object maintains references to other objects.

## S

### Service Discovery
The automatic detection of devices and services offered by these devices on a computer network.

### Scalability
The capability of a system to handle a growing amount of work, or its potential to be enlarged to accommodate that growth.

### Stateless
A server implementation where no session data is stored between requests.

## T

### Throughput
The amount of work that can be performed or the amount of data that can be transferred in a given time period.

### Transaction
A sequence of information exchange and related work that is treated as a unit for purposes of satisfying a request.

### Two-Phase Commit
A distributed algorithm that coordinates all the processes that participate in a distributed atomic transaction.

## V

### Vertical Scaling
Adding more power (CPU, RAM) to an existing server.

### Virtual Machine
An emulation of a computer system that provides the functionality of a physical computer.

### Version Control
The management of changes to documents, computer programs, and other collections of information.

## Conclusion
These terms form the foundation of software architecture vocabulary. Understanding them is crucial for effective communication and implementation of architectural concepts.

Note: This glossary is continuously updated as new terms and concepts emerge in the field of software architecture.
