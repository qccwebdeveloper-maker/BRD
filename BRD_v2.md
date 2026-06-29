# eB2BMart Portal — Business Requirements Document (BRD)

**Version:** 2.0
**Status:** Draft for Review
**Date:** 26 June 2026
**Prepared by:** Product / Business Analysis
**Source Reference:** eB2BMart Portal – Functional Requirement Document (FRD), full clean version
**Supersedes:** BRD v1.0

---

## 1. Document Control

### 1.1 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 26-Jun-2026 | BA Team | Initial BRD from FRD: objectives, scope, stakeholders, high-level requirements, KPIs. |
| 2.0 | 26-Jun-2026 | BA Team | Reworked against the full clean FRD. Adds **module-complete requirement coverage** (all Seller/Buyer/Admin modules), a **measurable KPI appendix** (formula/source/target), a **phased release roadmap**, a **domain/data model**, an **admin RACI**, expanded business rules, and full BRD→FRD traceability. |

### 1.2 Approvals

| Name | Role | Decision | Date |
|------|------|----------|------|
| _TBD_ | Business Owner / Sponsor | Pending | |
| _TBD_ | Product Manager | Pending | |
| _TBD_ | Technical Lead | Pending | |
| _TBD_ | Finance / Compliance | Pending | |

### 1.3 Purpose & Audience
This BRD states **what the business needs and why**, in business terms, for sponsors and the delivery team. It sits above the FRD (which defines *how* each screen behaves). v2 ensures **every FRD module is represented** by at least one business requirement and a measurable outcome, and adds the planning artifacts needed to scope delivery.

---

## 2. Executive Summary

eB2BMart is a **B2B lead-generation marketplace** (IndiaMART / Aajjo model) connecting **buyers** with **verified sellers**. Its commercial engine is **enquiry capture → lead distribution**: buyer enquiries are stored, qualified, and distributed to sellers by **subscription-package entitlement** and **admin-defined rules**, with **pay-per-lead** top-ups and **featured/premium** upsells.

Three integrated portals deliver this:
1. **Seller Panel** — manage profile, products, leads, subscriptions, support (the paying side).
2. **Buyer Panel** — search, enquire, shortlist, track (the demand side, free).
3. **Admin Panel** — govern users, leads, products, packages, finance, content, configuration.

**Revenue model:** seller subscription packages + buy-lead credits + featured listings/advertisement credits + premium services. **Buyers are free** to maximise demand-side liquidity.

---

## 3. Business Context & Problem Statement

### 3.1 Opportunity
B2B procurement is fragmented and offline. Buyers can't quickly find credible suppliers; sellers can't reliably source qualified, intent-rich leads.

### 3.2 Problem
- **Buyers:** no single trusted place to discover verified suppliers and raise spec/quantity-aware enquiries.
- **Sellers:** no predictable, measurable channel of qualified leads, nor tooling to convert them.
- **Operators:** no centralised system to monetise enquiries, verify credibility, moderate content, and govern at scale.

### 3.3 Solution
A multi-sided marketplace where enquiries become **monetisable leads** distributed by package + rules, backed by verification, product quality scoring, reviews/Q&A, and complete admin governance.

---

## 4. Business Objectives & Goals

| # | Objective | Rationale | Primary KPI(s) (defined in §16) |
|---|-----------|-----------|----------------------------------|
| BO-1 | Monetise enquiries via packages + pay-per-lead | Primary revenue | MRR, ARPU, lead-credit revenue |
| BO-2 | Maximise qualified lead volume & distribution efficiency | Marketplace liquidity | Leads generated, % distributed, distribution latency |
| BO-3 | Improve buyer–seller match quality | Two-sided retention | Lead→Contact→Conversion funnel |
| BO-4 | Build trust via verification, reviews, quality scoring | Differentiation | % verified sellers, avg. quality score, avg. rating |
| BO-5 | Enable scalable operations through Admin | Control cost-to-serve | Approval/verification SLA, tickets resolved |
| BO-6 | Grow and retain both bases | Network effects | MAU, seller renewal %, churn % |
| BO-7 | Ensure financial & regulatory compliance | India-first GST/e-NACH | Invoice accuracy, mandate success rate |

