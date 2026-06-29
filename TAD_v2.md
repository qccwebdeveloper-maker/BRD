# eB2BMart Portal — Technical Architecture Document (TAD)

**Version:** 2.0
**Status:** Draft for Review
**Date:** 26 June 2026
**Prepared by:** Engineering / Solution Architecture
**Source References:** eB2BMart Portal – BRD v3.0, FRD v1.x

---

## 1. Document Control

### 1.1 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 26-Jun-2026 | Architecture Team | Initial TAD derived from BRD v3.0. Defined target stack, architecture, data model (PostgreSQL), integrations, security, infrastructure, and phased delivery. |
| 2.0 | 26-Jun-2026 | Architecture Team | **Changed primary datastore from PostgreSQL to MongoDB** (document model). Reworked data architecture to collections/embedding, ODM (Mongoose), multi-document transactions, and **Atlas Search** in place of Postgres FTS. Updated infrastructure to MongoDB Atlas, and explicitly named cross-cutting infra services (Secrets Manager, API Gateway, Load Balancer). |

### 1.2 Approvals

| Name | Role | Decision | Date |
|------|------|----------|------|
| _TBD_ | Technical Lead / Architect | Pending | |
| _TBD_ | Engineering Manager | Pending | |
| _TBD_ | Product Manager | Pending | |
| _TBD_ | Security / Compliance | Pending | |

### 1.3 Purpose & Audience

This TAD describes **how the eB2BMart platform will be built** to satisfy the business requirements in **BRD v3.0**. Where the BRD answers *what* and *why*, this document answers *how* at a technical level.

**Audience:** engineering team, DevOps, QA, security reviewers, and technical stakeholders.

### 1.4 Scope

The architecture covers all three portals (Seller, Buyer, Admin) and shared platform services. It delivers the BRD's **Phase 1 (Core MVP)** first, with **Phase 2 (Enhancements)** built on the same foundations without re-platforming. Future Enhancements (FE-1…FE-13) influence extensibility decisions but are not fully designed here.

> **Datastore note (v2):** This version adopts **MongoDB** as the primary operational database. Because several core domains are financial and transactional (packages, invoices, lead-credit balances, e-NACH mandates), this document specifies explicit mitigations — **multi-document ACID transactions**, **schema validation**, and careful **embedding-vs-referencing** modelling — so document storage does not weaken data integrity. See §6 and §16.

### 1.5 Reference Documents

| Ref | Document |
|-----|----------|
| R1 | eB2BMart Portal — BRD v3.0 |
| R2 | eB2BMart Portal — FRD |
| R3 | Phasing map — BRD §5.2 (Module → Phase) |
| R4 | TAD v1.0 (PostgreSQL baseline, superseded by this datastore decision) |

---

## 2. Architectural Goals & Constraints

Derived from BRD §12 (Non-Functional Expectations) and §6.4 (Constraints).

### 2.1 Architectural Goals
- **Phased delivery:** ship Phase 1 quickly; add Phase 2 without rework. Favour a **modular monolith** with clean module boundaries over premature microservices.
- **Marketplace scale:** support growth in sellers, products, and lead volume; the lead engine and search must remain responsive under load.
- **Trust & compliance:** secure handling of PII, GST/PAN, and payment/e-NACH data; full auditability of sensitive actions.
- **Discoverability (SEO):** buyer-facing product/supplier pages must be crawlable — server-side rendering for public pages.
- **Operability:** role-based admin tooling, observability, and safe deployments.

### 2.2 Key Constraints
- **India-first:** GST/PAN/CIN handling, INR currency, e-NACH (Phase 2) and an India-capable payment gateway are mandatory.
- **Geography model:** Country → State → City hierarchy throughout (dealing areas, addresses, lead targeting).
- **Business rules in the engine:** package-bound lead limits, Product Quality Score ≥ 70% for Featured, 7-day trash purge, admin moderation gates.
- **Scale assumption:** target sizing of the order of **~10k active users** with headroom; architecture must scale horizontally beyond that without redesign.
- **Datastore integrity:** financial/transactional flows must remain consistent on a document database (handled via MongoDB transactions on a replica set — see §6.4).

