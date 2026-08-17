# System Design Case Study Template

Use this structure for every system design case study in the repository.

## 1. Problem Statement

Briefly describe the system you are designing.

Include:

* What the product does
* Who uses it
* What the core user journey looks like
* What is explicitly out of scope

Example:

> Design a URL shortening service that allows users to create short links and redirect visitors to the original URL.

---

## 2. Requirements

### Functional Requirements

List the main capabilities the system must support.

Examples:

* Create a resource
* Retrieve a resource
* Update a resource
* Delete a resource
* Search
* Send or receive events
* Support real-time interactions

Prioritize the requirements that matter most to the design.

### Non-Functional Requirements

Define the system qualities that matter.

Examples:

* High availability
* Low latency
* Scalability
* Durability
* Consistency
* Fault tolerance
* Security
* Global availability

Where possible, define measurable targets.

Example:

```text
P99 read latency < 200 ms
99.99% availability
```

---

## 3. Assumptions and Constraints

State the assumptions used throughout the design.

Examples:

```text
100M monthly active users
10M daily active users
1B requests per day
Read/write ratio: 100:1
Data retention: 5 years
```

This prevents the design from becoming unnecessarily vague.

---

## 4. Back-of-the-Envelope Estimation

Estimate the scale of the system.

### Traffic

Calculate:

```text
Requests per second
Reads per second
Writes per second
Peak requests per second
```

### Storage

Estimate:

```text
Average record size
New records per day
Storage per year
Total retained storage
```

### Bandwidth

Where relevant, estimate:

```text
Ingress bandwidth
Egress bandwidth
Media bandwidth
```

The goal is not perfect numbers.

The goal is to understand the approximate order of magnitude.

---

## 5. API Design

Define the most important APIs.

Example:

```http
POST /api/v1/resources
GET /api/v1/resources/{id}
PUT /api/v1/resources/{id}
DELETE /api/v1/resources/{id}
```

Include sample requests and responses when useful.

Also consider:

* Authentication
* Authorization
* Pagination
* Idempotency
* Rate limiting
* Versioning

---

## 6. Data Model

Identify the core entities.

Example:

```text
User

id
name
email
created_at
```

```text
Resource

id
user_id
status
created_at
updated_at
```

Document:

* Primary keys
* Relationships
* Important indexes
* Unique constraints
* Partition keys

---

## 7. Database Choice

Explain what storage technology you would choose.

Possible options:

```text
Relational database
Key-value database
Document database
Wide-column database
Graph database
Search index
Object storage
Time-series database
```

Explain why the access patterns fit the database.

Also discuss alternatives.

---

## 8. High-Level Architecture

Create the first version of the architecture.

Example:

```text
Clients
   │
   ▼
API Gateway
   │
   ▼
Load Balancer
   │
   ▼
Application Services
   │
   ├── Cache
   │
   ├── Database
   │
   └── Message Queue
```

Focus initially on the main request and data flows.

Avoid optimizing everything immediately.

---

## 9. Core Flows

Walk through the most important flows step by step.

Examples:

### Write Flow

```text
Client
  │
  ▼
API
  │
  ▼
Service
  │
  ▼
Database
```

### Read Flow

```text
Client
  │
  ▼
Service
  │
  ▼
Cache
  │
  ├── Hit → return
  │
  └── Miss → database
```

For each case study, identify roughly 2–4 critical flows.

---

## 10. Component Deep Dives

Explore the most interesting parts of the system.

This section will vary significantly by case study.

Possible topics include:

* ID generation
* Matching algorithms
* Message ordering
* Payment processing
* Search indexing
* Video transcoding
* Geospatial queries
* Notification fan-out
* Inventory reservation

Explain how each critical component works internally.

---

## 11. Caching Strategy

Describe what should be cached.

Include:

```text
Cache keys
Cache values
TTL
Eviction policy
Invalidation strategy
```

Discuss potential issues such as:

* Cache stampedes
* Hot keys
* Stale data
* Cache penetration

---

## 12. Scaling Strategy

Explain how the system grows beyond a single machine.

Cover relevant strategies such as:

### Application Layer

```text
Stateless services
Horizontal scaling
Load balancing
Auto-scaling
```

### Database Layer

```text
Read replicas
Partitioning
Sharding
Replication
```

### Async Processing

```text
Message queues
Event streams
Background workers
```

---

## 13. Partitioning and Sharding

If the system becomes large enough, describe how data is partitioned.

Possible strategies:

```text
hash(user_id)
hash(resource_id)
geographic region
time range
tenant_id
```

Discuss:

* Shard selection
* Rebalancing
* Hot partitions
* Cross-shard queries

---

## 14. Consistency

Define the consistency requirements.

Ask:

* Which operations require strong consistency?
* Where is eventual consistency acceptable?
* What happens during replication lag?
* Can users observe stale data?

Discuss concepts such as:

```text
Strong consistency
Eventual consistency
Read-after-write consistency
Quorum reads/writes
```

---

