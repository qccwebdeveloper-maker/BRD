# eB2BMart Portal — Business Requirements Document (BRD)

**Version:** 3.0
**Status:** Draft for Review
**Date:** 26 June 2026
**Prepared by:** Product / Business Analysis
**Source Reference:** eB2BMart Portal – Functional Requirement Document (FRD)

---

## 1. Document Control

### 1.1 Version History

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0 | 26-Jun-2026 | BA Team | Initial BRD derived from the eB2BMart FRD. Defines business objectives, scope, stakeholders, high-level requirements, and success metrics. |
| 1.1 | 26-Jun-2026 | BA Team | Removed objective BO-1 (enquiry monetisation) and BO-3 (match quality) from the Business Objectives table; removed the Out of Scope subsection and renumbered the Scope subsections accordingly. |
| 3.0 | 26-Jun-2026 | BA Team | Introduced a **two-phase delivery model**. Scope and high-level requirements are now split into **Phase 1 (core MVP)** and **Phase 2 (enhancements)**, derived from the FRD: un-highlighted FRD items map to Phase 1; highlighted FRD items map to Phase 2. Added the Future Enhancements list to Phase 2. |

### 1.2 Approvals

| Name | Role | Decision | Date |
|------|------|----------|------|
| _TBD_ | Business Owner / Sponsor | Pending | |
| _TBD_ | Product Manager | Pending | |
| _TBD_ | Technical Lead | Pending | |

### 1.3 Document Purpose

This BRD describes **what the business needs** from the eB2BMart platform and **why**, expressed in business terms for stakeholders, sponsors, and the delivery team. It precedes and complements the FRD (which defines *how* the system behaves at the functional level).

**This version (3.0) organises scope into two delivery phases** so the platform can launch with a focused core and grow incrementally:

- **Phase 1 — Core MVP:** the minimum capability set required to operate the marketplace end-to-end (onboard sellers, list products, capture and distribute leads, take payment, and support users).
- **Phase 2 — Enhancements:** trust/engagement features, advanced admin governance, advanced reporting, CMS, recurring-payment automation, and the recommended future enhancements.

---

## 2. Executive Summary

eB2BMart is a **B2B online marketplace** (similar in model to IndiaMART / Aajjo) that connects **buyers** seeking products and services with **verified sellers/suppliers**. The platform's core commercial engine is **lead generation and distribution**: buyer enquiries are captured, qualified, and distributed to sellers based on paid **subscription packages**, with additional **pay-per-lead** purchasing.

Revenue is driven primarily by **seller subscription packages**, **buy-lead credits**, and **featured/premium listings**. The platform comprises three integrated portals:

1. **Seller Panel** — suppliers manage profiles, products, leads, subscriptions, and support.
2. **Buyer Panel** — buyers search, enquire, shortlist suppliers, and track responses.
3. **Admin Panel** — operators manage users, leads, packages, payments, content, and platform configuration.

This document establishes the business case, scope, and **phased requirements** for the platform, splitting delivery into a **Phase 1 core MVP** and a **Phase 2 enhancement** release.

---

## 3. Business Context & Problem Statement

### 3.1 Market Opportunity
B2B procurement in India and similar markets is fragmented, relationship-driven, and largely offline. Buyers struggle to discover credible suppliers quickly; suppliers struggle to generate qualified, intent-rich leads cost-effectively.

### 3.2 Problem Statement
- **Buyers** lack a single, trusted place to discover verified suppliers, compare them, and raise enquiries with quantity/specification context.
- **Sellers** lack a predictable, measurable channel for qualified buyer enquiries and the tooling to manage and convert them.
- **Marketplace operators** lack a centralised system to monetise enquiries, verify supplier credibility, moderate content, and govern the marketplace at scale.

### 3.3 Proposed Solution
A multi-sided B2B marketplace where buyer enquiries become **monetisable leads** distributed to sellers by **package entitlement and admin-defined rules**, supported by supplier verification, product quality scoring, reviews/Q&A, and full administrative governance — delivered incrementally across two phases.

---

## 4. Business Objectives & Goals