---

## 3. Architecture Overview

### 3.1 Architectural Style

A **modular-monolith backend** exposing REST APIs to three separate web front-ends, with **asynchronous workers** for lead distribution, notifications, scheduled jobs, and report generation. This balances delivery speed (one deployable core) with clear seams that can later be extracted into services.

### 3.2 Logical Architecture

```
                         ┌──────────────────────────────────────────────┐
                         │                 Clients (Web)                 │
                         │  Seller Portal   Buyer Portal   Admin Portal  │
                         │   (Next.js)       (Next.js)      (Next.js)    │
                         └───────────────┬──────────────────────────────┘
                                         │ HTTPS / REST (JSON)
                              ┌──────────▼───────────┐
                              │  Load Balancer (ALB) │  TLS, health checks
                              └──────────┬───────────┘
                              ┌──────────▼───────────┐
                              │  API Gateway / Edge  │  authN, rate-limit, routing
                              └──────────┬───────────┘
                                         │
                      ┌──────────────────▼───────────────────┐
                      │        Application Core (API)         │
                      │        NestJS modular monolith        │
                      │                                       │
                      │  Identity & RBAC │ Seller │ Buyer     │
                      │  Product/Catalog │ Lead Engine        │
                      │  Packages/Billing│ Reviews/Q&A (P2)   │
                      │  Notifications   │ Support  │ CMS (P2)│
                      │  Reporting (P2)  │ Admin/Moderation   │
                      └───┬─────────┬─────────┬────────┬──────┘
                          │         │         │        │
                ┌─────────▼──┐ ┌────▼────┐ ┌──▼─────┐ ┌▼──────────────┐
                │  MongoDB   │ │  Redis  │ │ Object │ │  Atlas Search │
                │  (Atlas,   │ │ cache + │ │ Store  │ │  (P1 text) /  │
                │  replica   │ │ queues  │ │ (S3)   │ │  OpenSearch   │
                │  set, OLTP)│ └────┬────┘ └────────┘ │  (P2 option)  │
                └─────┬──────┘      │                 └───────────────┘
                      │   ┌─────────▼──────────┐
                  Secrets│  │  Async Workers     │  lead distribution,
                  Manager│  │  (BullMQ consumers) │  notifications, jobs,
                      │   └─────────┬──────────┘  scheduled purges, reports
                      │             │
                 ┌────▼─────────────▼────────────────────┐
                 │        External Integrations           │
                 │  Payment GW (Razorpay) │ e-NACH (P2)    │
                 │  Email (SES/SMTP) │ SMS GW │ Push (P2)  │
                 └────────────────────────────────────────┘
```

### 3.3 Portals (Front-end Applications)
- **Seller**, **Buyer**, **Admin** portals are three Next.js apps sharing a common component/design library and API client.
- Public buyer-facing pages (product/supplier listings, CMS pages) are **SSR/SSG** for SEO; authenticated dashboards are client-rendered.

---

