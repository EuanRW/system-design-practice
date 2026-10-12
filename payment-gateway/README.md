# Payment Gateway

## 1. Problem Statement

Design a scalable payment gateway that allows online merchants to accept payments from their customers using multiple payment methods such as credit/debit cards, bank transfers, and digital wallets.

The gateway should expose APIs that merchants can integrate into their applications to.

## 2. Functional Requirements

### Key stakeholders

Firstly, let's establish who interacts with the payment gateway.

**Merchant**
Integrates with the gateway, initiates payments, checks transaction status, issues refunds, and receives transaction reports.

**Customer**
Pays for goods or services using cards, bank transfers, or digital wallets.

**Payment Service Provider / Acquirer**
Processes payment requests and connects the gateway to card networks, banks, or other payment providers.

**Gateway Operations / Risk Team**
Monitors transactions, investigates suspicious activity, and manages disputes and operational issues.

### Key functional requirements

#### P0 - Must-have requirements
These define the minimum viable payment gateway.

FR1. Initiate a payment  
The gateway must expose an API that allows an authenticated merchant to initiate a payment for a specified amount and currency, linked to an order or transaction reference.

FR2. Support multiple payment methods  
The gateway must support credit/debit cards, bank transfers, and digital wallets through appropriate payment providers.

FR3. Process and authorize payments  
The gateway must route payment requests to the appropriate provider, handle authorization results, and record the transaction outcome.

FR4. Track payment status  
The gateway must maintain transaction states such as CREATED, PENDING, AUTHORIZED, SUCCEEDED, FAILED, and CANCELLED, as appropriate for the payment method.

FR5. Retrieve payment details  
Merchants must be able to retrieve a payment's status, amount, currency, timestamps, and relevant provider reference using a payment ID.

FR6. Prevent duplicate payment processing  
The gateway must support idempotency keys so that retries of the same payment request do not unintentionally charge the customer more than once.

FR7. Notify merchants of payment events  
The gateway must send webhooks for relevant events, such as payment success, failure, or a transition to a final state, and allow merchants to handle retries or duplicate notifications safely.

#### P1 - Important requirements

These make the gateway more complete and operationally useful.

FR8. Capture payments  
Capture previously authorized funds, either fully or partially if supported.

FR9. Refund payments  
Support full and partial refunds for eligible transactions.

FR10. Cancel or void payments
Cancel eligible payments or release an authorization before capture.

FR11. Handle asynchronous payments  
Process provider callbacks and delayed outcomes, particularly for bank transfers and some wallet flows.

FR12. Support customer authentication  
Support required authentication flows, such as 3-D Secure for eligible card payments.

FR13. Merchant management  
Register merchants and manage API credentials, configuration, and enabled payment methods.

FR14. Transaction history  
Allow merchants to list and filter payments by date, status, order reference, or payment method.

FR15. Reconcile transactions  
Compare gateway records with provider reports to identify missing, inconsistent, or unsettled transactions.

#### P2 - Advanced requirements

Add these if the service becomes a more mature platform.

- Fraud and risk management: Screen transactions against configurable risk rules and flag suspicious activity.
- Smart payment routing: Select providers based on payment method, country, cost, availability, or success rate.
- Dispute management: Track chargebacks and disputes and support the relevant evidence workflow.
- Payouts and settlement reporting: Track funds owed to merchants and report settlement progress.
- Multi-currency support: Process payments in supported currencies and handle currency-specific rules.
- Merchant analytics: Provide transaction volume, success rates, refund metrics, and payment method breakdowns.

## 3. Non-Functional Requirements

### Relevant questions to ask

- **What is the expected average and peak transaction volume?**

        "We expect around 1,000 payment requests per second on average, with peaks of 10,000 requests per second during major shopping events. The system should scale as our merchant base grows."

  **Average load**
  1,000 RPS  
  86.4 million requests/day if sustained  

  **Peak load**  
  10,000 RPS  
  10× average traffic  

  Assumption: these are incoming API requests, not necessarily unique payment attempts. Retries, status checks, refunds, and webhooks add workload.

- **What availability and latency SLOs does the business require?**

        "Our critical payment APIs should have 99.99% monthly availability. Payment creation should have a p99 gateway-processing latency below 200 ms, excluding external provider processing time. We want payment status updates to be reflected promptly, and merchants should receive webhooks quickly."

    | Metric | Agreed target |  
    |--------|---------------|
    | API availability | 99.99% monthly |
    | Payment creation latency | p99 below 200 ms, excluding provider latency |
    | Payment status lookup | p99 below 100 ms |
    | Internal processing of provider events | p99 below 1 second after receipt |
    | Webhook delivery | 99% within 5 seconds of the event being ready for delivery |

- **Which regions, currencies, and payment providers must be supported?**

        “We are launching in the UK and European Economic Area first. We need GBP and EUR support, with an architecture that can expand into North America later. Initially, we will integrate with two payment service providers to support cards and selected digital wallets, plus a provider for bank transfers.”

    Design implications:

    - Define a provider-independent payment interface.
    - Maintain provider-specific adapters and mappings for errors, statuses, and capabilities.
    - Store currency explicitly and represent monetary amounts safely, avoiding floating-point arithmetic.
    - Make routing configurable by country, payment method, currency, and provider health.
    - Keep regional deployment and data-residency requirements in mind.

- **What are the recovery expectations and applicable compliance constraints?**

        “We need to recover critical payment APIs within 15 minutes after a major infrastructure failure. We want near-zero loss of acknowledged payment records. We must protect cardholder data and meet applicable PCI DSS and privacy requirements.”

    | Requirement | Agreed assumption |
    |-------------|-------------------|
    | Recovery Time Objective (RTO) | 15 minutes |
    | Recovery Point Objective (RPO) | Near zero for acknowledged payment records |
    | Database recovery | Automated backups, replication, and regularly tested restoration |
    | Payment recovery | Reconcile in-flight transactions with providers before retrying uncertain operations |
    | Card security | Minimize card data storage and use tokenization where appropriate |
    | Access control | Strong merchant authentication and authorization |
    | Compliance | Applicable PCI DSS requirements and UK/EU data-protection obligations |

## 4. API Design

## 5. Back-of-the-Envelope Estimation

## 6. Data Model

## 7. High-Level Architecture

## 8. Detailed Design

## 9. Scaling Strategy

## 10. Reliability & Failure Handling

## 11. Security Considerations

## 12. Observability

## 13. Trade-offs

## 14. Future Improvements