| # | Objective | Rationale | Indicative Success Measure |
|---|-----------|-----------|----------------------------|
| BO-1 | Maximise qualified lead volume and distribution efficiency | Marketplace liquidity | Leads generated, % distributed, distribution latency |
| BO-2 | Build trust via verification, reviews, and quality scoring | Credibility differentiates from unverified channels | % verified sellers, avg. product quality score |
| BO-3 | Enable scalable operations through the Admin Panel | Control cost-to-serve as GMV grows | Tickets resolved, approvals SLA |
| BO-4 | Grow and retain both buyer and seller bases | Network effects | MAU, seller renewals, churn |

---

## 5. Phased Delivery Approach

The full FRD scope is delivered across two phases. **Phase 1** establishes a launchable, revenue-capable marketplace; **Phase 2** layers on engagement, governance depth, advanced analytics, content management, and recommended future enhancements.

### 5.1 Phasing Principles
- **Phase 1 = un-highlighted FRD scope** — the core operational backbone needed to go live.
- **Phase 2 = highlighted FRD scope** — enhancements that increase trust, monetisation depth, operational control, and reporting maturity, plus the recommended future-enhancement backlog.
- Phase 2 items depend on the corresponding Phase 1 foundations (e.g. buy-leads depends on the Phase 1 lead engine; review/Q&A moderation depends on Phase 1 catalogue and accounts).

### 5.2 Module Phasing Map (FRD → Phase)

| Panel | Module (FRD) | Phase 1 | Phase 2 |
|-------|--------------|:-------:|:-------:|
| Seller | 1.1 Leads Management | View all leads received | Buy Leads (per subscription); lead status tracking (distributed / un-distributed; distributed-to-count) |
| Seller | 1.2 My Account | ✅ Full | — |
| Seller | 1.3 Product Management | Add, All, Trash, Dealing Areas | Featured Products |
| Seller | 1.4 Reports | Lead, Product Performance, Enquiry | Conversion Reports |
| Seller | 1.5 Contact Us | ✅ Full | — |
| Seller | 1.6 Reviews | — | View reviews; monitor ratings & feedback |
| Seller | 1.7 Package Management | ✅ Full | — |
| Seller | 1.8 Notifications | ✅ Full | — |
| Seller | 1.9 Questions & Answers | — | View buyer questions; submit responses |
| Seller | 1.10 Support Tickets | ✅ Full | — |
| Buyer | 2.1 Dashboard | ✅ Full | — |
| Buyer | 2.2 My Profile | ✅ Full | — |
| Buyer | 2.3 Wishlist | ✅ Full | — |
| Buyer | 2.4 Reviews | — | View / edit own reviews |
| Buyer | 2.5 Questions & Answers | — | View questions; track seller responses |
| Buyer | 2.6 Report Seller | — | Report suspicious sellers; submit complaints |
| Buyer | 2.7 Notifications | System Announcements | Seller Responses |
| Admin | 3.1 Dashboard | Sellers, Buyers, Packages, Leads, Revenue, Product Stats | Pending Tasks |
| Admin | 3.2 User Management | ✅ Full | — |
| Admin | 3.3 Seller Management | View / edit registered sellers | Activate/Deactivate; Blocked/Flagged; Callback Requests; Seller Product Deletion; Featured Products Mgmt; Dealing Area Mgmt |
| Admin | 3.4 Data Bank | ✅ Full | — |
| Admin | 3.5 Package Management | ✅ Full (Active, Trial) | — |
| Admin | 3.6 Lead Management | Distributed & Undistributed Leads | Call Enquiries |
| Admin | 3.7 Product Management | ✅ Full (Add, Active, Inactive, Trash, Category, Type) | — |
| Admin | 3.8 Review & Q&A Management | — | ✅ Full (review & Q&A moderation) |
| Admin | 3.9 Billing Management | Quotations, Invoices | e-NACH Management |
| Admin | 3.10 Reports | — | ✅ Full (Sales, Package, Lead, Product reports) |
| Admin | 3.11 Support Management | Seller Settings, Client Checklist, Tickets | Banner Management; Page/CMS Management |
| Platform | Future Enhancements | — | ✅ Full (see 8.9) |