## 15. Concurrency and Race Conditions

Identify operations that can happen simultaneously.

Examples:

```text
Two users reserving the same inventory
Two drivers accepting the same ride
Two payments using the same idempotency key
Two users claiming the same username
```

Explain how conflicts are prevented.

Possible mechanisms:

* Transactions
* Optimistic locking
* Pessimistic locking
* Compare-and-swap
* Unique constraints
* Distributed locks

---

## 16. Reliability and Failure Handling

Identify likely failure scenarios.

Examples:

```text
Database unavailable
Cache unavailable
Queue unavailable
Worker crashes
Third-party API times out
Network partition
Entire region fails
```

For each critical failure, explain how the system responds.

Cover where relevant:

* Retries
* Exponential backoff
* Timeouts
* Circuit breakers
* Dead-letter queues
* Replication
* Graceful degradation

---

## 17. Idempotency

For operations that may be retried, explain how duplicate requests are handled.

Important for systems involving:

* Payments
* Orders
* Notifications
* Job processing
* Webhooks

Example:

```text
Idempotency-Key: 3f46c...
```

The same request should not accidentally execute twice.

---

## 18. Security

Cover the major security considerations.

Examples:

* Authentication
* Authorization
* Encryption
* Secrets management
* Input validation
* Abuse prevention
* Rate limiting
* DDoS protection
* Sensitive data handling
* Audit logging

The depth of this section should depend on the system.

---

## 19. Observability

Define how the system will be monitored.

### Metrics

Examples:

```text
Requests per second
Error rate
P50 / P95 / P99 latency
CPU usage
Database latency
Cache hit ratio
Queue depth
Consumer lag
```

### Logs

Include important application and audit events.

### Tracing

Use distributed tracing for requests spanning multiple services.

---

## 20. Bottlenecks

Identify where the design is most likely to fail as traffic increases.

Examples:

```text
Database write throughput
Hot partitions
Cache hot keys
Message queue lag
External API limits
Network bandwidth
```

Explain how each bottleneck could be mitigated.

---

## 21. Multi-Region Design

For globally distributed systems, discuss:

```text
Regional deployments
Traffic routing
Replication
Failover
Data locality
Consistency across regions
```

Consider what happens if an entire region goes offline.

---

## 22. Trade-offs

Document important design decisions.

Use a structure like:

### Decision

Use Redis for frequently accessed records.

### Benefits

```text
Low latency
Reduces database load
Easy horizontal scaling
```

### Drawbacks

```text
Additional infrastructure
Cache invalidation complexity
Potential stale data
```

The goal is to explain **why** you chose an approach rather than simply listing technologies.

---

## 23. Evolution of the Architecture

Show how the system might evolve as traffic grows.

### Stage 1 — Small Scale

```text
Application
    │
    ▼
Database
```

### Stage 2 — Growing

```text
Load Balancer
     │
Application Servers
     │
     ├── Cache
     └── Database
```

### Stage 3 — Large Scale

```text
CDN
 │
Load Balancer
 │
Services
 │
 ├── Distributed Cache
 ├── Sharded Database
 ├── Event Stream
 └── Worker Fleet
```

This section helps demonstrate that architecture should evolve with requirements rather than start unnecessarily complex.

---

## 24. Final Architecture

Present the final architecture after incorporating the major scaling and reliability decisions.

```text
Clients
   │
   ▼
CDN / Edge
   │
   ▼
API Gateway
   │
   ▼
Load Balancer
   │
   ▼
Application Services
   │
   ├── Cache
   │
   ├── Database
   │
   ├── Message Queue
   │
   └── Object Storage
```

Explain the role of each major component.

---

## 25. Interview Follow-Up Questions

Finish each case study with questions that could extend the design.

Examples:

* What happens at 10× the traffic?
* What is the largest bottleneck?
* How would you make this multi-region?
* What happens if the database goes down?
* How would you reduce latency?
* Which component would you shard first?
* What consistency guarantees do users need?
* How would you handle a sudden traffic spike?
* How would you reduce infrastructure cost?
* What would you redesign if scale increased by 100×?

---

## 26. Key Concepts Practiced

Finish with the specific concepts exercised by the design.

Example:

```text
Caching
Sharding
Replication
Distributed transactions
Event-driven architecture
Rate limiting
Idempotency
Load balancing
Fault tolerance
Observability
Consistency
```

---

## Recommended Order During an Interview

You do not necessarily need to discuss every section in an interview.

A practical order is:

```text
1. Clarify requirements
        ↓
2. Estimate scale
        ↓
3. Define APIs
        ↓
4. Define data model
        ↓
5. Draw high-level architecture
        ↓
6. Walk through core flows
        ↓
7. Identify bottlenecks
        ↓
8. Deep dive into critical components
        ↓
9. Scale the architecture
        ↓
10. Discuss reliability
        ↓
11. Discuss trade-offs
```

The most important habit is to continuously connect architecture decisions back to **requirements, scale, access patterns, and trade-offs**.