## 4. Technology Stack

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Web front-end | **Next.js (React) + TypeScript**, Tailwind CSS, shared UI library | SSR/SSG for SEO; one ecosystem across three portals |
| API / backend | **Node.js + NestJS (TypeScript)** | Modular boundaries in a monolith; same language as front-end; strong validation/DI |
| **Primary database** | **MongoDB 6+ (Atlas managed, replica set)** | Flexible document model for product/catalogue and evolving schemas; native JSON; horizontal scale via sharding; **multi-document ACID transactions** for billing/lead integrity |
| ODM | **Mongoose (TypeScript)** | Schema definitions, validation, hooks, and typed models over MongoDB |
| Cache / queue | **Redis** + **BullMQ** | Caching, sessions, rate limiting, durable background jobs |
| Object storage | **S3-compatible** (AWS S3 / MinIO) | Product images, documents, invoices; signed URLs |
| Search | **MongoDB Atlas Search (Phase 1)** → **OpenSearch (Phase 2, optional)** | Atlas Search (Lucene) gives full-text + relevance natively in the DB; move to OpenSearch only if needed at scale |
| Auth | **JWT access + refresh tokens**, argon2/bcrypt hashing, RBAC | Stateless API auth; refresh rotation; role/permission claims |
| Secrets | **AWS Secrets Manager** (or SSM Parameter Store) | Encrypted storage + rotation of DB URIs, gateway keys, JWT signing keys |
| API gateway / edge | **ALB + in-app gateway (NestJS guards + Redis rate-limit)**; managed API Gateway optional | Routing, authN, throttling at the edge |
| Load balancer | **AWS Application Load Balancer (ALB)** | TLS termination, health checks, horizontal autoscaling, zero-downtime deploys |
| Payments | **Razorpay** (cards/UPI/netbanking + **e-NACH/eMandate** in Phase 2) | India-first; recurring mandates; GST invoicing |
| Email | **Amazon SES** (or SMTP) | Transactional email |
| SMS / Push | SMS gateway (MSG91/Twilio); Web/Mobile push | Phase 2 (BR-T, FE-3) |
| Containerisation | **Docker** | Reproducible builds |
| Hosting / orchestration | **AWS** (ECS Fargate or EKS); **MongoDB Atlas**; ElastiCache (Redis) | Managed services reduce ops burden at 10k scale |
| CI/CD | GitHub Actions | Build, test, scan, deploy |
| IaC | Terraform | Reproducible infrastructure |
| Observability | OpenTelemetry + centralised logs/metrics/traces | Operability and audit |

### 4.2 Key Technology Decisions (with alternatives)

| Decision | Chosen | Alternative considered | Why chosen |
|----------|--------|------------------------|------------|
| Backend shape | Modular monolith | Microservices | Faster Phase 1; lower ops cost; seams allow later extraction |
| **Primary DB** | **MongoDB** | PostgreSQL | Per project direction — flexible document/catalogue model, native JSON, easy horizontal scale; integrity preserved via multi-document transactions + schema validation |
| Search (P1) | Atlas Search | OpenSearch from day 1 | Built into Atlas — full-text + relevance without separate infra |
| Front-end | Next.js (SSR) | SPA (CSR-only) | SEO is critical for B2B discovery |
| Payments | Razorpay | Stripe / PayU | Native India support incl. e-NACH eMandate + GST invoicing |
| API edge | ALB + in-app gateway | AWS API Gateway service | Monolith doesn't need per-route managed gateway yet; cheaper, simpler |

> **Integrity caveat:** MongoDB is the chosen store, but the financial/relational nature of billing and lead allocation means we lean on **transactions, referential discipline in code, and Mongoose schema validation** to compensate for the lack of DB-enforced foreign keys. See §6.4 and §16.

---

## 5. System Components (Modules → BRD Mapping)

| Module | Responsibilities | BRD Ref | Phase |
|--------|------------------|---------|:-----:|
| **Identity & Access (RBAC)** | Registration, login, JWT/refresh, password, roles & permissions, admin user mgmt, login history | BR-S1, BR-A2 | P1 |
| **Seller Account** | Company/address/contact profile, GST/PAN, business profile, settings, credentials | BR-S1 | P1 |
| **Seller Admin Mgmt** | Admin view/edit sellers, Data Bank + export; activate/deactivate/block/flag, callbacks, dealing-area mgmt | BR-S2,S3 (P1) / BR-S4,S5,S6 (P2) | P1 + P2 |
| **Product / Catalog** | CRUD products + attributes/media, **Quality Score** engine, trash (7-day purge), dealing areas, categories, product types | BR-P1–P4 | P1 |
| **Featured Listings** | Promote products to Featured; admin featured approval | BR-P5,P6 | P2 |
| **Lead Engine** | Capture enquiries → leads, distribution by package + rules, seller lead views, manual assignment | BR-L1–L4 | P1 |
| **Lead Monetisation** | Buy Leads/credits, lead status tracking (distributed-count), call enquiries, conversion data | BR-L5–L7 | P2 |
| **Buyer** | Dashboard, profile, wishlist, search & enquiry submission | BR-B1–B3 | P1 |
| **Report Seller** | Buyer reports + complaint routing to moderation | BR-B4 | P2 |
| **Packages & Billing** | Package master/active/trial, quotations, invoices, payment verification | BR-K1–K3 | P1 |
| **e-NACH / Recurring** | Mandate creation/monitoring, authorisation status | BR-K4 | P2 |
| **Reviews & Q&A** | Buyer reviews/ratings & questions; seller responses; admin moderation | BR-T3,T4 | P2 |
| **Notifications** | Multi-event notifications (seller + buyer), templates, delivery; seller-response events | BR-T1 (P1) / BR-T5 (P2) | P1 + P2 |
| **Support** | Ticketing (raise/track/history), seller settings, client checklist | BR-T2 | P1 |
| **CMS** | Banners, static/legal pages | BR-A4 | P2 |
| **Reporting & Analytics** | Seller reports (P1); admin sales/package/lead/product reports + conversion | BR-R1 (P1) / BR-R2–R6 (P2) | P1 + P2 |
| **Admin Dashboard** | Operational stats; Pending Tasks | BR-A1 (P1) / BR-A3 (P2) | P1 + P2 |