---

## 6. Project Scope

### 6.1 In Scope — Phase 1 (Core MVP)

**Seller Panel**
- Leads Management — view all leads received from buyers
- My Account — company, address, contact, GST/PAN, business profile, account settings, login credentials
- Product Management — add product (with quality score), all/active products, trash (7-day purge), dealing areas
- Reports — lead reports, product performance reports, enquiry reports
- Contact Us — support team & assigned account manager
- Package Management — view active package, upgrade, renew, validity/expiry
- Notifications — package expiry, lead assignments, product approvals, system announcements, promotional offers, own-product-deletion
- Support Tickets — raise, track status, view history

**Buyer Panel**
- Dashboard — enquiries, favourites/wishlist access, search, recent activity
- My Profile — personal info, contact details & preferences
- Wishlist — saved products/suppliers, enquire from wishlist
- Notifications — system announcements

**Admin Panel**
- Dashboard — total sellers, total buyers, active packages, total leads, revenue summary, product statistics
- User Management — create users, roles & permissions, employee access levels
- Seller Management — view all registered sellers, edit seller details
- Data Bank — view/export paid-seller database
- Package Management — active packages, trial packages & expiry
- Lead Management — distributed leads (view), undistributed leads (view, manual assign)
- Product Management — add, active, inactive, trash, category management, product type management
- Billing Management — quotations, invoices
- Support Management — seller settings, client checklist, support tickets

### 6.2 In Scope — Phase 2 (Enhancements)

**Seller Panel**
- Leads Management — **Buy Leads** and purchase leads per subscription package; **lead status tracking** (distributed / un-distributed, and if distributed, distributed to how many sellers)
- Product Management — **Featured Products** (upgrade products to Featured Listing for visibility)
- Reports — **Conversion Reports** (distributed lead → sales conversion)
- Reviews — view buyer reviews; monitor ratings and feedback
- Questions & Answers — view buyer product questions; submit responses

**Buyer Panel**
- Reviews — view all reviews submitted by the buyer; edit/update reviews (if permitted)
- Questions & Answers — view all questions submitted to sellers; track seller responses
- Report Seller — report suspicious sellers; submit complaints to moderation team
- Notifications — seller responses

**Admin Panel**
- Dashboard — Pending Tasks
- Seller Management — Activate/Deactivate accounts; Blocked/Flagged sellers; Callback Requests (registration enquiries + follow-up); Seller Product Deletion; Featured Products Management; Dealing Area Management
- Lead Management — Call Enquiries (phone enquiry records, outcome tracking)
- Review & Q&A Management — moderate product reviews; moderate product questions & answers
- Billing Management — **e-NACH Management** (generate/monitor mandates, payment authorisation status)
- Reports — **Sales Reports** (daily/weekly/monthly, revenue analysis); **Package Reports** (package sales daily/weekly/monthly, quotation report, package conversion); **Lead Reports** (total leads, undistributed, lead estimation per seller); **Product Reports** (listing statistics, performance analysis, category-wise)
- Support Management — **Banner Management**; **Page/CMS Management** (About Us, Contact Us, Privacy Policy, Terms & Conditions, Refund Policy, other CMS pages)

**Future Enhancements (Recommended)** — see §8.9.

### 6.3 Assumptions
- The platform operates primarily in an **India-first** context (GST, PAN, CIN, e-NACH, INR).
- Sellers transact via **prepaid subscription packages** and **lead credits**; buyers use the platform free of charge.
- Geography is modelled as **Country → State → City**.
- A buyer enquiry may be distributed to **multiple** eligible sellers.
- Phase 2 features build on Phase 1 foundations and are scheduled after Phase 1 go-live.

### 6.4 Constraints
- Regulatory: GST/PAN handling, invoicing, e-NACH mandate processing must comply with Indian norms (e-NACH lands in Phase 2).
- Lead distribution is bounded by **package entitlements** and **admin-configured rules**.
- Trash retention auto-purge fixed at **7 days** for deleted products.
- Featured eligibility requires a **Product Quality Score ≥ 70%** and an enabling package (Featured workflow delivered in Phase 2).

