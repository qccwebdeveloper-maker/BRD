# eB2BMart Portal — Business Requirements Document (BRD)

**Version:** 1.1
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
| 1.1 | 26-Jun-2026 | BA Team | Removed objective BO-1 (enquiry monetisation) and BO-3 (match quality) from the Business Objectives table; removed the Out of Scope subsection (former 5.2) and renumbered the Scope subsections accordingly. |

### 1.2 Approvals

| Name | Role | Decision | Date |
|------|------|----------|------|
| _TBD_ | Business Owner / Sponsor | Pending | |
| _TBD_ | Product Manager | Pending | |
| _TBD_ | Technical Lead | Pending | |

### 1.3 Document Purpose

This BRD describes **what the business needs** from the eB2BMart platform and **why**, expressed in business terms for stakeholders, sponsors, and the delivery team. It precedes and complements the FRD (which defines *how* the system behaves at the functional level). Where the FRD lists individual screen behaviours, this BRD groups them into business capabilities, objectives, and measurable outcomes.

---

## 2. Executive Summary

eB2BMart is a **B2B online marketplace** (similar in model to IndiaMART / Aajjo) that connects **buyers** seeking products and services with **verified sellers/suppliers**. The platform's core commercial engine is **lead generation and distribution**: buyer enquiries are captured, qualified, and distributed to sellers based on paid **subscription packages**, with additional **pay-per-lead** purchasing.

Revenue is driven primarily by **seller subscription packages**, **buy-lead credits**, and **featured/premium listings**. The platform comprises three integrated portals:

1. **Seller Panel** — suppliers manage profiles, products, leads, subscriptions, and support.
2. **Buyer Panel** — buyers search, enquire, shortlist suppliers, and track responses.
3. **Admin Panel** — operators manage users, leads, packages, payments, content, and platform configuration.

This document establishes the business case, scope, and requirements for **Version 1** of the platform.

---

## 3. Business Context & Problem Statement

### 3.1 Market Opportunity
B2B procurement in India and similar markets is fragmented, relationship-driven, and largely offline. Buyers struggle to discover credible suppliers quickly; suppliers struggle to generate qualified, intent-rich leads cost-effectively.

### 3.2 Problem Statement
- **Buyers** lack a single, trusted place to discover verified suppliers, compare them, and raise enquiries with quantity/specification context.
- **Sellers** lack a predictable, measurable channel for qualified buyer enquiries and the tooling to manage and convert them.
- **Marketplace operators** lack a centralised system to monetise enquiries, verify supplier credibility, moderate content, and govern the marketplace at scale.

### 3.3 Proposed Solution
A multi-sided B2B marketplace where buyer enquiries become **monetisable leads** distributed to sellers by **package entitlement and admin-defined rules**, supported by supplier verification, product quality scoring, reviews/Q&A, and full administrative governance.

---

## 4. Business Objectives & Goals

| # | Objective | Rationale | Indicative Success Measure |
|---|-----------|-----------|----------------------------|
| BO-1 | Maximise qualified lead volume and distribution efficiency | Marketplace liquidity | Leads generated, % distributed, distribution latency |
| BO-2 | Build trust via verification, reviews, and quality scoring | Credibility differentiates from unverified channels | % verified sellers, avg. product quality score |
| BO-3 | Enable scalable operations through the Admin Panel | Control cost-to-serve as GMV grows | Tickets resolved, approvals SLA |
| BO-4 | Grow and retain both buyer and seller bases | Network effects | MAU, seller renewals, churn |

---

## 5. Project Scope

### 5.1 In Scope (Version 1)

**Seller Panel**
- Leads Management (view, buy, status tracking, package-based allocation)
- My Account (company, address, contact, GST/PAN, business profile, settings, credentials)
- Product Management (add/edit, listing, trash, dealing areas, featured products, quality score)
- Reports (leads, product performance, enquiries, conversion)
- Contact Us, Reviews, Package Management, Notifications, Questions & Answers, Support Tickets

**Buyer Panel**
- Dashboard, My Profile
- Enquiry Management (create, track, status)
- Wishlist, Favourite Sellers & Products
- Reviews, Questions & Answers
- Report Seller, Notifications, Contact Support

**Admin Panel**
- Dashboard & operational statistics
- User Management (role-based access)
- Seller Management (verification, approval, suspension, dealing areas)
- Package Management (master, active, trial)
- Lead Management (distribution rules, undistributed/call enquiries)
- Product Management (approval, categories, types, brands, attributes)
- Review & Q&A moderation
- Billing & Finance (quotations, invoices, payments, e-NACH)
- Reports & Analytics
- Support Management, CMS, Notification Management, System Settings

