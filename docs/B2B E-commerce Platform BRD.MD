# B2B E-commerce Platform
## Business Requirements & Design Specification


---

## Document Control

| Field | Value |
|---|---|
| Document Title | B2B E-commerce Platform: Business Requirements & Design Specification |
| Prepared By | Omar Ahmed Al-Buriki, Business Systems Analyst |
| Company | Mohd. Saeed Balbid Company |
| Project Duration | Feb 2023 to May 2025 (formal start; early scoping preceded this period) |
| Development Model | Vendor-led (external service provider); business-side requirements owned internally |
| Related Prior Engagement | Multi-Branch ERP Implementation (this platform depends on that ERP as its single source of truth) |
| Document Status | Representative sample, reconstructed from project dossier |

---

## Table of Contents

1. Introduction and Purpose
2. Business Context and Problem Statement
3. Business Objectives
4. Scope
5. Stakeholders and RACI
6. Platform Requirements
7. Order Lifecycle and Pricing Model Design
8. Regional Business Rule Design (Delivery Logic)
9. Business Rules Specification
10. Integration Requirements
11. Credit and Approval Control Matrix
12. Assumptions and Constraints
13. Requirements Traceability and UAT Validation
14. Document Governance

---

## 1. Introduction and Purpose

This document defines the business and functional requirements for a B2B e-commerce platform extending an established multi-branch FMCG operation into a digital ordering channel. It translates stakeholder requirements gathered through workshops and business discussions into specifications a vendor could build against, and defines the acceptance basis for UAT sign-off and the Go/No-Go launch decision.

---

## 2. Business Context and Problem Statement

Wholesale ordering had previously been physical-only. Customers visited a branch in person, submitted a paper draft, waited on manual picking, and received a manually adjusted invoice, with no self-service option and no ability to order outside business hours.

Following the ERP implementation that gave the business reliable real-time inventory and pricing data, the organization identified an opportunity to extend that operational foundation into a digital B2B ordering channel. The core design constraint was to serve verified B2B customers with 24/7 self-service ordering, without introducing the sync risk of a duplicate, platform-side data source separate from the ERP.

---

## 3. Business Objectives

| ID | Objective |
|---|---|
| OBJ-01 | Enable 24/7 digital ordering for verified B2B customers |
| OBJ-02 | Reduce manual wholesale order handling by shifting volume to the digital channel |
| OBJ-03 | Enforce tiered pricing digitally, correctly applied per verified customer segment |
| OBJ-04 | Prevent orders below regional minimum thresholds |
| OBJ-05 | Maintain real-time inventory accuracy between the platform and the ERP, eliminating online oversell |
| OBJ-06 | Restrict ordering to verified B2B accounts only |
| OBJ-07 | Enforce credit terms structurally: net terms for lower-risk tiers, prepayment for higher-risk tiers |

---

## 4. Scope

### 4.1 In Scope

- 8,000+ active SKUs, sourced live from the ERP.
- 5-tier customer segmentation and pricing engine.
- Real-time ERP inventory and pricing integration. The ERP remained the single source of truth; no platform-side stock table.
- HyperPay payment gateway integration (MADA, Visa, Mastercard, Apple Pay).
- Regional delivery rules with minimum order and delivery fee logic.
- Logistics API integration for fulfillment tracking.
- Customer registration and verification workflow (CR and VAT upload, business type classification, tier assignment).
- B2C-ready architecture, built but left inactive for configuration-only future activation.

### 4.2 Out of Scope

- B2C activation.
- Native mobile app.
- Platform-side inventory management.
- AI-driven pricing.

---

## 5. Stakeholders and RACI

| Stakeholder | Role | Requirements Involvement |
|---|---|---|
| Business Systems Analyst (Omar) | Requirements owner | Gathered requirements through workshops; designed order lifecycle, pricing model, and delivery rules; reviewed vendor implementation against specification |
| Finance Manager | Requirement source, approval authority | Credit terms design, credit limit assignment at account activation |
| Commercial / Sales Manager | Requirement source | Customer segmentation, tier criteria |
| Operations Manager | Requirement source | Delivery logic, fulfillment coordination |
| IT Administrator | Technical liaison | ERP integration coordination |
| External Service Provider (Vendor) | Technical delivery | Platform build against approved specification |
| HyperPay | Integration partner | Payment gateway for prepaid-tier checkout |
| Logistics Provider | Integration partner | Fulfillment tracking |
| B2B Customers | End users | Platform ordering and account management |

