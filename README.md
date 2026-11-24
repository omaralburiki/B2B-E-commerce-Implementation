# B2B E-commerce Implementation

A B2B e-commerce platform built by an external vendor on top of a live ERP. I led the project on the company side and owned the business design: who can order, how customers are priced and given credit, how delivery is charged, and how the platform works with the ERP.

**FMCG retailer and wholesaler, Saudi Arabia · Feb 2023 – May 2025 · Role: E-commerce Solutions Lead**

## Background

Wholesale customers could only order in person. They visited a branch, handed over a paper draft, waited for manual picking, and received an invoice adjusted by hand. Once the ERP was live and its stock and prices could be trusted, the company decided to open a digital channel for verified business customers, available at any hour.

I studied and scoped the channel before the project formally started, so I was part of the decision to build it. From early 2023 the platform was my main responsibility, and ERP work became secondary support.

## What I designed

**Customer access.** Only verified businesses can order. A customer registers, uploads CR and VAT documents, selects a business type, and waits for review. An approved account is assigned a pricing tier, and Finance sets its credit limit.

**Pricing and credit.** The tiers follow payment risk instead of a single wholesale discount.

| Tier | Segment | Terms |
|---|---|---|
| 1 | Hotels and large F&B chains | Longer net terms, signed guarantee required |
| 2 | Restaurants and cafes | Shorter net terms, signed guarantee required |
| 3 | Charities and NGOs | Prepaid |
| 4 | Resellers and delivery apps | Prepaid |
| 5 | Other B2B customers | Prepaid |

A net-term order that goes over the customer's credit limit is blocked at checkout, the same control the ERP already applied in the branches.

**Delivery.** Minimum order value, delivery fee, and free-delivery threshold differ across 4 regions and are calculated at checkout.

**Integrations.** The platform reads stock and prices live from the ERP and keeps no copy of its own, so the two cannot drift apart. I also defined the requirements for the payment gateway (MADA, Visa, Mastercard, Apple Pay), SMS notifications, and the logistics provider's tracking API.

## Leading the project

I led the B2B e-commerce project on the company side, managing delivery from business requirements and release planning through vendor coordination, testing, and launch readiness. I set release scope and priorities, approved the vendor's timelines, reviewed change requests and milestone payments, and reported status to management.

The vendor built the platform against my specifications. In Jira I ordered the backlog by how important each requirement was for the next release, logged defects with the vendor, and followed them up. Before launch I coordinated UAT with business users, tested the platform myself, and took part in the Go/No-Go decision.

Product content such as images, descriptions, and translations was not stored in the ERP, so I led the content team that maintained it on the platform. I set the product categorization and the standards for images and descriptions, decided which products to add, and reviewed the quality of the work. I also joined new business accounts during onboarding alongside the commercial team, with product walkthroughs and commercial discussions.

## Outcome

The platform launched for verified B2B accounts only and supported more than 500 B2B customers across 8,000+ active SKUs. Wholesale ordering moved from branch visits to self-service. A set of post-launch KPI targets was drafted, but those were targets, not measured results, so they are not reported here.

## Related project

The platform depends on the ERP delivered in the [Multi-Branch ERP Implementation](https://github.com/omaralburiki/Multi-Branch-ERP-Implementation) as its source of truth for stock and prices.

## Documents

- [Full case study](docs/Case-Study.md): how the project was led, the pricing, credit, and delivery design, the integration architecture, the release cycle, and testing.
- [Business Requirements & Design Specification](docs/BRD.md): platform requirements, business rules, and UAT validation. This is a representative version; the original belongs to the company.