---

## 7. Stakeholders

| Stakeholder | Interest / Role |
|-------------|-----------------|
| Business Owner / Sponsor | Revenue, growth, ROI |
| Sellers / Suppliers (paying customers) | Qualified leads, visibility, conversions |
| Buyers / Procurement users | Fast discovery of credible suppliers, quotations |
| Super Admin | Overall platform governance |
| Sales Executive | Seller acquisition, callbacks, conversions |
| Customer Support | Ticket resolution, seller assistance |
| Lead Manager | Lead qualification & distribution |
| Finance Executive | Invoicing, payments, e-NACH |
| Product Moderator | Product approval & quality control |
| Content Manager | CMS, categories, banners |
| Marketing Executive | Campaigns, notifications, promotions |

---

## 8. High-Level Business Requirements

Requirements are grouped by business capability and **tagged by phase** (P1 / P2). Each maps to detailed functional behaviour in the FRD. Priority uses MoSCoW (**M**ust / **S**hould / **C**ould).

### 8.1 Lead Generation, Monetisation & Distribution — *the core engine*

| ID | Business Requirement | Phase | Priority |
|----|----------------------|:-----:|:--------:|
| BR-L1 | The platform must capture buyer enquiries as leads and store them centrally. | P1 | M |
| BR-L2 | The platform must distribute leads to eligible sellers, based on subscription package entitlements and admin-defined rules. | P1 | M |
| BR-L3 | Sellers must be able to view all leads received/assigned to them. | P1 | M |
| BR-L4 | Admins must be able to view and manually assign undistributed leads. | P1 | M |
| BR-L5 | Sellers must be able to purchase additional leads (**Buy Leads** / credits) as per their subscription package. | P2 | M |
| BR-L6 | Every lead must carry trackable status (distributed / un-distributed), and for distributed leads, the count of sellers it was distributed to. | P2 | M |
| BR-L7 | Admins must be able to record and track **call/phone enquiries** and their outcomes, converting them into leads. | P2 | S |

### 8.2 Seller Onboarding, Profile & Verification

| ID | Business Requirement | Phase | Priority |
|----|----------------------|:-----:|:--------:|
| BR-S1 | Sellers must maintain a complete business profile (company details, address, contacts, GST/PAN, business profile, settings, credentials). | P1 | M |
| BR-S2 | Admins must be able to view and edit registered seller details. | P1 | M |
| BR-S3 | Admins must maintain a Data Bank of paid sellers with export capability. | P1 | M |
| BR-S4 | Admins must be able to activate/deactivate, block, and manage flagged seller accounts. | P1 | M |
| BR-S5 | Admins must handle **callback / registration enquiries** and assign follow-up actions. | P2 | S |
| BR-S6 | Admins must be able to delete seller products when required, and manage seller dealing areas. | P2 | S |

### 8.3 Product Catalogue & Quality

| ID | Business Requirement | Phase | Priority |
|----|----------------------|:-----:|:--------:|
| BR-P1 | Sellers must add, edit, list, trash, and restore products with full attributes and media; products in Trash auto-purge after 7 days. | P1 | M |
| BR-P2 | The platform must compute a **Product Quality Score** on submission and gate Featured eligibility at **≥ 70%**. | P1 | M |
| BR-P3 | Sellers must define **dealing areas** (Country/State/City) influencing search relevance and lead targeting. | P1 | M |
| BR-P4 | Admins must manage products end-to-end (add, active, inactive, trash) and maintain category and product-type hierarchies. | P1 | M |
| BR-P5 | Eligible sellers must be able to promote products to **Featured Listings** for better visibility. | P2 | S |
| BR-P6 | Admins must approve and manage featured product listings. | P2 | S |

### 8.4 Buyer Discovery & Enquiry

| ID | Business Requirement | Phase | Priority |
|----|----------------------|:-----:|:--------:|
| BR-B1 | Buyers must search products/suppliers and submit enquiries from dashboard and wishlist. | P1 | M |
| BR-B2 | Buyers must manage a profile (personal info, contact details, preferences). | P1 | M |
| BR-B3 | Buyers must maintain a wishlist of saved products/suppliers and enquire directly from it. | P1 | M |
| BR-B4 | Buyers must be able to **report suspicious sellers** and submit complaints to the moderation team. | P2 | M |

