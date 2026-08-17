# System Design Practice

A collection of system design exercises focused on designing scalable, reliable, and maintainable distributed systems.

The goal of this repository is to practice breaking down real-world products into their core requirements, APIs, data models, architecture, scaling strategies, and engineering trade-offs.

## Systems

| #  | System                                           | Key Concepts                                                            |
| -- | ------------------------------------------------ | ----------------------------------------------------------------------- |
| 01 | [URL Shortener](./url-shortener)                 | Hashing, caching, databases, redirects, read-heavy systems              |
| 02 | [Ride-Sharing Platform](./ride-sharing)          | Geospatial indexing, real-time location, matching, event-driven systems |
| 03 | [Chat Application](./chat-application)           | WebSockets, message delivery, presence, ordering, fan-out               |
| 04 | [Payment Gateway](./payment-gateway)             | Idempotency, transactions, ledgers, retries, consistency                |
| 05 | [Video Streaming](./video-streaming)             | CDN, object storage, transcoding, adaptive bitrate streaming            |
| 06 | [Order Management](./order-management)           | State machines, inventory, distributed transactions, event sourcing     |
| 07 | [Notification Platform](./notification-platform) | Queues, rate limiting, retries, prioritization, multi-channel delivery  |
| 08 | [Search Engine](./search-engine)                 | Crawling, indexing, ranking, inverted indexes, distributed search       |

## Approach

Each system design follows roughly the same process:

1. **Requirements**

   * Functional requirements
   * Non-functional requirements
   * Constraints and assumptions

2. **Capacity Estimation**

   * Users and traffic
   * Read/write throughput
   * Storage
   * Bandwidth

3. **API Design**

   * Core endpoints
   * Request/response models
   * Authentication and authorization

4. **Data Model**

   * Entities and relationships
   * Database choice
   * Indexes and partitioning strategy

5. **High-Level Design**

   * Core services
   * Data flow
   * Communication patterns
   * Architecture diagram

6. **Deep Dives**

   * Scaling bottlenecks
   * Caching
   * Replication
   * Sharding
   * Queues and asynchronous processing
   * Consistency and availability

7. **Reliability**

   * Failure scenarios
   * Retries and idempotency
   * Fault tolerance
   * Monitoring and observability

8. **Trade-offs**

   * Alternative approaches
   * Design decisions
   * CAP considerations
   * Cost vs. complexity

## Repository Structure

```text
system-design-practice/
├── README.md
├── url-shortener/
│   └── README.md
├── ride-sharing/
│   └── README.md
├── chat-application/
│   └── README.md
├── payment-gateway/
│   └── README.md
├── video-streaming/
│   └── README.md
├── order-management/
│   └── README.md
├── notification-platform/
│   └── README.md
└── search-engine/
    └── README.md
```

## Design Template

Each exercise can use the following structure:

```text
# System Name

## 1. Problem Statement
## 2. Functional Requirements
## 3. Non-Functional Requirements
## 4. Back-of-the-Envelope Estimation
## 5. API Design
## 6. Data Model
## 7. High-Level Architecture
## 8. Detailed Design
## 9. Scaling Strategy
## 10. Reliability & Failure Handling
## 11. Security Considerations
## 12. Observability
## 13. Trade-offs
## 14. Future Improvements
```

## What I'm Practicing

This repository is intended to strengthen understanding of:

* Horizontal and vertical scaling
* Load balancing
* Caching strategies
* SQL vs. NoSQL databases
* Database replication and sharding
* Consistency models
* Message queues and event streaming
* Distributed transactions
* Idempotency
* Rate limiting
* CDNs and object storage
* WebSockets and real-time communication
* Service discovery
* Fault tolerance
* Observability
* Security
* CAP theorem
* Architecture trade-offs

## Guiding Principle

There is rarely one perfect system design.

The focus of these exercises is not to reproduce the architecture of an existing company, but to understand **why a particular architecture makes sense given a set of requirements, constraints, and trade-offs**.

## Progress

* [ ] URL Shortener
* [ ] Ride-Sharing Platform
* [ ] Chat Application
* [ ] Payment Gateway
* [ ] Video Streaming
* [ ] Order Management
* [ ] Notification Platform
* [ ] Search Engine

## Contributing

This repository is primarily for learning and experimentation. Suggestions, alternative designs, and discussions around engineering trade-offs are welcome.

## License

This project is available for educational and personal use.
