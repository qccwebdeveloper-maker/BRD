# eB2BMart Portal — Technical Architecture Document (TAD)

**Version:** 1.0
**Status:** Draft for Review
**Date:** 26 June 2026
**Prepared by:** Engineering / Solution Architecture
**Source References:** eB2BMart Portal – BRD v3.0, FRD v1.x

---

## 1. Document Control

### 1.1 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 26-Jun-2026 | Architecture Team | Initial Technical Architecture Document derived from BRD v3.0. Defines the target technology stack, system architecture, data model, integrations, security, infrastructure, and a phased (Phase 1 / Phase 2) technical delivery plan. |

### 1.2 Approvals

| Name | Role | Decision | Date |
|------|------|----------|------|
| _TBD_ | Technical Lead / Architect | Pending | |
| _TBD_ | Engineering Manager | Pending | |
| _TBD_ | Product Manager | Pending | |
| _TBD_ | Security / Compliance | Pending | |

### 1.3 Purpose & Audience

This TAD describes **how the eB2BMart platform will be built** to satisfy the business requirements in **BRD v3.0**. Where the BRD answers *what* and *why*, this document answers *how* at a technical level: the architecture, components, data, technology choices, integrations, security, and deployment.

**Audience:** engineering team, DevOps, QA, security reviewers, and technical stakeholders.

### 1.4 Scope

The architecture covers all three portals (Seller, Buyer, Admin) and the shared platform services. It is organised to deliver the BRD's **Phase 1 (Core MVP)** first, with **Phase 2 (Enhancements)** built on the same foundations without re-platforming. The Future Enhancements (FE-1…FE-13) are treated as a forward-looking backlog and influence architectural decisions (extensibility points) but are not fully designed here.

### 1.5 Reference Documents

| Ref | Document |
|-----|----------|
| R1 | eB2BMart Portal — BRD v3.0 |
| R2 | eB2BMart Portal — FRD (Functional Requirement Document) |
| R3 | Phasing map — BRD §5.2 (Module → Phase) |

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
- **Business rules baked into the engine:** package-bound lead limits, Product Quality Score ≥ 70% for Featured, 7-day trash purge, admin moderation gates.
- **Scale assumption:** target sizing of the order of **~10k active users** (sellers + buyers + admin operators) with headroom; architecture must scale horizontally beyond that without redesign.

---

## 3. Architecture Overview

### 3.1 Architectural Style

A **modular-monolith backend** exposing REST APIs to three separate web front-ends, with **asynchronous workers** for lead distribution, notifications, scheduled jobs, and report generation. This balances delivery speed (one deployable core) with clear seams that can later be extracted into services (e.g. Lead Engine, Notifications) if scale demands.

### 3.2 Logical Architecture

```
                         ┌──────────────────────────────────────────────┐
                         │                 Clients (Web)                 │
                         │  Seller Portal   Buyer Portal   Admin Portal  │
                         │   (Next.js)       (Next.js)      (Next.js)    │
                         └───────────────┬──────────────────────────────┘
                                         │ HTTPS / REST (JSON)
                                  ┌──────▼───────┐
                                  │ API Gateway  │  auth, rate-limit, routing
                                  │  / Edge      │
                                  └──────┬───────┘
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
                ┌─────────▼──┐ ┌────▼────┐ ┌──▼─────┐ ┌▼─────────────┐
                │ PostgreSQL │ │  Redis  │ │ Object │ │  Search      │
                │ (primary   │ │ cache + │ │ Store  │ │  (PG FTS P1 /│
                │  OLTP DB)  │ │ queues  │ │ (S3)   │ │  OpenSearch  │
                └────────────┘ └────┬────┘ └────────┘ │  P2)         │
                                    │                 └──────────────┘
                          ┌─────────▼──────────┐
                          │  Async Workers      │  lead distribution,
                          │  (BullMQ consumers)  │  notifications, jobs,
                          └─────────┬──────────┘  scheduled purges, reports
                                    │
                 ┌──────────────────▼────────────────────┐
                 │        External Integrations           │
                 │  Payment GW (Razorpay) │ e-NACH (P2)    │
                 │  Email (SES/SMTP) │ SMS GW │ Push (P2)  │
                 └────────────────────────────────────────┘
```

### 3.3 Portals (Front-end Applications)
- **Seller Portal**, **Buyer Portal**, **Admin Portal** are three Next.js applications sharing a common component/design library and API client.
- Public buyer-facing pages (product/supplier listings, CMS pages) are **server-side rendered / statically generated** for SEO; authenticated dashboards are client-rendered.

---

## 4. Technology Stack