---

## 6. Data Architecture (MongoDB)

### 6.1 Modelling Approach
- **MongoDB** (document store) as the primary operational datastore, accessed via **Mongoose** schemas.
- **Embed** data that is read together and is bounded; **reference** data that is large, unbounded, or shared across domains.
- **Schema validation** enforced at two levels: Mongoose schemas in the app **and** MongoDB JSON-Schema validators on critical collections (billing, leads) to prevent malformed documents.
- Geography (Country/State/City) stored as a reference collection and embedded as denormalised labels where useful for reads.
- **Soft-delete + status** for products (Trash), with a scheduled job enforcing the 7-day purge.

### 6.2 Core Collections (Phase 1)

| Collection | Shape / notes | Embed vs Reference |
|-----------|---------------|--------------------|
| `users` | email/phone, passwordHash, type (seller/buyer/admin), status, loginHistory[] | loginHistory embedded (bounded/capped) |
| `roles`, `permissions` | RBAC definitions for admin users | referenced by user |
| `sellerProfiles` | userId, company, GST/PAN/CIN, businessProfile, keywords[], addresses[] | addresses embedded |
| `buyerProfiles` | userId, contact, preferences, addresses[] | addresses embedded |
| `categories`, `productTypes`, `brands`, `attributes` | catalogue taxonomy | referenced |
| `products` | sellerId(ref), categoryId(ref), **attributes (embedded sub-doc)**, MOQ, price, gstPct, hsn, media[], **qualityScore**, status, trashedAt, dealingAreas[] | attributes/media/dealingAreas embedded |
| `enquiries` | buyerId(ref), product/category ref, requirement, quantity, docs[], preferredLocation | docs embedded |
| `leads` | enquiryId(ref), status, createdAt, **distributions[] {sellerId, assignedAt}** | distributions embedded (bounded array) |
| `packages` | name, price, validity, leadAllocation, product/featured limits, flags | standalone |
| `subscriptions` | sellerId(ref), packageId(ref), start/end, trial flag, status | referenced |
| `quotations`, `invoices`, `payments` | billing docs, GST fields, status, idempotencyKey | referenced; written within transactions |
| `notifications` | userId(ref), type, payload (sub-doc), channel, read/sent status | payload embedded |
| `supportTickets` | category, priority, status, assignee, **messages[]** | messages embedded (thread) |
| `auditLogs` | actor, action, target, before/after, timestamp (append-only) | standalone |

### 6.3 Additional Collections (Phase 2)

| Collection | Purpose |
|-----------|---------|
| `leadCreditTxns` | Buy-leads purchases and balances |
| `leadStatusEvents` | Lifecycle/conversion events per lead |
| `callEnquiries` | Phone enquiry records + outcomes |
| `featuredListings` | Featured promotions + admin approval state |
| `reviews` | Ratings/reviews + moderation state |
| `questions`, `answers` | Product Q&A + moderation (referenced by product) |
| `sellerReports` | Buyer complaints about sellers |
| `enachMandates` | Mandate id, status, authorisation, recurring schedule |
| `cmsPages`, `banners` | CMS content + scheduling |
| `reportSnapshots` | Pre-aggregated reporting data (offload analytics) |