> *BO-7 is new in v2*, reflecting the FRD's explicit GST/PAN/CIN, invoicing, and **e-NACH** requirements.

---

## 5. Project Scope

### 5.1 In Scope (Version 1 build)

| Portal | Modules (FRD ref) |
|--------|-------------------|
| **Seller** | Leads Management (1.1), My Account (1.2), Product Management (1.3), Reports (1.4), Contact Us (1.5), Reviews (1.6), Package Management (1.7), Notifications (1.8), Q&A (1.9), Support Tickets (1.10) |
| **Buyer** | Dashboard (2.1), My Profile (2.2), Enquiry Management (2.3), Wishlist (2.4), Reviews (2.6), Q&A (2.7), Favourite Sellers & Products (2.8), Report Seller (2.9), Notifications (2.10), Contact Support (2.12) |
| **Admin** | Dashboard (3.1), User Management (3.2), Seller Management (3.3), Package Management (3.5), Lead Management (3.6), Product Management (3.7), Review & Q&A Management (3.8), Billing & Finance (3.9), Reports & Analytics (3.10), Support Management (3.11), CMS (3.12), Notification Management (3.13), System Settings (3.14) |

### 5.2 Out of Scope (V1) — phased candidates
- Full transactional checkout / cart / order fulfilment (V1 is **lead-led**, not order-led). *Note: FRD 3.9 references "Convert Quotations into Orders" — treated as a finance/quotation artefact, not buyer-facing e-commerce checkout. See OI-2.*
- Integrated logistics / shipping execution.
- Native mobile apps (responsive web assumed for V1 — see OI-1).
- Multi-currency settlement / non-GST international tax engines.
- AI/predictive lead scoring beyond the rule-based distribution + Product Quality Score.

### 5.3 Assumptions
- **India-first**: GST, PAN, CIN, e-NACH, PIN code, INR.
- Sellers pay (prepaid packages + lead credits); **buyers are free**.
- Geography = **Country → State → City** (+ PIN).
- One enquiry may be distributed to **multiple** eligible sellers.
- Product media: JPG/PNG/WEBP; documents: catalogue, brochure, datasheet.

### 5.4 Constraints
- Lead distribution bounded by **package entitlements** + **admin rules**.
- Trash auto-purge fixed at **7 days**.
- Featured eligibility requires **Quality Score ≥ 70%** + enabling package (+ optional admin approval).
- GST/PAN/invoicing/e-NACH must comply with Indian regulation.

---

## 6. Stakeholders

| Stakeholder | Interest / Role |
|-------------|-----------------|
| Business Owner / Sponsor | Revenue, growth, ROI |
| Sellers (paying customers) | Qualified leads, visibility, conversions |
| Buyers (procurement) | Fast discovery of credible suppliers, quotations |
| Super Admin | Overall governance |
| Sales Executive | Seller acquisition, callbacks |
| Customer Support | Ticket resolution, seller assistance |
| Lead Manager | Lead qualification & distribution |
| Finance Executive | Quotations, invoicing, payments, e-NACH |
| Product Moderator | Product approval & quality |
| Content Manager | CMS, categories, banners |
| Marketing Executive | Campaigns, notifications, promotions |

---

## 7. User Roles & Access (RBAC)

### 7.1 Marketplace actors
- **Seller** — supplier converting distributed leads into sales.
- **Buyer** — procurement user raising enquiries and shortlisting suppliers.

### 7.2 Admin RACI (indicative — to be confirmed in System Settings)