### 5.2 Assumptions
- The platform operates primarily in an **India-first** context (GST, PAN, CIN, e-NACH,code, INR).
- Sellers transact via **prepaid subscription packages** and **lead credits**; buyers use the platform free of charge.
- Geography is modelled as **Country → State → City** 
- A buyer enquiry may be distributed to **multiple** eligible sellers.

### 5.3 Constraints
- Regulatory: GST/PAN handling, invoicing, e-NACH mandate processing must comply with Indian norms.
- Lead distribution is bounded by **package entitlements** and **admin-configured rules**.
- Trash retention auto-purge fixed at **7 days** for deleted products.
- Featured eligibility requires a **Product Quality Score ≥ 70%** and an enabling package.

---

## 6. Stakeholders

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

## 7. User Roles & Personas

### 7.1 Primary Marketplace Actors
- **Seller** — registered supplier managing products and converting distributed leads into sales.
- **Buyer** — registered procurement user raising enquiries and shortlisting suppliers.

### 7.2 Administrative Roles (role-based access control)
Super Admin, Sales Executive, Customer Support, Lead Manager, Finance Executive, Product Moderator, Content Manager, Marketing Executive.

Each admin role shall have configurable permissions; the **Super Admin** holds full control.

---

## 8. High-Level Business Requirements

Requirements are grouped by business capability. Each maps to detailed functional behaviour in the FRD. Priority uses MoSCoW (**M**ust / **S**hould / **C**ould).

### 8.1 Lead Generation, Monetisation & Distribution — *the core engine*

| ID | Business Requirement | Priority |
|----|----------------------|----------|
| BR-L1 | The platform must capture buyer enquiries as leads and store them centrally. | M |
| BR-L2 | The platform must distribute leads to eligible sellers automatically, based on subscription package entitlements and admin-defined rules (category eligibility, geographic coverage, priority, daily/monthly limits). | M |
| BR-L3 | Higher-tier packages must receive higher lead priority and greater allocation. | M |
| BR-L4 | Sellers must be able to purchase additional leads (buy-lead credits) when package quota is exhausted. | M |
| BR-L5 | Every lead must carry a lifecycle status (Undistributed, Distributed, Viewed, Contacted, Follow-up Pending, Converted, Closed, Expired). | M |
| BR-L6 | Admins must be able to view, edit, merge, manually assign, schedule, and mark invalid leads, and convert phone enquiries into leads. | M |
| BR-L7 | The platform should support package upgrade as an alternative to lead purchase when quota is exhausted. | S |

### 8.2 Seller Onboarding, Profile & Verification

| ID | Business Requirement | Priority |
|----|----------------------|----------|
| BR-S1 | Sellers must maintain a complete business profile (company details, multiple address types, contacts, GST/PAN/CIN, business profile, keywords). | M |
| BR-S2 | The platform must support admin verification of seller credentials (GST, PAN, documents, address, contact). | M |
| BR-S3 | Admins must be able to approve, activate, suspend, block, flag, and delete seller accounts with remarks and history. | M |
| BR-S4 | Sellers should be able to secure their accounts (password change, optional 2FA, login history, remote logout). | S |
| BR-S5 | Verified status should increase seller credibility and marketplace visibility. | S |

### 8.3 Product Catalogue & Quality

| ID | Business Requirement | Priority |
|----|----------------------|----------|
| BR-P1 | Sellers must be able to create, edit, duplicate, delete, and restore product listings with full attributes (pricing, MOQ, GST%, HSN, specs, media, documents). | M |
| BR-P2 | The platform must compute a **Product Quality Score** on submission and gate Featured eligibility at **≥ 70%**, with improvement recommendations. | M |
| BR-P3 | Deleted products must move to Trash and auto-purge after **7 days**. | M |
| BR-P4 | Sellers must be able to define **dealing areas** (Country/State/City) influencing search relevance and lead targeting. | M |
| BR-P5 | Admins must approve/reject/flag/ban products and manage the category hierarchy, product types, brands, and attributes. | M |
| BR-P6 | Eligible sellers should be able to promote products to **Featured Listings** (subject to score, package, and optional admin approval). | S |

### 8.4 Buyer Discovery & Enquiry

| ID | Business Requirement | Priority |
|----|----------------------|----------|
| BR-B1 | Buyers must be able to search products, suppliers, and categories, and submit enquiries with requirement, quantity, documents, and preferred supplier location. | M |
| BR-B2 | Buyers must be able to track enquiry status and re-submit or close enquiries. | M |
| BR-B3 | Buyers must be able to maintain wishlists and favourite sellers/products, and enquire directly from them. | M |
| BR-B4 | Buyers must be able to report fraudulent/misleading sellers with supporting evidence. | M |
| BR-B5 | Buyers should manage a profile with company, contact, and address (billing/shipping) information. | S |