> Reviews, questions/answers, leadStatusEvents, and notifications are **separate collections (referenced)** rather than embedded, because they grow unbounded and would otherwise bloat parent documents past practical limits.

### 6.4 Transactions, Integrity & Indexing
- **Multi-document ACID transactions** (MongoDB on a replica set — Atlas provides this) wrap operations that must be atomic, e.g.:
  - Lead allocation + package quota decrement + lead-credit deduction.
  - Invoice creation + payment confirmation + subscription activation.
- **Referential discipline:** because MongoDB has no foreign keys, cross-collection integrity is enforced in the service layer (existence checks, cascade handling) and verified by integration tests.
- **Idempotency:** payment/billing writes carry an `idempotencyKey` (unique index) to safely handle gateway webhook retries.
- **Indexing:** compound indexes on hot paths — `products(categoryId, status, qualityScore)`, `leads(status, createdAt)`, `leadDistributions(sellerId)`, `subscriptions(sellerId, status)`, text/Atlas Search indexes on product/supplier fields.
- **TTL index / scheduled job:** enforce the 7-day product Trash purge (RULE-5).

### 6.5 Data Lifecycle & Retention
- **Trash purge:** scheduled worker (or TTL index) permanently removes products trashed > 7 days.
- **Audit:** sensitive actions written to append-only `auditLogs`.
- **PII:** encrypted at rest (Atlas encryption) and in transit (TLS); field-level access via least privilege.

---

## 7. API Design

- **Style:** RESTful JSON over HTTPS, versioned (`/api/v1/...`).
- **Auth:** Bearer JWT access tokens (short-lived) + rotating refresh tokens; RBAC enforced per-endpoint via guards/policies.
- **Conventions:** consistent pagination, filtering, sorting; standardised error envelope; **idempotency keys** for payment/billing mutations.
- **Validation:** DTO + Mongoose schema validation at the API boundary.
- **Separation:** distinct route groups per portal (seller/buyer/admin) sharing the same core services.
- **Rate limiting:** per-IP and per-user at the edge (Redis-backed) protecting enquiry/search/auth endpoints.

---

## 8. Key Workflows

### 8.1 Lead Capture → Distribution (P1 core; P2 tracking)
```
Buyer submits enquiry (API)
   → persist enquiry + create lead (status=undistributed)
   → enqueue "distribute-lead" job (BullMQ)
Worker (inside a MongoDB transaction):
   → find eligible sellers (category + dealing area + active package + remaining quota)   [P1]
   → push lead.distributions[] + decrement package quota atomically (RULE-1/2)             [P1]
   → set lead.status=distributed; emit seller notifications                                [P1]
   → record distributed-to count + leadStatusEvents                                        [P2]
If quota exhausted → seller buys leads (credits) or upgrades package                        [P2 / P1]
```

### 8.2 Product Publish + Quality Score (P1)
```
Seller submits product → Quality Score engine computes score (completeness, media, specs)
   → score ≥ 70%: eligible for Featured (Featured workflow = P2) + higher search weight
   → score < 70%: published as normal listing
   → admin moderation (approve/reject/flag/ban)
```

### 8.3 Subscription Purchase & Billing (P1; e-NACH P2)
```
Seller selects package → quotation/invoice generated (GST) → payment via Razorpay
   → payment verified (webhook + idempotent confirmation)
   → invoice + payment + subscription activation committed in one transaction
Phase 2: recurring billing via e-NACH mandate (create → authorise → monitor status)
```

### 8.4 Review / Q&A Moderation (P2)
```
Buyer submits review/question → stored as "pending"
   → admin moderates (approve/edit/remove) → published
   → seller notified; buyer notified on seller response
```

---

## 9. Integration Architecture