---

## 6. Platform Requirements

| ID | Requirement | Priority |
|---|---|---|
| BR-01 | The platform shall restrict ordering access to verified B2B accounts only; no self-registration into an active, ordering-capable account | Must Have |
| BR-02 | The platform shall provide a customer registration and verification workflow, including CR and VAT document upload and business type classification | Must Have |
| BR-03 | The platform shall enable 24/7 self-service ordering for verified accounts | Must Have |
| BR-04 | The platform shall query stock and pricing live from the ERP at all times, without maintaining a duplicate platform-side record | Must Have |
| BR-05 | The platform shall include a B2C-ready architecture, configured but inactive, for future activation as a separate business decision | Should Have |

### 6.1 Customer Registration and Verification Flow

1. Customer registers on the platform.
2. Customer uploads CR and VAT documents.
3. Customer selects business type.
4. Application is submitted for admin review.
5. If not verified, the application is rejected with a reason provided to the applicant.
6. If verified, the customer is assigned to a pricing tier (1 of 5) based on business type classification.
7. Finance sets the credit limit for the account.
8. For Tier 1 or Tier 2 accounts, a promissory note is required before activation.
9. For Tier 3, 4, and 5 accounts, the account is activated directly.

---

## 7. Order Lifecycle and Pricing Model Design

### 7.1 Pricing Model

A 5-tier pricing structure was designed, mapped to customer risk and payment terms rather than a flat wholesale discount:

| Tier | Segment | Payment Terms | Activation Requirement |
|---|---|---|---|
| Tier 1 | Hotels / Large F&B Chains | Net 30 days | Promissory note required |
| Tier 2 | Restaurants / Cafes | Net 15 days | Promissory note required |
| Tier 3 | Charities / NGOs | Prepaid | None |
| Tier 4 | Resellers / Delivery Apps | Prepaid | None |
| Tier 5 | Standard / Other B2B | Prepaid | None |

### 7.2 Functional Requirements: Pricing and Credit

| ID | Functional Requirement | Related Requirement |
|---|---|---|
| FR-01 | On tier assignment, the platform shall apply the pricing and payment terms corresponding to the assigned tier | BR-01, Section 7.1 |
| FR-02 | For Tier 1 and Tier 2 accounts, the platform shall require a signed promissory note before account activation | Section 7.1 |
| FR-03 | For Tier 3, 4, and 5 accounts, the platform shall activate the account directly upon tier assignment | Section 7.1 |
| FR-04 | On checkout for a net-term account (Tier 1 or 2), the platform shall verify available credit and block the order if the customer's outstanding balance exceeds their approved credit limit | Section 9, BRule-02 |
| FR-05 | On checkout for a prepaid-tier account (Tier 3, 4, or 5), the platform shall route payment through HyperPay, supporting MADA, Visa, Mastercard, and Apple Pay | BR-04, Section 10 |

### 7.3 Order Lifecycle

The platform served verified B2B customers exclusively, replacing physical-only wholesale ordering with 24/7 self-service. Once an account is active, the customer places orders directly against live ERP stock and pricing data. Net-term accounts are subject to the credit check in FR-04 at checkout; prepaid accounts are routed to HyperPay for payment. This design credit-checks net-term accounts using the same principle already enforced in the ERP implementation, applying the earlier project's credit control logic to a new channel rather than reinventing it.

---

## 8. Regional Business Rule Design (Delivery Logic)

Delivery logic was defined by region rather than a flat nationwide rate. Minimum order value, delivery fee, and free-delivery threshold each varied by region, with fees calculated automatically at checkout rather than entered manually.

### 8.1 Functional Requirements: Delivery

| ID | Functional Requirement | Related Requirement |
|---|---|---|
| FR-06 | On checkout, the platform shall determine the customer's delivery region and apply the corresponding minimum order value | OBJ-04 |
| FR-07 | On checkout, the platform shall calculate the delivery fee automatically based on the customer's delivery region | OBJ-04 |
| FR-08 | On checkout, the platform shall apply the region-specific free-delivery threshold, waiving the delivery fee where the order value qualifies | OBJ-04 |
| FR-09 | The platform shall integrate with a logistics API to support fulfillment tracking after order confirmation | Section 4.1 |

---

## 9. Business Rules Specification