### 8.5 Subscription Packages & Billing

| ID | Business Requirement | Priority |
|----|----------------------|----------|
| BR-K1 | Admins must define subscription packages (price, validity, lead allocation, buy-lead credits, product/featured limits, advertisement credits, premium services, verification, catalogue limit). | M |
| BR-K2 | Sellers must view, compare, upgrade, and renew packages and view entitlements/credits/expiry. | M |
| BR-K3 | The platform must support trial packages with expiry monitoring and trial-to-paid conversion. | M |
| BR-K4 | Finance must generate quotations and invoices (generate/download/email/cancel) and verify payments. | M |
| BR-K5 | The platform must support **e-NACH** mandate creation, authorisation monitoring, and payment status tracking for recurring billing. | M |
| BR-K6 | Sellers must access invoices and payment history with downloadable documents. | M |

### 8.6 Trust, Engagement & Communication

| ID | Business Requirement | Priority |
|----|----------------------|----------|
| BR-T1 | Buyers must be able to submit product/seller reviews and ratings; sellers can view and reply; admins moderate. | M |
| BR-T2 | Buyers must be able to ask product questions; sellers answer; admins moderate before public display. | M |
| BR-T3 | The platform must provide a multi-channel notification system (in-app, email, SMS, push) with preferences, templates, scheduling, and broadcasts. | M |
| BR-T4 | The platform must provide support ticketing for buyers and sellers with categories, priority, assignment, status lifecycle, and history. | M |

### 8.7 Administration, Governance & Content

| ID | Business Requirement | Priority |
|----|----------------------|----------|
| BR-A1 | Admins must have a real-time dashboard of user, lead, package, revenue, and operational statistics. | M |
| BR-A2 | The platform must support role-based admin user management (create/edit/delete, roles, permissions, activation, password reset, login history). | M |
| BR-A3 | Admins must manage marketplace content via CMS (banners with scheduling; static/legal/blog/FAQ/career/custom pages). | M |
| BR-A4 | Admins must configure platform-wide and marketplace settings (lead distribution rules, default package, branding, contacts, social links). | M |
| BR-A5 | The platform must provide operational and business reporting (sales, package, lead, product, user analytics with daily/weekly/monthly/yearly breakdowns). | M |
| BR-A6 | Admins must handle callback/registration enquiries and assign sales executives with follow-up tracking. | S |

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
Automatic Distribution (by package entitlement + admin rules)
        ↓
Seller Notification
        ↓