| Integration | Purpose | Phase | Notes |
|-------------|---------|:-----:|-------|
| **Payment Gateway (Razorpay)** | Package payments, invoices, verification | P1 | Webhooks; idempotent handlers |
| **e-NACH / eMandate** | Recurring payment authorisation & status | P2 | Tracked in `enachMandates` |
| **Email (SES/SMTP)** | Transactional notifications, invoices | P1 | Templated; bounce handling |
| **SMS Gateway** | SMS notifications | P2 | BR-T / FE-3 |
| **Push notifications** | In-app/web/mobile push | P2 | |
| **Object storage (S3)** | Media & document storage | P1 | Signed URLs |
| **Atlas Search / OpenSearch** | Product/supplier search | P1 / P2 | Atlas Search native; OpenSearch if scale demands |
| **Future (FE backlog)** | CRM, WhatsApp API, AI lead scoring, analytics, public API | Future | Extensibility via event bus + webhooks |

Integrations sit behind **internal adapter interfaces** so providers can be swapped without touching business logic.

---

## 10. Security & Compliance Architecture

- **AuthN/AuthZ:** JWT + refresh rotation; **RBAC** with Super Admin full control and configurable role permissions (BR-A2); least privilege.
- **Secrets:** all credentials (Mongo connection URI, Razorpay/e-NACH keys, JWT signing keys, SMS/email tokens) stored in **AWS Secrets Manager**, fetched at runtime via IAM — never committed to VCS or baked into images.
- **Data protection:** TLS in transit; encryption at rest for MongoDB (Atlas) and object storage.
- **PII & financial data:** GST/PAN, payment, and e-NACH data access restricted and audited; PCI concerns offloaded to the payment gateway (no raw card data stored).
- **Input safety:** server-side validation, Mongoose schema enforcement, query sanitisation (guard against NoSQL injection / operator injection), output encoding, CSRF protection for cookie flows, security headers (CSP, HSTS).
- **Abuse protection:** rate limiting and bot/spam mitigation on enquiry, search, review, and auth endpoints.
- **Moderation gates:** reviews and Q&A pass admin moderation before public display (RULE-10).
- **Auditability:** append-only `auditLogs` for verification, account state changes, lead distribution, and financial transactions.
- **Compliance:** India-first invoicing (GST); e-NACH mandate handling per NPCI norms (Phase 2).

---

## 11. Infrastructure & Deployment

### 11.1 Environments
- **Dev → Staging → Production**, each isolated (separate Atlas cluster/DB, Redis, storage buckets, secrets).
- Infrastructure defined in **Terraform**; promotion via CI/CD.

### 11.2 Topology (Production, target ~10k users)
- **App tier:** containerised API + workers on AWS ECS Fargate (or EKS), behind an **Application Load Balancer**; horizontal autoscaling.
- **Front-end:** Next.js apps served via container or managed platform + CDN for static assets and SSR caching.
- **Data tier:** **MongoDB Atlas** (replica set; backups + point-in-time recovery; sharding-ready); **Redis (ElastiCache)**; **S3** for objects.
- **Workers:** separate autoscaled worker service consuming BullMQ queues (lead distribution, notifications, scheduled jobs, report generation).

### 11.3 Cross-Cutting Infrastructure Services

| Service | Role in this platform |
|---------|----------------------|
| **AWS Application Load Balancer (ALB)** | Distributes traffic across API instances; TLS termination; health checks; enables zero-downtime rolling/blue-green deploys and horizontal autoscaling (supports §12 stateless scaling). |
| **API Gateway / Edge** | Single entry point: JWT validation, routing, throttling, and per-user/IP rate limiting (Redis-backed) before requests hit business logic — protecting enquiry/search/auth endpoints. Implemented as ALB + in-app NestJS gateway; managed AWS API Gateway optional if the backend later splits into many services. |
| **AWS Secrets Manager** | Encrypted, rotatable storage for all secrets (Mongo URI, gateway keys, JWT keys); apps read via IAM at runtime. Satisfies the §10 "no secrets in code/env" requirement and supports auditability for financial/compliance data. |
| **CDN** | Caches static assets and SSR pages; offloads media delivery from origin. |