### 8.5 Subscription Packages & Billing

| ID | Business Requirement | Phase | Priority |
|----|----------------------|:-----:|:--------:|
| BR-K1 | Admins must manage subscription packages, including active packages and trial packages with expiry tracking. | P1 | M |
| BR-K2 | Sellers must view, upgrade, and renew their package and view validity/expiry. | P1 | M |
| BR-K3 | Finance must generate and manage quotations and invoices. | P1 | M |
| BR-K4 | The platform must support **e-NACH** mandate creation/monitoring and payment authorisation status tracking. | P2 | M |

### 8.6 Trust, Engagement & Communication

| ID | Business Requirement | Phase | Priority |
|----|----------------------|:-----:|:--------:|
| BR-T1 | The platform must provide a notification system for sellers (package expiry, lead assignments, product approvals, announcements, offers) and buyers (system announcements). | P1 | M |
| BR-T2 | The platform must provide support ticketing for sellers (raise, track, history) and admin support management (seller settings, client checklist, tickets). | P1 | M |
| BR-T3 | Buyers must submit/edit reviews and ratings; sellers must view reviews and monitor ratings/feedback; admins must moderate reviews. | P2 | M |
| BR-T4 | Buyers must ask product questions and track seller responses; sellers must view and respond; admins must moderate Q&A. | P2 | M |
| BR-T5 | Buyers must receive **seller-response** notifications. | P2 | S |

### 8.7 Administration, Governance & Content

| ID | Business Requirement | Phase | Priority |
|----|----------------------|:-----:|:--------:|
| BR-A1 | Admins must have a dashboard of user, lead, package, revenue, and product statistics. | P1 | M |
| BR-A2 | The platform must support role-based admin user management (create users, roles, permissions, access levels). | P1 | M |
| BR-A3 | The admin dashboard must surface **Pending Tasks** for operational follow-up. | P2 | S |
| BR-A4 | Admins must manage marketplace content via CMS — **banners** and **static pages** (About Us, Contact Us, Privacy Policy, Terms & Conditions, Refund Policy, other CMS pages). | P2 | M |

### 8.8 Reporting & Analytics

| ID | Business Requirement | Phase | Priority |
|----|----------------------|:-----:|:--------:|
| BR-R1 | Sellers must view lead, product performance, and enquiry reports. | P1 | M |
| BR-R2 | Sellers must view **conversion reports** (distributed lead → sales). | P2 | S |
| BR-R3 | Admins must access **Sales Reports** (daily/weekly/monthly + revenue analysis). | P2 | M |
| BR-R4 | Admins must access **Package Reports** (package sales by period, quotation count, package conversion). | P2 | M |
| BR-R5 | Admins must access **Lead Reports** (total leads, undistributed/pending allocation, lead-delivery estimation per seller). | P2 | M |
| BR-R6 | Admins must access **Product Reports** (listing statistics, performance analysis, category-wise). | P2 | S |

### 8.9 Future Enhancements (Recommended) — Phase 2 Backlog

These are recommended enhancements captured in the FRD for Phase 2 / future delivery:

| ID | Enhancement |
|----|-------------|
| FE-1 | CRM Integration |
| FE-2 | WhatsApp API Integration |
| FE-3 | SMS Notification System |
| FE-4 | AI Lead Scoring |
| FE-5 | Seller Performance Scorecard |
| FE-6 | Commission Management |
| FE-7 | Marketing Campaign Management |
| FE-8 | Email Automation |
| FE-9 | Lead Distribution Automation |
| FE-10 | Analytics Dashboard |
| FE-11 | Mobile Application Support |
| FE-12 | Multi-language Support |
| FE-13 | API Access for Enterprise Users |

---

## 9. Key Business Process Flows