### BRule-01: Tier-Based Account Activation
- **Trigger:** Customer verification approved and tier assigned.
- **System Action:** Net-term tiers (1 and 2) require a signed promissory note before activation. Prepaid tiers (3, 4, and 5) activate directly.
- **Override:** None documented.

### BRule-02: Net-Term Credit Check at Checkout
- **Trigger:** Order checkout for a Tier 1 or Tier 2 account.
- **System Action:** Order is blocked if the customer's outstanding balance exceeds their approved credit limit.
- **Design Note:** Mirrors the same credit control principle already enforced in the Multi-Branch ERP Implementation, applying consistent business logic across both projects.

### BRule-03: Region-Based Delivery Rule
- **Trigger:** Order checkout.
- **System Action:** Minimum order value, delivery fee, and free-delivery threshold are applied automatically based on the customer's assigned delivery region.
- **Override:** None documented.

### BRule-04: ERP as Single Source of Truth
- **Trigger:** Any stock or price query on the platform.
- **System Action:** Platform queries the ERP live; no platform-side duplicate stock or price table is maintained.
- **Design Rationale:** Deliberately avoids the sync-drift risk that a duplicate data source would introduce.

---

## 10. Integration Requirements

| Integration | Purpose | Detail |
|---|---|---|
| ERP | Real-time stock and pricing feed | ERP remains the single source of truth for both; no platform-side stock table |
| HyperPay | Payment gateway | Supports MADA, Visa, Mastercard, and Apple Pay for prepaid-tier checkout |
| SMS | Customer communication | Coordinated as part of the platform's communication capability |
| Logistics API | Fulfillment tracking | Order status tracking after confirmation |

Each integration was coordinated with the vendor and validated as part of the testing phase described in Section 13.

---

## 11. Credit and Approval Control Matrix

| Check Point | Trigger | Outcome |
|---|---|---|
| Verification review | Registration submitted | Approved (tier assigned) or rejected with reason |
| Promissory note | Tier 1 or Tier 2 assignment | Required before account activation |
| Credit check | Checkout, Tier 1 or Tier 2 account | Order blocked if credit limit exceeded |
| Payment routing | Checkout, Tier 3, 4, or 5 account | Routed through HyperPay |

---

## 12. Assumptions and Constraints

- The platform depends on the Multi-Branch ERP Implementation being live and stable; it was built after that ERP was operational, not in parallel with it.
- No platform-side inventory record is maintained, by design, to avoid sync drift with the ERP.
- The external service provider is responsible for technical build against these requirements; this document defines business intent, not system architecture.
- B2C activation, native mobile app development, platform-side inventory management, and AI-driven pricing are explicitly excluded from this project's scope.

---

## 13. Requirements Traceability and UAT Validation

| Requirement | Validation Approach | Outcome |
|---|---|---|
| BR-04 (ERP as source of truth) | UAT confirmed live stock and pricing reflected immediately from the ERP with no platform-side lag or discrepancy | Confirmed |
| BRule-01 (Tier-based activation) | UAT confirmed promissory note gating for Tier 1 and Tier 2, direct activation for Tier 3 through 5 | Confirmed |
| BRule-02 (Credit check at checkout) | UAT confirmed order blocking behavior for net-term accounts exceeding credit limit | Confirmed |
| BRule-03 (Regional delivery rules) | UAT confirmed automatic fee calculation and minimum order enforcement varied correctly by region | Confirmed |
| HyperPay integration | UAT confirmed prepaid-tier checkout completed successfully across supported payment methods | Confirmed |

I coordinated UAT with business users and also performed hands-on testing myself to validate business processes and system functionality, rather than solely relying on the vendor's own QA. I participated directly in the Go/No-Go decision ahead of launch, and acted as the primary liaison between business stakeholders, the external service provider, and the technical team to ensure business requirements were correctly implemented and that issues were resolved before production deployment.

**Note on KPI targets:** A baseline-to-target framework exists for post-launch metrics (order volume growth, payment success rate, inventory sync accuracy, cart abandonment rate). These are documented as proposed targets, not confirmed achieved outcomes, and are excluded from this specification's validation results accordingly.

---

## 14. Document Governance

Requirements in this document were gathered through stakeholder workshops and business discussions covering the commercial, finance, and operational functions the platform needed to serve, then translated into specifications the vendor could build against. Implementation was reviewed against these specifications throughout the build, with gaps escalated as needed. Backlog and issue tracking with the vendor was managed through Jira. Final acceptance was confirmed through the UAT and Go/No-Go process described in Section 13.