### 11.4 CI/CD
- GitHub Actions: lint → unit/integration tests → security scan (SAST/dependency) → build image → deploy to staging → promote to prod (with approval).
- Mongo schema/index changes and data migrations versioned and run as controlled deploy steps (migration scripts).
- Blue/green or rolling deploys for zero-downtime releases.

### 11.5 Backup & DR
- Atlas automated backups + point-in-time recovery; object storage versioning; documented restore runbook and RPO/RTO targets (to be finalised).

---

## 12. Scalability & Performance

- **Stateless API** behind the ALB → scale horizontally; sessions/tokens in Redis/JWT (no server affinity).
- **Async-first** for heavy/spiky work (lead distribution, notifications, reports) via queues to keep request latency low.
- **Caching:** Redis for hot reads (categories, package definitions, search facets); CDN for media and SSR pages.
- **MongoDB scaling:** appropriate compound indexes on hot paths; **read preference to secondaries** for read-heavy/reporting workloads; **sharding** (e.g. by sellerId or hashed key) for high-volume collections (`leads`, `notifications`) when needed.
- **Search:** Atlas Search for Phase 1; evaluate OpenSearch in Phase 2 if relevance/scale demands a dedicated cluster.
- **Reporting (P2):** offload to secondary reads and/or pre-aggregated `reportSnapshots` to protect the OLTP path.

---

## 13. Observability & Operations

- **Logging:** structured, centralised logs with correlation IDs across API ↔ workers.
- **Metrics:** application + infra metrics (request latency, queue depth, distribution latency, payment success rate, Mongo op/connection metrics via Atlas).
- **Tracing:** OpenTelemetry distributed tracing for key flows (enquiry → lead → notification).
- **Alerting:** on error rates, queue backlog, payment/e-NACH failures, undistributed-lead backlog, Atlas health.
- **Dashboards:** operational health + business KPIs (BRD §11) where feasible.

---

## 14. Phased Technical Delivery

### 14.1 Phase 1 — Core MVP
- Platform skeleton: monorepo, three Next.js portals, NestJS core, **MongoDB Atlas**, Redis, S3, ALB, Secrets Manager, CI/CD, environments, auth/RBAC.
- Modules: Identity/RBAC, Seller Account, Buyer (dashboard/profile/wishlist/enquiry), Product/Catalog + Quality Score + trash purge + dealing areas, **Lead Engine** (capture/distribute/assign/view), Packages & Billing (quotations/invoices/payment via Razorpay), Notifications (core events, email), Support ticketing, Admin (dashboard stats, user mgmt, seller view/edit, data bank, package mgmt, product mgmt).
- Search via **Atlas Search**.

### 14.2 Phase 2 — Enhancements
- Lead Monetisation (Buy Leads/credits, status tracking & distributed-count, call enquiries, conversion reporting).
- Featured Listings (+ admin featured approval).
- Reviews & Q&A (+ admin moderation), Report Seller, seller-response notifications, SMS/push channels.
- e-NACH recurring billing.
- Full admin Reporting suite (sales/package/lead/product) + Pending Tasks.
- CMS (banners + static pages), advanced Seller Management (activate/block/flag, callbacks, dealing-area mgmt, product deletion).
- Optional search upgrade to OpenSearch.

### 14.3 Future (FE backlog — architectural hooks only)
- Event bus + outbound webhooks and a documented public API to enable CRM, WhatsApp/SMS automation, AI lead scoring, analytics dashboards, and enterprise API access (FE-1…FE-13) without core rework.

---

## 15. Non-Functional Requirements (Technical Targets)

| Category | Target (initial; to be ratified) |
|----------|----------------------------------|
| Availability | ≥ 99.5% core APIs (Phase 1); Atlas replica set + DR improves this |
| Performance | P95 API latency < 500 ms for read endpoints; search < 1 s |
| Lead distribution latency | Lead distributed to eligible sellers within seconds (async) |
| Scalability | Horizontal scale (API + Mongo sharding) to several × the ~10k baseline without redesign |
| Security | OWASP Top-10 controls; NoSQL-injection guards; encrypted PII/financial data; audited admin actions |
| Recoverability | Atlas backups + PITR; documented RPO/RTO |
| Maintainability | Modular boundaries, automated tests, IaC, CI/CD |
| Observability | Centralised logs/metrics/traces with alerting |