### 9.1 Lead Lifecycle (core monetisation flow)
```
Buyer submits enquiry
        ↓
Lead stored in Lead Database
        ↓
Lead Qualification (admin / rules)
        ↓
Distribution to paid sellers (by package entitlement + admin rules)   [P1]
        ↓
Seller Notification → Seller views lead                                [P1]
        ↓
Lead status tracking (distributed / un-distributed; distributed-count) [P2]
        ↓
Seller acts on lead → conversion reporting                             [P2]
```
Where package quota is exhausted, the seller may **buy lead credits** (Phase 2) or **upgrade package** (Phase 1) to continue receiving leads.

### 9.2 Seller Onboarding & Monetisation
```
Registration / Callback enquiry [P2] → Sales follow-up
→ Admin review of seller details [P1] → Account activation/deactivation [P2]
→ Package purchase (incl. trial) [P1] → Product listing & quality scoring [P1]
→ Lead distribution begins [P1] → Renewal / Upgrade [P1]
```

### 9.3 Product Publication & Quality Gating
```
Seller adds product → System computes Quality Score                    [P1]
→ Score ≥ 70%: eligible for Featured + improved visibility
→ Score < 70%: published as normal listing
→ Admin product management (active / inactive / trash / category)      [P1]
→ Featured promotion + admin featured approval                         [P2]
```

---

## 10. Business Rules (Consolidated)

| ID | Rule | Phase |
|----|------|:-----:|
| RULE-1 | When a user creates a lead, it is stored in Lead Management and distributed to **paid sellers only**. | P1 |
| RULE-2 | Sellers cannot receive leads beyond their package limit. | P1 |
| RULE-3 | Exhausted lead quota requires buying credits (P2) or upgrading the package (P1). | P1/P2 |
| RULE-4 | Product Quality Score ≥ 70% is required for Featured eligibility. | P1 |
| RULE-5 | Products in Trash are permanently deleted automatically after 7 days. | P1 |
| RULE-6 | Featured product listings may require admin approval. | P2 |
| RULE-7 | One buyer enquiry may be distributed to multiple eligible sellers. | P1 |
| RULE-8 | Reported sellers are reviewed by Admin before any action is taken. | P2 |
| RULE-9 | Support tickets remain open until resolved/closed. | P1 |
| RULE-10 | Buyer reviews and questions are moderated by Admin before public display. | P2 |
| RULE-11 | e-NACH mandates must be authorised and monitored for recurring billing. | P2 |

---

## 11. Success Metrics & KPIs

| Category | KPI | Primarily measured from |
|----------|-----|-------------------------|
| Revenue | Subscription revenue, lead-credit revenue, ARPU, renewal rate | P1 (subs) / P2 (credits, reports) |
| Liquidity | Total leads generated, % distributed, undistributed backlog | P1 / P2 reporting |
| Quality | Lead→Conversion %, avg. product quality score | P1 score / P2 conversion |
| Engagement | MAU (buyers/sellers), enquiries per buyer, reviews & Q&A volume | P1 / P2 |
| Retention | Seller churn, trial-to-paid conversion %, package upgrade rate | P1 |
| Operations | Avg. ticket resolution time, product approval SLA | P1 |
| Trust | Avg. rating, reported sellers actioned | P2 |

---

## 12. Non-Functional Expectations (Business-Level)

- **Security & Compliance:** Secure handling of PII, GST/PAN, payment and e-NACH data; role-based access; audit/login history.
- **Availability & Performance:** Search, enquiry submission, and lead distribution should be responsive and reliable at marketplace scale.
- **Scalability:** Architecture must accommodate growth in sellers, products, and lead volume — and absorb Phase 2 features without rework.
- **Usability:** Each panel must be intuitive for its audience (non-technical sellers/buyers; operational admins).
- **Auditability:** Key actions (verification, suspension, lead distribution, financial transactions) must be traceable.
- **Localisation readiness:** Language/timezone preferences supported per user (full multi-language is a Phase 2 enhancement, FE-12).

---

