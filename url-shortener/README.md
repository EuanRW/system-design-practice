# url-shortener

## 1. Problem Statement

Design a URL shortening service that allows users to submit a long URL and receive a unique, compact short URL.

When a user visits the short URL, the system should efficiently redirect them to the original URL.

The system should be designed to handle a large number of stored URLs and significantly more redirect requests than URL creation requests, while remaining highly available, scalable, and low-latency.

## 2. Functional Requirements

- Create short URLs from long URLs
- Redirect short URLs to their original URLs
- Track basic click analytics
- Support URL expiration
- Optional: Support custom aliases

## 3. Non-Functional Requirements

- Low-latency redirects
- High availability
- Horizontal scalability
- Durable storage of URL mappings

## 4. Back-of-the-Envelope Estimation

### Assumptions

- 100M new URLs/month
- 100:1 redirect-to-create ratio
- Assume 5 years of stored URLs
- Assume traffic is evenly distributed for rough estimation
- Peak traffic 5x average traffic
- Average URL length is 100
- 1 ASCII character ≈ 1 byte
- Redirect response transfers 1 KB

### Write traffic

100M new URLs/month

`Seconds per month = 60 seconds * 60 minutes * 24 hrs * 30 days ≈ 2.6 million seconds`

Average writes per second:

`100,000,000 / 2,600,000 = ~ 38 writes/sec`

Assuming peak write traffic 5x average:

`~200 writes/sec`

### Read traffic

Assuming 100:1 redirect-to-create ratio:

`100x write traffic ≈ 3800 read/sec`

Peak read traffic:

`≈ 19,000 reads/sec`

### Storage

URLs created over 5 years:

`100M x 12 months x 5 years = 6 billion URLs`

Representative metadata record:

```
original_url = "https://example.com/products/..."
short_code   = "aB3xYz"
created_at   = timestamp
expires_at   = timestamp
user_id      = optional ID
```

```
Original URL       ~100 bytes
Short code           ~8 bytes
created_at           ~8 bytes
expires_at           ~8 bytes
user_id             ~16 bytes
flags/status         ~4 bytes
click_count          ~8 bytes
--------------------------------
Raw data total      ~252 bytes
```

1.5x safety factor: 252 x 1.5 = ~380 bytes per record

Total data size = 380 bytes * 6 billion = 2.28 TB

With database overhead, indexes, and replication, the actual storage requirement could be significantly higher.

### Bandwidth

3,800 requests/sec x 1 KB ≈ 3.8 MB/sec average

At peak traffic:

19,000 requests/sec x 1 KB ≈ 19 MB/sec peak

Write bandwidth is much smaller because URL creation traffic is significantly lower than redirect traffic.

## 5. API Design

### POST /urls

Creates a shortened URL.

Request:

```json
{
  "url": "https://example.com/some/long/path",
}
```

Response:

```json
{
  "shortUrl": "https://short.ly/aB3xYz"
}
```

Possible responses:

- 201 Created — short URL created successfully
- 400 Bad Request — invalid URL

### GET /{shortCode}

Returns original URL.

Example:

```http
GET /aB3XYz
```

Response:

```http
HTTP/1.1 302 Found
Location: https://example.com/some/long/path
```

Possible responses:

- 302 Found — redirect to the original URL
- 404 Not Found — short code does not exist
- 410 Gone — URL has expired or was deleted

Architechtural decision: A 302 redirect was chosen because it allows the service to retain control over future requests and collect click analytics. A 301 can be cached more aggressively by browsers and intermediaries, reducing load on the service but this caching potentially reduces analytics accuracy.

## 6. Data Model

A simple URL mapping record could contain:

### UrlMapping

| Field | Description |
| ---------------- | --------------------------------------- |
| `short_code` | Unique identifier used in the short URL |
| `original_url` | Destination URL |
| `created_at` | Creation timestamp |
| `expires_at` | Optional expiration timestamp |
| `user_id` | Optional owner of the URL |
| `click_count` | Optional cached/aggregated click count |

Example:

```text
short_code:   aB3xYz
original_url: https://example.com/some/long/path
created_at:   2026-08-22T18:00:00Z
expires_at:   2027-08-22T18:00:00Z
user_id:      12345
click_count:  105
```

## 7. High-Level Architecture

The system consists of a stateless URL service, a cache, and a persistent database.

```text
Client
  |
  v
Load Balancer
  |
  v
URL Service
  |
  +----> Cache
  |        |
  |       miss
  |        v
  +----> Database
  |
  +----> Analytics Queue
             |
             v
       Analytics Worker
```