---

## 16. Technical Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| **No DB-enforced foreign keys (MongoDB)** | Orphaned/inconsistent cross-collection data | Service-layer referential checks, multi-document transactions, integration tests, schema validators |
| **Transactional/financial flows on a document DB** | Double-allocation, billing inconsistency | MongoDB ACID transactions on replica set; idempotency keys; reconciliation jobs |
| **Schema drift over time** | Inconsistent documents, query bugs | Mongoose + MongoDB JSON-Schema validators; versioned migrations |
| **NoSQL / operator injection** | Security breach | Input sanitisation, query builders, parameter typing, validation at boundary |
| Modular monolith becomes tangled | Slows Phase 2 / scale | Enforce module boundaries + dependency rules + tests; keep extraction seams |
| Lead distribution hot path under load | Delayed/incorrect allocation | Async queue + idempotent workers + indexing + queue-depth monitoring |
| Payment / e-NACH integration complexity | Revenue leakage, failed mandates | Idempotent webhooks, reconciliation, gateway-native mandate flows (P2) |
| Reporting load impacts OLTP | Slow app under heavy reports | Secondary reads + pre-aggregated snapshots (P2) |
| PII/GST/e-NACH compliance gaps | Legal exposure | Encryption, least-privilege access, audit logging, compliance review pre-go-live |

---

## 17. Assumptions & Open Items

### 17.1 Assumptions
- Single primary region (India) for Phase 1; multi-region not required initially.
- Web-first delivery; native mobile app is a Future Enhancement (FE-11).
- Buyers are free users; only sellers transact (packages/credits).
- One enquiry may fan out to multiple eligible sellers (BRD RULE-7).
- MongoDB runs as a **replica set** (Atlas) so transactions are available from day one.

### 17.2 Open Items

| ID | Open Item | Owner | Status |
|----|-----------|-------|--------|
| OI-T1 | Ratify final cloud provider and confirm **MongoDB Atlas tier/sizing** for target load. | Architecture | Open |
| OI-T2 | Confirm payment gateway (Razorpay) and e-NACH provider for Phase 2. | Eng / Finance | Open |
| OI-T3 | Finalise **Product Quality Score** weighting model (mirrors BRD OI-3). | Product / Eng | Open |
| OI-T4 | Confirm SLAs/NFR targets (availability, RPO/RTO, latency) in §15. | Eng / Sponsor | Open |
| OI-T5 | Decide whether Phase 2 needs OpenSearch or Atlas Search suffices. | Eng | Open |
| OI-T6 | Confirm whether any Phase 2 module must move into Phase 1 (mirrors BRD OI-5). | Product / Eng | Open |
| OI-T7 | Define embedding-vs-referencing standards and document-size limits per collection. | Eng | Open |

---

## 18. Glossary

| Term | Definition |
|------|------------|
| Modular monolith | Single deployable backend with strongly separated internal modules. |
| Document model | Data stored as JSON-like documents (MongoDB) rather than relational rows. |
| Embedding / Referencing | Storing related data inside a document vs linking by id across collections. |
| ODM (Mongoose) | Object-Document Mapper providing schemas/validation over MongoDB. |
| Atlas Search | Lucene-based full-text search built into MongoDB Atlas. |
| Replica set | Group of MongoDB nodes enabling HA and multi-document transactions. |
| Sharding | Horizontal partitioning of a collection across nodes for scale. |
| RBAC | Role-Based Access Control. |
| e-NACH | Electronic mandate for recurring payment authorisation (India). |
| Quality Score | Computed product score (0–100%) gating Featured eligibility (≥ 70%). |
| SSR / SSG | Server-Side Rendering / Static Site Generation (SEO for buyer pages). |
| IaC | Infrastructure as Code (Terraform). |
| RPO / RTO | Recovery Point / Recovery Time Objective. |

---

*End of Document — TAD v2.0 (Draft for Review)*