Recommended stack (decisive defaults; key alternatives noted in §4.2).

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Web front-end | **Next.js (React) + TypeScript**, Tailwind CSS, shared UI library | SSR/SSG for SEO on buyer pages; one ecosystem for all three portals; strong hiring pool |
| API / backend | **Node.js + NestJS (TypeScript)** | Modular structure enforces clean module boundaries in a monolith; same language as front-end; mature DI/validation |
| Primary database | **PostgreSQL 15+** | Relational integrity for marketplace/billing/GST data; JSONB for flexible product attributes; strong full-text search for Phase 1 |
| Cache / queue | **Redis** + **BullMQ** | Caching, sessions, rate limiting, and durable background job queues (lead distribution, notifications) |
| Object storage | **S3-compatible** (AWS S3 / MinIO) | Product images, documents, invoices; signed URLs |
| Search | **Postgres FTS (Phase 1)** → **OpenSearch/Elasticsearch (Phase 2)** | Start simple; upgrade to dedicated search as catalogue and traffic grow |
| Auth | **JWT access + refresh tokens**, bcrypt/argon2 hashing, RBAC | Stateless API auth; refresh rotation; role/permission claims |
| Payments | **Razorpay** (cards/UPI/netbanking + **e-NACH/eMandate** in Phase 2) | India-first, supports recurring mandates and GST invoicing flows |
| Email | **Amazon SES** (or SMTP relay) | Transactional email (notifications, invoices) |
| SMS / Push | SMS gateway (e.g. MSG91/Twilio); Web/Mobile push | Phase 2 channels per BRD BR-T (and FE-3) |
| Containerisation | **Docker** | Reproducible builds across environments |
| Orchestration / hosting | **AWS** (ECS Fargate or EKS); managed RDS (Postgres), ElastiCache (Redis) | Managed services reduce ops burden at 10k scale; scale up later |
| CI/CD | GitHub Actions (build, test, scan, deploy) | Automated pipelines, environment promotion |
| IaC | Terraform | Reproducible, reviewable infrastructure |
| Observability | OpenTelemetry + centralised logs/metrics/traces (e.g. CloudWatch/Grafana stack) | Operability and audit |

### 4.2 Key Technology Decisions (with alternatives)

| Decision | Chosen | Alternative considered | Why chosen |
|----------|--------|------------------------|------------|
| Backend shape | Modular monolith | Microservices | Faster Phase 1 delivery, lower ops cost at current scale; seams allow later extraction |
| Primary DB | PostgreSQL | MySQL / MongoDB | Relational + JSONB hybrid fits structured billing + flexible product attributes |
| Search (P1) | Postgres FTS | OpenSearch from day 1 | Avoid extra infra until catalogue/traffic justify it (Phase 2) |
| Front-end | Next.js (SSR) | SPA (CSR-only React) | SEO is critical for a B2B discovery marketplace |
| Payments | Razorpay | Stripe / PayU | Native India support incl. e-NACH eMandate and GST invoicing |

> Stack is a recommendation; final selection is an open item (see §17, OI-T1). Where the org already standardises on a language/cloud, align accordingly.

---

## 5. System Components (Modules → BRD Mapping)

Each backend module maps to BRD capabilities and is tagged with the phase in which it is first delivered.

| Module | Responsibilities | BRD Ref | Phase |
|--------|------------------|---------|:-----:|
| **Identity & Access (RBAC)** | Registration, login, JWT/refresh, password, roles & permissions, admin user management, login history | BR-S1, BR-A2 | P1 |
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
| **Reporting & Analytics** | Seller reports (P1: lead/performance/enquiry); admin sales/package/lead/product reports + conversion | BR-R1 (P1) / BR-R2–R6 (P2) | P1 + P2 |
| **Admin Dashboard** | Operational stats; Pending Tasks | BR-A1 (P1) / BR-A3 (P2) | P1 + P2 |

---

## 6. Data Architecture

### 6.1 Approach
- **PostgreSQL** as the single source of truth (OLTP). Normalised core with **JSONB** for variable product specifications and notification payloads.
- Strict foreign keys and transactions for billing/lead allocation integrity.
- **Soft-delete + status** pattern for products (Trash), with a scheduled job enforcing the 7-day purge.
- Geography reference tables (Country/State/City) shared by addresses, dealing areas, and lead targeting.

### 6.2 Core Entities (Phase 1)