## 13. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Phase 1 launches without trust features (reviews/Q&A) | Lower buyer confidence early | Prioritise verification & quality score in P1; fast-follow reviews/Q&A in P2 |
| Buy-leads deferred to P2 limits early monetisation | Slower revenue ramp | P1 monetises via subscription packages; buy-leads adds upside in P2 |
| Poor lead quality erodes seller trust | Churn, refund pressure | P1 distribution rules; P2 conversion reporting & lead estimation |
| Marketplace cold-start (few buyers/sellers) | Low liquidity | Sales-led seller acquisition, trial packages, P1 notifications |
| Fraudulent sellers / fake listings | Reputational damage | P1 admin seller management; P2 block/flag, report-seller, moderation |
| Payment / e-NACH failures | Revenue leakage | P1 invoice/quotation handling; P2 e-NACH authorisation tracking |

---

## 14. Open Items / Decisions Required

| ID | Open Item | Owner | Status |
|----|-----------|-------|--------|
| OI-1 | Confirm Phase 1 vs Phase 2 split aligns with launch timeline and budget. | Sponsor / Product | Open |
| OI-2 | Confirm V1 channel scope: responsive web only (mobile app is FE-11, Phase 2). | Sponsor / Product | Open |
| OI-3 | Define exact Product Quality Score weighting model. | Product / Eng | Open |
| OI-4 | Confirm whether buyers remain free of charge in both phases. | Business | Open |
| OI-5 | Confirm whether any Phase 2 item must be pulled into Phase 1 (e.g. buy-leads, reviews). | Sponsor / Product | Open |
| OI-6 | Prioritise the Future Enhancements (FE-1…FE-13) backlog for sequencing within Phase 2. | Product | Open |

---

## 15. Glossary

| Term | Definition |
|------|------------|
| Phase 1 | Core MVP scope (un-highlighted FRD items) required to launch the marketplace. |
| Phase 2 | Enhancement scope (highlighted FRD items) plus recommended future enhancements. |
| Lead | A buyer enquiry captured and distributed to sellers. |
| Buy Lead | Purchasing additional leads beyond package allocation (Phase 2). |
| Package | A seller subscription plan defining entitlements (leads, products, features). |
| Product Quality Score | A computed score (0–100%) gating Featured eligibility and visibility. |
| Featured Listing | Promoted product placement with higher visibility (score/package gated; Phase 2). |
| Dealing Area | Seller-defined geography (Country/State/City) for lead/search relevance. |
| e-NACH | Electronic mandate for recurring payment authorisation, India (Phase 2). |
| CMS | Content Management System for banners and static pages (Phase 2). |
| RBAC | Role-Based Access Control. |

---

## 16. Traceability (BRD → FRD Modules, with Phase)

| Business Capability | FRD Modules | Phase 1 | Phase 2 |
|---------------------|-------------|:-------:|:-------:|
| Lead engine | 1.1 Leads, 3.6 Lead Mgmt | View/distribute/assign | Buy Leads, status tracking, call enquiries |
| Seller profile & verification | 1.2 My Account, 3.3 Seller Mgmt, 3.4 Data Bank | Profile, view/edit, data bank | Activate/block, callbacks, product deletion, dealing-area mgmt |
| Product catalogue & quality | 1.3 Product Mgmt, 3.7 Product Mgmt | Add/list/trash/dealing area, categories | Featured products + admin featured mgmt |
| Buyer discovery & enquiry | 2.1–2.3 Buyer modules, 2.6 Report Seller | Dashboard, profile, wishlist | Report seller |
| Packages & billing | 1.7 / 3.5 Package Mgmt, 3.9 Billing | Packages, quotations, invoices | e-NACH |
| Trust & engagement | 1.6/2.4 Reviews, 1.9/2.5 Q&A, 1.8/2.7 Notifications, 1.10/3.11 Support, 3.8 Moderation | Notifications, support tickets | Reviews, Q&A, moderation, seller-response notifications |
| Admin governance & content | 3.1 Dashboard, 3.2 User Mgmt, 3.11 CMS | Dashboard, user mgmt, support | Pending tasks, banner & page mgmt |
| Reporting & analytics | 1.4 Seller Reports, 3.10 Admin Reports | Seller lead/performance/enquiry reports | Conversion + all admin sales/package/lead/product reports |
| Future enhancements | Future Enhancements list | — | FE-1…FE-13 |

---

*End of Document — BRD v3.0 (Draft for Review)*