Seller acts on lead → status progresses
(Distributed → Viewed → Contacted → Follow-up → Converted / Closed / Expired)
```
Where package quota is exhausted, the seller may **buy lead credits** or **upgrade package** to continue receiving leads.

### 9.2 Seller Onboarding & Monetisation
```
Registration / Callback enquiry → Sales follow-up → Document submission
→ Admin verification (GST/PAN/docs) → Account approval/activation
→ Package purchase (incl. trial) → Product listing & quality scoring
→ Lead distribution begins → Renewal / Upgrade
```

### 9.3 Product Publication & Quality Gating
```
Seller adds product → System computes Quality Score
→ Score ≥ 70%: eligible for Featured + improved search visibility
→ Score < 70%: published as normal listing, ineligible for Featured
→ Admin moderation (approve / reject / flag / ban)
```

---

## 10. Business Rules (Consolidated)

| ID | Rule |
|----|------|
| RULE-1 | Sellers cannot receive leads beyond their package limit (daily/monthly). |
| RULE-2 | Higher-tier packages receive higher lead priority and allocation. |
| RULE-3 | Exhausted lead quota requires buying credits or upgrading the package. |
| RULE-4 | Product Quality Score ≥ 70% is required for Featured eligibility. |
| RULE-5 | Products in Trash are permanently deleted automatically after 7 days. |
| RULE-6 | Featured status may require admin approval depending on platform policy. |
| RULE-7 | Only registered buyers can submit enquiries; one enquiry may reach multiple eligible sellers. |
| RULE-8 | Buyers may submit unlimited enquiries unless platform policy restricts. |
| RULE-9 | Reported sellers are reviewed by Admin before any action is taken. |
| RULE-10 | Support tickets remain open until resolved/closed by buyer or support executive. |
| RULE-11 | GST/profile changes may require admin re-verification. |
| RULE-12 | Buyer questions and reviews may require admin moderation before public display. |

---

## 11. Success Metrics & KPIs

| Category | KPI |
|----------|-----|
| Revenue | Subscription revenue, lead-credit revenue, ARPU, renewal rate |
| Liquidity | Total leads generated, % distributed, undistributed backlog, distribution latency |
| Quality | Lead→Contact→Conversion %, avg. product quality score, % verified sellers |
| Engagement | MAU (buyers/sellers), enquiries per buyer, products per seller |
| Retention | Seller churn, trial-to-paid conversion %, package upgrade rate |
| Operations | Avg. ticket resolution time, product approval SLA, seller verification SLA |
| Trust | Avg. rating, number of reported sellers actioned |

---

## 12. Non-Functional Expectations (Business-Level)

These are business-level expectations; detailed NFRs belong in the technical specification.

- **Security & Compliance:** Secure handling of PII, GST/PAN, payment and e-NACH data; role-based access; audit/login history.
- **Availability & Performance:** Search, enquiry submission, and lead distribution should be responsive and reliable at marketplace scale.
- **Scalability:** Architecture must accommodate growth in sellers, products, and lead volume.
- **Usability:** Each panel must be intuitive for its audience (non-technical sellers/buyers; operational admins).
- **Auditability:** Key actions (verification, suspension, lead distribution, financial transactions) must be traceable.
- **Localisation readiness:** Language/timezone preferences supported per user.

---

## 13. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Poor lead quality erodes seller trust | Churn, refund pressure | Lead qualification, invalid-lead marking, conversion reporting |
| Marketplace cold-start (few buyers/sellers) | Low liquidity | Sales-led seller acquisition, trial packages, marketing notifications |
| Fraudulent sellers / fake listings | Reputational damage | Verification, reporting, moderation, flag/ban controls |
| Payment / e-NACH failures | Revenue leakage | Payment verification, status tracking, finance oversight |
| Over-distribution of leads | Seller dissatisfaction | Package limits and admin distribution rules (RULE-1, RULE-2) |
| Regulatory non-compliance (GST/e-NACH) | Legal exposure | Compliance-aligned invoicing and mandate handling |

---

## 14. Open Items / Decisions Required

| ID | Open Item | Owner | Status |
|----|-----------|-------|--------|
| OI-1 | Confirm V1 channel scope: responsive web only, or native mobile apps included? | Sponsor / Product | Open |
| OI-2 | Confirm whether transactional ordering/checkout is deferred (lead-led V1 assumed). | Product | Open |
| OI-3 | Define exact Product Quality Score weighting model. | Product / Eng | Open |
| OI-4 | Confirm whether buyers are always free or if any buyer monetisation applies. | Business | Open |
| OI-5 | Confirm geographic / multi-language launch markets beyond India-first. | Business | Open |
| OI-6 | FRD numbering gaps noted (Buyer 2.5/2.11; Admin 3.4); confirm no missing modules. | BA | Open |

---

## 15. Glossary

| Term | Definition |
|------|------------|
| Lead | A buyer enquiry captured and distributed to sellers. |
| Buy Lead | Purchasing additional leads beyond package allocation using credits. |
| Package | A seller subscription plan defining entitlements (leads, products, features). |
| Product Quality Score | A computed score (0–100%) gating Featured eligibility and visibility. |
| Featured Listing | Promoted product placement with higher visibility (score/package gated). |
| Dealing Area | Seller-defined geography (Country/State/City) for lead/search relevance. |
| e-NACH | Electronic mandate for recurring payment authorisation (India). |
| MOQ | Minimum Order Quantity. |
| HSN | Harmonized System of Nomenclature (tax classification code). |
| RBAC | Role-Based Access Control. |

---

## 16. Traceability (BRD → FRD Modules)

| Business Capability | FRD Modules |
|---------------------|-------------|
| Lead engine | 1.1 Leads Management, 3.6 Lead Management |
| Seller profile & verification | 1.2 My Account, 3.3 Seller Management |
| Product catalogue & quality | 1.3 Product Management, 3.7 Product Management |
| Buyer discovery & enquiry | 2.1–2.4, 2.8 Buyer modules, 2.9 Report Seller |
| Packages & billing | 1.7 Package Mgmt, 3.5 Package Mgmt, 3.9 Billing & Finance |
| Trust & engagement | 1.6/2.6 Reviews, 1.9/2.7 Q&A, 1.8/2.10/3.13 Notifications, 1.10/2.12/3.11 Support |
| Admin governance & content | 3.1 Dashboard, 3.2 User Mgmt, 3.8 Review/Q&A Mgmt, 3.10 Reports, 3.12 CMS, 3.14 System Settings |

---

*End of Document — BRD v1.1 (Draft for Review)*