| Entity | Key fields / notes |
|--------|--------------------|
| `user` | id, email/phone, password_hash, type (seller/buyer/admin), status, login history |
| `role`, `permission`, `role_permission`, `user_role` | RBAC for admin users |
| `seller_profile` | user_id, company, GST/PAN/CIN, business profile, keywords |
| `address` | owner ref, type (billing/shipping/office), country/state/city |
| `buyer_profile` | user_id, contact, preferences |
| `category`, `product_type`, `brand`, `attribute` | catalogue taxonomy |
| `product` | seller_id, category_id, attributes (JSONB), MOQ, price, GST%, HSN, media refs, **quality_score**, status (active/inactive/trash), trashed_at |
| `dealing_area` | seller_id, country/state/city |
| `enquiry` | buyer_id, product/category ref, requirement, quantity, docs, preferred location |
| `lead` | enquiry_id, status (undistributed/distributed/…), created_at |
| `lead_distribution` | lead_id, seller_id, assigned_at (n sellers per lead) |
| `package` | name, price, validity, lead allocation, product/featured limits, flags |
| `subscription` | seller_id, package_id, start/end, trial flag, status |
| `quotation`, `invoice`, `payment` | billing artifacts, GST fields, status |
| `notification` | user_id, type, payload (JSONB), channel, read/sent status |
| `support_ticket`, `ticket_message` | category, priority, status, assignee, history |

### 6.3 Additional Entities (Phase 2)

| Entity | Purpose |
|--------|---------|
| `lead_credit_txn` | Buy-leads purchases and balances |
| `lead_status_event` | Lifecycle/conversion tracking per lead |
| `call_enquiry` | Phone enquiry records + outcomes |
| `featured_listing` | Featured promotions + admin approval state |
| `review`, `review_moderation` | Ratings/reviews + moderation state |
| `question`, `answer`, `qa_moderation` | Product Q&A + moderation |
| `seller_report` | Buyer complaints about sellers |
| `enach_mandate` | Mandate id, status, authorisation, recurring schedule |
| `cms_page`, `banner` | CMS content, scheduling |
| `report_snapshot` (optional) | Pre-aggregated reporting data |

### 6.4 Data Lifecycle & Retention
- **Trash purge:** scheduled worker permanently deletes products trashed > 7 days (RULE-5).
- **Audit:** sensitive actions (verification, activation/block, lead distribution, financial transactions) written to an append-only `audit_log`.
- **PII:** encrypted at rest (DB/disk) and in transit (TLS); access via least privilege.

---

## 7. API Design

- **Style:** RESTful JSON over HTTPS, versioned (`/api/v1/...`).
- **Auth:** Bearer JWT access tokens (short-lived) + rotating refresh tokens; RBAC enforced per-endpoint via guards/policies.
- **Conventions:** consistent pagination, filtering, sorting; standardised error envelope; idempotency keys for payment/billing mutations.
- **Validation:** DTO-level schema validation at the API boundary.
- **Separation:** distinct API surfaces/route groups per portal (seller, buyer, admin) sharing the same core services.
- **Rate limiting:** per-IP and per-user at the gateway (Redis-backed) to protect enquiry/search/auth endpoints.

---

## 8. Key Workflows

### 8.1 Lead Capture → Distribution (P1 core; P2 tracking)
```
Buyer submits enquiry (API)
   → persist enquiry + create lead (status=undistributed)
   → enqueue "distribute-lead" job (BullMQ)
Worker:
   → evaluate eligible sellers (category + dealing area + active package + remaining quota)   [P1]
   → create lead_distribution rows (respect package limits, RULE-1/2)                          [P1]
   → set lead.status=distributed; emit seller notifications                                    [P1]
   → record distributed-to count + status events                                               [P2]
If quota exhausted → seller buys leads (credits) or upgrades package                            [P2 / P1]
```

### 8.2 Product Publish + Quality Score (P1)
```
Seller submits product → Quality Score engine computes score (completeness, media, specs, etc.)
   → score ≥ 70%: eligible for Featured (Featured workflow = P2) + higher search weight
   → score < 70%: published as normal listing
   → admin moderation (approve/reject/flag/ban)
```