## 8. Detailed Design

### URL Creation

1. Client sends a long URL to `POST /urls`.
2. The URL service generates a unique short code.
3. The mapping is stored in the database.
4. The shortened URL is returned to the client.

### URL Redirect

1. Client requests `GET /{shortCode}`.
2. The service checks the cache for the mapping.
3. On a cache miss, the mapping is fetched from the database and added to the cache.
4. The service returns a `302` redirect to the original URL.
5. A click event is sent asynchronously to the analytics queue.

### Short-Code Generation

Generate a unique identifier and encode it using Base62 (`0-9`, `a-z`, `A-Z`) to produce compact short codes.

The short code must be unique, and collisions should be detected and retried if necessary.

### Caching

Frequently accessed URL mappings are stored in a distributed cache to reduce database load and redirect latency.

A TTL can be used to prevent stale mappings from remaining cached indefinitely.

## 9. Scaling Strategy

- **Horizontal scaling**  
  Run multiple stateless URL service instances behind a load balancer.

- **Caching**  
  Store frequently accessed short-code mappings in a distributed cache to reduce database reads.

- **Database replication**  
  Use replicas to improve availability and distribute read traffic.

- **Sharding**  
  Partition URL mappings across multiple database nodes as the dataset grows, using the short code as the shard key.

- **Asynchronous analytics**  
  Send click events to a queue so analytics processing does not slow down redirects.

- **Hot URL handling**  
  Frequently accessed URLs should remain cached to prevent a single popular short code from overloading the database.
  
## 10. Reliability & Failure Handling

- **Service failures**  
  Run multiple stateless URL service instances so traffic can be routed away from unhealthy nodes.

- **Cache failures**  
  On a cache miss or cache outage, fall back to the database.

- **Database failures**  
  Use replication and automatic failover to maintain availability.

- **Analytics failures**  
  Buffer click events in a durable queue and retry failed processing asynchronously.

- **Duplicate requests**  
  Ensure short-code creation is idempotent where appropriate and enforce uniqueness in the database.

- **Expired or deleted URLs**  
  Return `410 Gone` for mappings that are no longer valid.

- **Monitoring**  
  Track error rates, redirect latency, cache hit rate, database health, and queue backlog.

## 11. Security Considerations

- **URL validation**  
  Validate submitted URLs and only allow supported schemes such as `http` and `https`.

- **Malicious links**  
  Detect or block URLs associated with phishing, malware, or other abuse.

- **Rate limiting**  
  Limit URL creation and redirect requests to reduce spam, scraping, and denial-of-service attacks.

- **Authentication & authorization**  
  Require authentication for account-specific features such as managing, deleting, or viewing analytics for URLs.

- **Unpredictable short codes**  
  Avoid easily guessable short codes where enumeration of private or unlisted links is a concern.

- **HTTPS**  
  Use HTTPS for all client communication.

- **Input protection**  
  Sanitize and validate user-controlled input to prevent injection and other application-layer attacks.

## 12. Observability

- **Latency**  
  Track redirect and URL creation latency.

- **Error rates**  
  Monitor failed redirects, failed URL creations, and server errors.

- **Cache metrics**  
  Track cache hit/miss ratio and cache availability.

- **Database health**  
  Monitor query latency, replication lag, storage usage, and failures.

- **Queue health**  
  Track analytics queue depth, processing latency, and failed events.

- **Traffic metrics**  
  Monitor request rate, popular URLs, and unusual traffic spikes.


## 13. Trade-offs

- **301 vs 302 redirects**  
  `301` reduces repeated traffic through caching, while `302` gives the service more control over analytics and destination changes.

- **SQL vs NoSQL**  
  SQL provides strong consistency and simpler constraints, while NoSQL/key-value stores can scale efficiently for short-code lookups.

- **Random codes vs generated IDs**  
  Random codes are harder to predict but require collision handling. Generated IDs guarantee uniqueness but can be more predictable.

- **Caching vs consistency**  
  Caching improves latency and reduces database load but can temporarily serve stale data.

- **Synchronous vs asynchronous analytics**  
  Asynchronous analytics keeps redirects fast but means analytics may be slightly delayed.


## 14. Future Improvements

- Custom short aliases
- User accounts and URL management
- Detailed analytics dashboards
- Geographic and device-level analytics
- QR code generation
- Link expiration and scheduled activation
- Abuse and spam detection
- Custom domains
- Ensuring no duplicate domains exist, return already created short code in these cases.