| Activity | Super Admin | Sales Exec | Support | Lead Mgr | Finance | Product Mod | Content Mgr | Marketing |
|----------|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| User & role management | **A/R** | – | – | – | – | – | – | – |
| Seller verification/approval | A | C | R | – | – | – | – | – |
| Callback / registration follow-up | A | **R** | C | – | – | – | – | – |
| Lead distribution rules | A | – | – | **R** | – | – | – | – |
| Undistributed lead handling | A | C | – | **R** | – | – | – | – |
| Product approval / moderation | A | – | – | – | – | **R** | – | – |
| Review & Q&A moderation | A | – | C | – | – | R | C | – |
| Packages (master/active/trial) | **A/R** | C | – | – | C | – | – | – |
| Quotations / invoices / e-NACH | A | C | – | – | **R** | – | – | – |
| Support ticket handling | A | – | **R** | – | – | – | – | – |
| CMS / banners / pages | A | – | – | – | – | – | **R** | C |
| Notifications / broadcasts | A | – | C | – | – | – | C | **R** |
| System settings | **A/R** | – | – | – | – | – | – | – |

*A = Accountable, R = Responsible, C = Consulted. To be ratified by the Business Owner.*

---

## 8. Module-Complete Business Requirements

Each FRD module maps to ≥1 business requirement. Priority = MoSCoW (**M**ust/**S**hould/**C**ould).

### 8.1 Seller Panel

| ID | Business Requirement | FRD | Pri |
|----|----------------------|-----|-----|
| SR-1 | Sellers must view, search, sort, and filter all assigned leads with full buyer/requirement detail and lifecycle status. | 1.1 | M |
| SR-2 | Sellers must purchase additional leads (buy-lead credits) with price, balance, deduction, and instant assignment. | 1.1 | M |
| SR-3 | Sellers must update lead status where permitted (Undistributed→…→Converted/Closed/Expired). | 1.1 | M |
| SR-4 | Leads must be allocated strictly within active-package entitlements (monthly/daily limits, category, geography, priority, speed, credit/concurrency limits). | 1.1 | M |
| SR-5 | Sellers must maintain a complete company profile: details, four address types, contacts, GST/PAN/CIN + certificate upload, business profile, keywords. | 1.2 | M |
| SR-6 | Sellers must manage account settings (notification/email/SMS prefs, language, timezone) and credentials (password, optional 2FA, login history, remote logout). | 1.2 | S |
| SR-7 | Sellers must create/edit/duplicate/delete products with full attributes, multi-image (JPG/PNG/WEBP) and document uploads. | 1.3 | M |
| SR-8 | The system must compute a **Product Quality Score** on submission and surface improvement recommendations. | 1.3 | M |
| SR-9 | Deleted products must move to Trash and auto-purge after **7 days**; sellers may restore or permanently delete. | 1.3 | M |
| SR-10 | Sellers must define **dealing areas** (Country/State/City) influencing search relevance and targeting. | 1.3 | M |
| SR-11 | Eligible sellers must promote products to **Featured** (Score ≥ 70% + enabling package + optional admin approval). | 1.3 | S |
| SR-12 | Sellers must access Reports: lead, product performance, enquiry, and conversion (incl. estimated revenue). | 1.4 | M |
| SR-13 | Sellers must contact support via multiple channels (phone, email, live chat, support form, callback, feedback) incl. assigned account manager. | 1.5 | S |
| SR-14 | Sellers must view product/company reviews, ratings & distribution, reply (if enabled), and report fake reviews. | 1.6 | S |
| SR-15 | Sellers must view/compare/upgrade/renew packages, view entitlements/credits/expiry, download invoices, view payment history. | 1.7 | M |
| SR-16 | Sellers must receive a notification centre (lead, package, product, payment, promo, system events) with read/delete/history. | 1.8 | M |
| SR-17 | Sellers must view buyer questions, answer, edit before moderation, and track status. | 1.9 | S |
| SR-18 | Sellers must raise and track support tickets across categories with full status lifecycle and history. | 1.10 | M |

### 8.2 Buyer Panel

| ID | Business Requirement | FRD | Pri |
|----|----------------------|-----|-----|
| BR-1 | Buyers must have a dashboard summarising enquiries, favourites, recently viewed, profile completion, notifications, and activity, with global search. | 2.1 | M |
| BR-2 | Buyers must manage profile: personal, company, contact, billing/shipping address, account settings. | 2.2 | S |
| BR-3 | Buyers must create enquiries (product/category, requirement, quantity, documents, preferred supplier location) routed by distribution rules. | 2.3 | M |
| BR-4 | Buyers must view/search/filter their enquiries, view detail, re-submit, close, and see status. | 2.3 | M |
| BR-5 | Buyers must maintain a wishlist of products/suppliers and enquire directly from it. | 2.4 | S |
| BR-6 | Buyers must submit/view/edit/delete product & seller reviews within policy. | 2.6 | S |
| BR-7 | Buyers must ask product questions, view responses, and delete where permitted. | 2.7 | S |
| BR-8 | Buyers must manage favourite sellers/products and enquire directly. | 2.8 | S |
| BR-9 | Buyers must report fraudulent/misleading/spam sellers with evidence and optional status tracking. | 2.9 | M |
| BR-10 | Buyers must receive notifications (responses, status, promos, announcements) with read/delete/history. | 2.10 | M |
| BR-11 | Buyers must raise, track, respond to, and close support tickets with attachments. | 2.12 | M |

### 8.3 Admin Panel

| ID | Business Requirement | FRD | Pri |
|----|----------------------|-----|-----|
| AR-1 | Admin must have a real-time dashboard: user, lead, package, revenue, and operational (pending approvals/verifications/tickets) statistics. | 3.1 | M |
| AR-2 | Admin must manage internal users with RBAC (CRUD, roles, permissions, activation, password reset, login history) across 8 defined roles. | 3.2 | M |
| AR-3 | Admin must manage sellers: view/search/filter/edit, verify documents, approve, activate, suspend, delete. | 3.3 | M |
| AR-4 | Admin must manage blocked/flagged sellers (block/unblock, remarks, suspension & complaint history). | 3.3 | M |
| AR-5 | Admin must manage callback/registration enquiries (assign sales exec, follow-up status, schedule, close). | 3.3 | S |
| AR-6 | Admin must verify GST, PAN, documents, address, mobile, email. | 3.3 | M |
| AR-7 | Admin must moderate seller products (view/approve/reject/edit/delete/restore/flag/ban). | 3.3, 3.7 | M |
| AR-8 | Admin must manage seller dealing areas (view/modify/restrict regions). | 3.3 | S |
| AR-9 | Admin must manage packages: master (CRUD + activate/deactivate with full config), active subscribers (extend/suspend/upgrade/renew), and trials (create/monitor/convert). | 3.5 | M |
| AR-10 | Admin must manage the full lead lifecycle: view/search/edit/delete/merge, handle undistributed (assign/schedule/mark invalid), configure distribution rules, and convert call enquiries to leads. | 3.6 | M |
| AR-11 | Admin must manage product taxonomy: categories (hierarchy), product types, brands, attributes/specs/groups. | 3.7 | M |
| AR-12 | Admin must moderate reviews and Q&A (approve/reject/edit/delete inappropriate). | 3.8 | M |
| AR-13 | Admin must manage finance: quotations (create/modify/convert), invoices (generate/download/email/cancel), payments (history/verify), and **e-NACH** (create/monitor/track). | 3.9 | M |
| AR-14 | Admin must generate sales, package, lead, product, and user reports with time-based breakdowns. | 3.10 | M |
| AR-15 | Admin must manage support operations: seller settings, **client checklist** (incomplete profile/SEO/media/docs, reminders), and ticket handling. | 3.11 | M |
| AR-16 | Admin must manage CMS: banners (add/schedule/remove) and pages (legal, blogs, FAQ, careers, custom). | 3.12 | M |
| AR-17 | Admin must manage notifications: push/email/SMS, templates, scheduling, broadcasts. | 3.13 | M |
| AR-18 | Admin must configure platform settings: general (static pages, contacts, social, branding) and marketplace (distribution rules, default package). | 3.14 | M |

---

## 9. Key Business Process Flows

### 9.1 Lead Lifecycle (core monetisation)
```
Buyer submits enquiry
        ↓
Lead stored in Lead Database
        ↓
Lead Qualification (rules / admin)
        ↓
Automatic Distribution  ── bounded by package entitlement + admin rules
        ↓
Seller Notification
        ↓
Seller acts → status: Distributed → Viewed → Contacted → Follow-up
        ↓                       → Converted / Closed / Expired
Quota exhausted? → Buy lead credits  OR  Upgrade package
```

### 9.2 Seller Onboarding & Monetisation
```
Registration / Callback enquiry → Sales follow-up → Documents submitted
→ Admin verification (GST/PAN/docs/address) → Approval / Activation
→ Package purchase (incl. Trial) → Product listing + Quality scoring
→ Lead distribution begins → Renewal / Upgrade
```

### 9.3 Product Publication & Quality Gating
```
Seller adds product → System computes Quality Score (title, desc, images,
specs, price, keywords, brand, category)
→ Score ≥ 70%: Featured-eligible + improved search visibility
→ Score < 70%: normal listing, Featured-ineligible (+ recommendations)
→ Admin moderation: approve / reject / flag / ban
```

### 9.4 Finance & Recurring Billing
```
Quotation → (accept) → Invoice generated → Payment / e-NACH mandate
→ Payment verified → Package activated/renewed → Invoice emailed/downloadable
```

---

## 10. Consolidated Business Rules

| ID | Rule | FRD |
|----|------|-----|
| RULE-1 | Sellers cannot receive leads beyond package limits (daily/monthly). | 1.1 |
| RULE-2 | Higher-tier packages get higher lead priority and allocation. | 1.1 |
| RULE-3 | Exhausted lead quota requires buying credits or upgrading the package. | 1.1 |
| RULE-4 | Product Quality Score ≥ 70% is required for Featured eligibility. | 1.3 |
| RULE-5 | Trashed products are auto-deleted after 7 days. | 1.3 |
| RULE-6 | Featured status may require admin approval per platform policy. | 1.3 |
| RULE-7 | Only registered buyers can submit enquiries; one enquiry may reach multiple eligible sellers. | 2.x |
| RULE-8 | Buyers may submit unlimited enquiries unless policy restricts. | 2.x |
| RULE-9 | Reported sellers are reviewed by Admin before any action. | 2.9 |
| RULE-10 | Support tickets stay open until resolved/closed by buyer or support. | 1.10/2.12 |
| RULE-11 | GST/profile changes may trigger admin re-verification. | 1.2 |
| RULE-12 | Reviews & buyer questions may require moderation before public display. | 1.6/1.9/3.8 |
| RULE-13 | Only buyers from a seller's dealing areas get higher search relevance. | 1.3 |

---

## 11. Phased Release Roadmap (Recommended)

A pragmatic sequencing to reach a monetisable marketplace fastest. Subject to OI decisions.

| Phase | Theme | Includes | Goal |
|-------|-------|----------|------|
| **P1 — Marketplace MVP** | Capture & distribute leads | Seller: 1.2, 1.3 (incl. Quality Score), 1.1 (view + status); Buyer: 2.1, 2.2, 2.3, search; Admin: 3.1, 3.2, 3.3 (verify/approve), 3.6 (rules + distribution), 3.7 (taxonomy + approval), 3.14 | Buyers can enquire; verified sellers receive distributed leads |
| **P2 — Monetisation** | Turn leads into revenue | Seller: 1.1 (buy leads), 1.7; Admin: 3.5 (packages/trials), 3.9 (quotations/invoices/payments/**e-NACH**); Notifications 1.8/2.10/3.13 | Packages + lead credits generate revenue |
| **P3 — Trust & Engagement** | Differentiate & retain | Reviews 1.6/2.6/3.8, Q&A 1.9/2.7, Featured 1.3E, Wishlist/Favourites 2.4/2.8, Report Seller 2.9, Support 1.10/2.12/3.11 | Higher match quality, retention, trust |
| **P4 — Scale & Optimise** | Operate efficiently | Reports & Analytics 1.4/3.10, CMS 3.12, Call enquiries 3.6E, Client Checklist 3.11, advanced distribution tuning | Operational efficiency, growth analytics |

---

## 12. High-Level Domain / Data Model

Core business entities and key relationships (for shared understanding; detailed schema in tech spec).

```
Buyer ──submits──> Enquiry ──becomes──> Lead ──distributed to──> Seller
Seller ──owns──> Product ──belongs to──> Category/Subcategory, Brand, ProductType
Seller ──subscribes to──> Package (Master) ──instance──> Subscription (Active/Trial)
Subscription ──governs──> Lead Allocation + Credits + Featured/Product limits
Lead ──has──> Status (lifecycle)
Product ──has──> QualityScore, DealingAreas, Media, Documents
Seller/Buyer ──raise──> SupportTicket ──assigned to──> Admin User (role)
Buyer ──writes──> Review / Question ──moderated by──> Admin
Subscription/Payment ──generates──> Quotation, Invoice, e-NACH Mandate, Transaction
Admin User ──has──> Role ──grants──> Permissions
```

**Key entities:** Buyer, Seller, Product, Category, Brand, ProductType, Attribute, Enquiry, Lead, Package, Subscription, LeadCredit, Review, Question, Notification, SupportTicket, Quotation, Invoice, Payment, e-NACH Mandate, AdminUser, Role, CMSPage, Banner, DistributionRule.

---

## 13. Non-Functional Expectations (Business-Level)

- **Security & Compliance:** Protect PII, GST/PAN/CIN, payment & e-NACH data; RBAC; audit/login history.
- **Performance:** Search, enquiry submission, and lead distribution must be responsive at scale.
- **Scalability:** Support growth in sellers, products, and lead volume.
- **Availability:** High uptime for revenue-critical flows (enquiry, distribution, payment).
- **Usability:** Tailored per audience — non-technical buyers/sellers, operational admins.
- **Auditability:** Trace verification, suspension, distribution, and financial actions.
- **Localisation readiness:** Per-user language & timezone.

---

## 14. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Poor lead quality erodes seller trust | Churn, refunds | Qualification, invalid-lead marking, conversion reporting, dealing-area targeting |
| Cold-start (thin liquidity) | Low value both sides | Sales-led acquisition, trial packages, marketing broadcasts |
| Fraudulent sellers / fake listings | Reputation | Verification, reporting, moderation, flag/ban |
| Payment / e-NACH failures | Revenue leakage | Verification, status tracking, finance oversight (BO-7) |
| Over-distribution of leads | Seller dissatisfaction | Package limits + admin rules (RULE-1/2) |
| Regulatory non-compliance (GST/e-NACH) | Legal exposure | Compliance-aligned invoicing & mandates |
| Self-reported lead conversion data unreliable | Skewed KPIs | Encourage status discipline; corroborate with enquiry/response data |

---

## 15. Open Items / Decisions Required

| ID | Open Item | Owner | Status |
|----|-----------|-------|--------|
| OI-1 | V1 channel: responsive web only, or native mobile apps too? | Sponsor/Product | Open |
| OI-2 | Is order/checkout deferred? (FRD 3.9 mentions "Convert Quotations into Orders".) Clarify lead-led vs order-led. | Product | Open |
| OI-3 | Exact Product Quality Score weighting model across the 8 factors. | Product/Eng | Open |
| OI-4 | Confirm buyers remain free (no buyer monetisation in V1). | Business | Open |
| OI-5 | Launch geography/languages beyond India-first. | Business | Open |
| OI-6 | **FRD numbering gaps**: Buyer 2.5 & 2.11 and Admin 3.4 & seller sub-section F are absent. Confirm no modules were dropped. | BA | Open |
| OI-7 | Confirm RACI in §7.2 and bind to System Settings permissions. | Business Owner | Open |
| OI-8 | Define package tiers (count, price points, entitlements) for P2. | Business/Finance | Open |

---

## 16. KPI Definitions Appendix

Each objective metric with formula, data source, frequency, and an indicative target to be ratified.

| KPI | Definition / Formula | Data Source | Frequency | Indicative Target |
|-----|----------------------|-------------|-----------|-------------------|
| **MRR** | Σ active subscription value normalised to month | Billing/Subscriptions | Monthly | Set at P2 launch |
| **ARPU** | Total seller revenue ÷ active paying sellers | Billing | Monthly | Grow QoQ |
| **Lead-credit revenue** | Σ buy-lead credit purchases | Lead credit ledger | Monthly | — |
| **Leads generated** | Count of enquiries captured as leads | Lead DB | Daily/Monthly | Growth MoM |
| **% Distributed** | Distributed leads ÷ total leads × 100 | Lead DB | Daily | ≥ 95% |
| **Distribution latency** | Avg. time enquiry → seller notification | Lead engine logs | Weekly | Minutes, not hours |
| **Contact rate** | Leads "Contacted" ÷ leads "Distributed" × 100 | Lead status | Monthly | ≥ 50% (baseline TBC) |
| **Conversion rate** | Leads "Converted" ÷ leads "Distributed" × 100 | Lead status | Monthly | ≥ 10% (baseline TBC) |
| **Avg. Product Quality Score** | Mean score across active products | Product engine | Monthly | ≥ 70% median |
| **% Verified sellers** | Verified sellers ÷ total sellers × 100 | Seller mgmt | Monthly | ≥ 80% of active |
| **Avg. rating** | Mean approved review rating | Reviews | Monthly | ≥ 4.0 / 5 |
| **MAU** | Unique buyers + sellers with a meaningful action in month | Activity logs | Monthly | Growth MoM |
| **Seller renewal %** | Sellers renewed ÷ sellers due for renewal × 100 | Subscriptions | Monthly | ≥ 70% |
| **Churn %** | Sellers lapsed ÷ active sellers at period start × 100 | Subscriptions | Monthly | Trend down |
| **Trial→Paid %** | Trials converted ÷ trials expired × 100 | Packages | Monthly | ≥ 25% |
| **Approval/Verification SLA** | Avg. time to approve product / verify seller | Admin queues | Weekly | < 24–48h |
| **Ticket resolution time** | Avg. open → resolved duration | Support | Weekly | Per SLA tier |
| **e-NACH success rate** | Successful mandates ÷ attempted × 100 | Finance | Monthly | ≥ 90% |

> "Meaningful action" for MAU = login **plus** an action (enquiry submitted, lead acted on, product edited), not bare login — recommended to keep MAU honest.

---

## 17. Traceability (BRD → FRD)

| Business Capability | Seller (1.x) | Buyer (2.x) | Admin (3.x) |
|---------------------|--------------|-------------|-------------|
| Lead engine | 1.1 | 2.3 | 3.6 |
| Profile & verification | 1.2 | 2.2 | 3.3 |
| Catalogue & quality | 1.3 | — | 3.7 |
| Discovery & enquiry | — | 2.1, 2.3, 2.4, 2.8 | — |
| Packages & billing | 1.7 | — | 3.5, 3.9 |
| Reviews & Q&A | 1.6, 1.9 | 2.6, 2.7 | 3.8 |
| Notifications | 1.8 | 2.10 | 3.13 |
| Support | 1.5, 1.10 | 2.12 | 3.11 |
| Trust / safety | 1.6 | 2.9 | 3.3, 3.8 |
| Reporting | 1.4 | 2.1 | 3.1, 3.10 |
| Content & config | — | — | 3.2, 3.12, 3.14 |

---


test = test 101