### 8.3 Subscription Purchase & Billing (P1; e-NACH P2)
```
Seller selects package → quotation/invoice generated (GST) → payment via Razorpay
   → payment verified (webhook + idempotent confirmation) → subscription activated
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
| **Payment Gateway (Razorpay)** | Package payments, invoices, verification | P1 | Webhooks for async confirmation; idempotent handlers |
| **e-NACH / eMandate** | Recurring payment authorisation & status | P2 | Mandate lifecycle tracked in `enach_mandate` |
| **Email (SES/SMTP)** | Transactional notifications, invoices | P1 | Templated; ret/bounce handling |
| **SMS Gateway** | SMS notifications | P2 | BR-T / FE-3 |
| **Push notifications** | In-app/web/mobile push | P2 | |
| **Object storage (S3)** | Media & document storage | P1 | Signed upload/download URLs |
| **Search (OpenSearch)** | Advanced product/supplier search | P2 | Indexed from Postgres via change events |
| **Future (FE backlog)** | CRM, WhatsApp API, AI lead scoring, analytics, public API | Future | Extensibility points reserved (event bus, webhooks) |

Integrations are wrapped behind **internal adapter interfaces** so providers can be swapped without touching business logic.

---

## 10. Security & Compliance Architecture

- **AuthN/AuthZ:** JWT + refresh rotation; **RBAC** with Super Admin full control and configurable role permissions (BR-A2). Principle of least privilege.
- **Data protection:** TLS in transit; encryption at rest for DB and object storage; secrets in a managed secret store (not in code/env files committed to VCS).
- **PII & financial data:** GST/PAN, payment, and e-NACH data access restricted and audited; PCI concerns offloaded to the payment gateway (no raw card data stored).
- **Input safety:** server-side validation, parameterised queries (ORM), output encoding, CSRF protection for cookie flows, security headers (CSP, HSTS).
- **Abuse protection:** rate limiting and bot/spam mitigation on enquiry, search, review, and auth endpoints.
- **Moderation gates:** reviews and Q&A pass admin moderation before public display (RULE-10).
- **Auditability:** append-only audit log for verification, account state changes, lead distribution, and financial transactions.
- **Compliance:** India-first invoicing (GST), e-NACH mandate handling per NPCI norms (Phase 2).

---

## 11. Infrastructure & Deployment

### 11.1 Environments
- **Dev → Staging → Production**, each isolated (separate DB, Redis, storage buckets, secrets).
- Infrastructure defined in **Terraform**; promotion via CI/CD.

### 11.2 Topology (Production, target ~10k users)
- **App tier:** containerised API + workers on AWS ECS Fargate (or EKS), behind an Application Load Balancer; horizontal autoscaling.
- **Front-end:** Next.js apps served via container or managed platform + CDN for static assets and SSR caching.
- **Data tier:** managed **PostgreSQL (RDS)** with automated backups + read replica (Phase 2/scale); **Redis (ElastiCache)**; **S3** for objects.
- **Workers:** separate autoscaled worker service consuming BullMQ queues (lead distribution, notifications, scheduled jobs, report generation).

### 11.3 CI/CD
- GitHub Actions: lint → unit/integration tests → security scan (SAST/dependency) → build image → deploy to staging → promote to prod (with approval).
- Database migrations versioned and run as a controlled deploy step.
- Blue/green or rolling deploys for zero-downtime releases.

### 11.4 Backup & DR
- Automated daily DB backups + point-in-time recovery; object storage versioning; documented restore runbook and RPO/RTO targets (to be finalised).

---

## 12. Scalability & Performance

- **Stateless API** behind a load balancer → scale horizontally; sessions/tokens in Redis/JWT (no server affinity).
- **Async-first** for heavy/spiky work (lead distribution, notifications, reports) via queues to keep request latency low.
- **Caching:** Redis for hot reads (categories, package definitions, search facets); CDN for media and SSR pages.
- **Database:** proper indexing on lead/product/enquiry hot paths; read replica and partitioning for high-volume tables (`lead`, `notification`) when needed.
- **Search:** migrate from Postgres FTS to OpenSearch in Phase 2 as catalogue/traffic grow.
- **Reporting (P2):** offload to read replica and/or pre-aggregated snapshots to avoid impacting OLTP.

---

## 13. Observability & Operations

- **Logging:** structured, centralised logs with correlation IDs across API ↔ workers.
- **Metrics:** application + infra metrics (request latency, queue depth, distribution latency, payment success rate).
- **Tracing:** OpenTelemetry distributed tracing for key flows (enquiry → lead → notification).
- **Alerting:** on error rates, queue backlog, payment/e-NACH failures, undistributed-lead backlog.
- **Dashboards:** operational health + business KPIs (BRD §11) where feasible.

---

## 14. Phased Technical Delivery

### 14.1 Phase 1 — Core MVP (build foundations)
- Platform skeleton: monorepo, three Next.js portals, NestJS core, PostgreSQL, Redis, S3, CI/CD, environments, auth/RBAC.
- Modules: Identity/RBAC, Seller Account, Buyer (dashboard/profile/wishlist/enquiry), Product/Catalog + Quality Score + trash purge + dealing areas, **Lead Engine** (capture/distribute/assign/view), Packages & Billing (quotations/invoices/payment via Razorpay), Notifications (core events, email), Support ticketing, Admin (dashboard stats, user mgmt, seller view/edit, data bank, package mgmt, product mgmt).
- Search via Postgres FTS.

### 14.2 Phase 2 — Enhancements (extend, don't re-platform)
- Lead Monetisation (Buy Leads/credits, status tracking & distributed-count, call enquiries, conversion reporting).
- Featured Listings (+ admin featured approval).
- Reviews & Q&A (+ admin moderation), Report Seller, seller-response notifications, SMS/push channels.
- e-NACH recurring billing.
- Full admin Reporting suite (sales/package/lead/product) + Pending Tasks.
- CMS (banners + static pages), advanced Seller Management (activate/block/flag, callbacks, dealing-area mgmt, product deletion).
- Search upgrade to OpenSearch.

### 14.3 Future (FE backlog — architectural hooks only)
- Event bus + outbound webhooks and a documented public API surface to enable CRM, WhatsApp/SMS automation, AI lead scoring, analytics dashboards, and enterprise API access (FE-1…FE-13) without core rework.

---

## 15. Non-Functional Requirements (Technical Targets)

| Category | Target (initial; to be ratified) |
|----------|----------------------------------|
| Availability | ≥ 99.5% for core APIs (Phase 1); improve with replicas/DR in Phase 2 |
| Performance | P95 API latency < 500 ms for read endpoints; search results < 1 s |
| Lead distribution latency | Lead distributed to eligible sellers within seconds of enquiry (async) |
| Scalability | Horizontal scale to several × the ~10k-user baseline without redesign |
| Security | OWASP Top-10 controls; encrypted PII/financial data; audited admin actions |
| Recoverability | Daily backups + PITR; documented RPO/RTO |
| Maintainability | Modular boundaries, automated tests, IaC, CI/CD |
| Observability | Centralised logs/metrics/traces with alerting |

---

## 16. Technical Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Modular monolith becomes a tangled monolith | Slows Phase 2 / scale | Enforce module boundaries, dependency rules, and integration tests; keep extraction seams |
| Lead distribution hot path under load | Delayed/incorrect allocation | Async queue + idempotent workers + DB indexing + monitoring of queue depth |
| Payment / e-NACH integration complexity | Revenue leakage, failed mandates | Idempotent webhook handling, reconciliation jobs, gateway-native mandate flows (P2) |
| Postgres FTS outgrown | Poor search relevance/latency | Planned migration to OpenSearch in Phase 2 with indexing pipeline |
| Reporting load impacts OLTP | Slow app under heavy reports | Read replica + pre-aggregated snapshots (P2) |
| PII/GST/e-NACH compliance gaps | Legal exposure | Encryption, least-privilege access, audit logging, compliance review before go-live |

---

## 17. Assumptions & Open Items

### 17.1 Assumptions
- Single primary region (India) for Phase 1; multi-region not required initially.
- Web-first delivery; native mobile app is a Future Enhancement (FE-11).
- Buyers are free users; only sellers transact (packages/credits).
- One enquiry may fan out to multiple eligible sellers (BRD RULE-7).

### 17.2 Open Items

| ID | Open Item | Owner | Status |
|----|-----------|-------|--------|
| OI-T1 | Ratify final technology stack & cloud provider (align with org standards). | Architecture | Open |
| OI-T2 | Confirm payment gateway choice (Razorpay) and e-NACH provider for Phase 2. | Eng / Finance | Open |
| OI-T3 | Finalise the **Product Quality Score** weighting model (mirrors BRD OI-3). | Product / Eng | Open |
| OI-T4 | Confirm SLAs/NFR targets (availability, RPO/RTO, latency) in §15. | Eng / Sponsor | Open |
| OI-T5 | Decide search strategy threshold for Postgres FTS → OpenSearch migration. | Eng | Open |
| OI-T6 | Confirm whether any Phase 2 module must move into Phase 1 (mirrors BRD OI-5) and adjust build order. | Product / Eng | Open |

---

## 18. Glossary

| Term | Definition |
|------|------------|
| Modular monolith | Single deployable backend with strongly separated internal modules. |
| RBAC | Role-Based Access Control. |
| FTS | Full-Text Search (Postgres native search). |
| BullMQ | Redis-based job/queue library for async workers. |
| e-NACH | Electronic mandate for recurring payment authorisation (India). |
| Quality Score | Computed product score (0–100%) gating Featured eligibility (≥ 70%). |
| SSR / SSG | Server-Side Rendering / Static Site Generation (SEO for buyer pages). |
| IaC | Infrastructure as Code (Terraform). |
| RPO / RTO | Recovery Point / Recovery Time Objective. |

---

*End of Document — TAD v1.0 (Draft for Review)*
