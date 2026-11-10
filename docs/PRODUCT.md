# Seller Commerce Platform

## Consolidated product context — 10 November 2026

The user-supplied [Seller Hub vision](PRODUCT-VISION.md) expands the original discussion. Read it alongside this implementation-oriented summary. Its examples are illustrative; prices, market assumptions and success metrics remain hypotheses, not validated results or implemented behavior.

### Positioning and early adopters

Seller Hub complements marketplaces rather than replacing them. Marketplace = customer acquisition; Seller Hub = customer relationships, direct commerce and retention. Acquisition can also come from social media, offline stores, advertising and referrals. The objective is selected repeat purchases through seller-owned channels, not mass migration away from marketplaces.

Initial target: sellers with existing customers, repeat purchases and existing WhatsApp conversations, especially those still managing WhatsApp orders manually. Potential categories include coffee, food, skincare, pet supplies, baby products, household consumables and fashion basics. Prioritizing low-to-medium transaction values and repeat-purchase categories is a business hypothesis to validate.

### Product value and AI

Reduce manual product, stock, order and follow-up work while helping sellers understand and retain customers. AI translates natural language into authorized Commerce operations; the commerce and customer relationship platform must retain value without AI. Planned capabilities include recommendations, repeat orders, FAQ and human handover, with tracking dependent on later shipping capabilities.

The WhatsApp experience should not require customers to install an app or create a separate account first. This experience goal does not settle backend identity, customer binding, consent or resource authorization rules.

Customer relationship context may eventually include WhatsApp contact, purchase and conversation history, favorite products, preferences, purchase frequency, last purchase and total spending. These are candidate capabilities, not permission to collect all data by default. Retention, privacy and seller-scoped identity rules remain open. The long-term vision is one customer relationship history across channels within the seller context; cross-seller identity sharing is not decided.

### Conceptual product roadmap

1. Commerce Foundation: Seller, Product, Customer, Cart and Order; enable basic transactions.
2. WhatsApp Commerce: connect customers to Commerce through WhatsApp.
3. AI Commerce: natural-language product discovery through order creation.
4. Payment: payment links, QRIS and confirmation.
5. Shipping: addresses, couriers, shipping costs and tracking.
6. Seller Operations: dashboard for products, stock, customers, orders and conversations.
7. CRM & Retention: segmentation, reorder reminders, preferences, loyalty, promotions, recommendations, campaigns and customer lifetime value.
8. Direct Storefront: seller website/PWA.
9. Marketplace Integration: integrate marketplace data if needed.
10. Mobile Ecosystem: consider only after sufficient product-market fit; one app following multiple sellers is a candidate direction, not an approved identity model.

WhatsApp is the first customer channel and MVP priority. This roadmap is conceptual, not a delivery commitment. Technical structure is owned by the backend and frontend documentation in their respective repositories. Platform Admin serves the operator; Seller Dashboard serves sellers. Their scopes are distinct. On 10 November 2026 the user confirmed Commerce-first implementation; Platform Admin login work is deferred. The old Identity scaffold is now archived under agent legacy/runtime-before-alignment; login remains deferred. See backend ADR 0013 and the shared FEATURE-STATUS.md. Phase 1 payment recording and fulfillment behavior still need specification; later payment/shipping integrations do not define those rules.

### Business model, validation and success

Subscription is the initial business-model hypothesis, avoiding a large transaction commission. Unvalidated monthly ranges: Starter Rp99,000–149,000; Growth Rp299,000–499,000. WhatsApp messaging, AI usage and payment-provider fees may add usage costs. These are not active plans, billing rules or pricing commitments.

Proposed validation: interview about 10 marketplace sellers and recruit 2–3 pilot sellers. Investigate revenue and effective marketplace costs, repeat customers, WhatsApp volume, current order/stock/follow-up processes, operational pain and willingness to pay. Validate seller ROI rather than assuming lower fees alone are sufficient differentiation.

Candidate metrics: active sellers, recognized customers, conversation-to-order conversion, direct-channel orders/GMV, repeat-order rate, operational time saved, customer and seller retention, AI-to-human handover rate and willingness to pay. Candidate north star: **repeat GMV generated through seller-owned channels**. Definitions, attribution, measurement windows and targets remain undecided.

Key risks to validate: marketplace convenience and customer reluctance, trust in direct transactions, seller willingness to pay, AI without business impact, marketplace fee changes and overbuilding before validation. Build incrementally; mobile, complex dashboards, advanced analytics and integrations are not prerequisites for validating the core need.

## Product direction
Help e-commerce sellers build direct customer relationships through their own WhatsApp, website and mobile channels. Marketplace channels support acquisition; the seller platform supports customer ownership and repeat purchases. This is a new commerce product, not a migration of the PUVA application or a confirmed legacy Seller codebase.

Source: the user-provided discussion "Rencana Migrasi Seller", https://chatgpt.com/share/6abf0fe6-c904-83ec-9c4a-7d8620142bf6, and the user's explicit instruction to start backend coding using PUVA's structure.

## Initial direction confirmed by the user
Start with WhatsApp and prepare backend APIs for products and orders. PUVA supplied the earlier technical reference; the current user instruction adopts the new starter. Technical implementation follows each repository architecture, not legacy PUVA layouts.

## Planned scope from the discussion
- Products: search, details, variants and stock availability.
- Customers: customer identity and contact association.
- Carts: view, add, update and remove items.
- Orders: create, retrieve, status and cancellation.

The detailed operations are planning inputs, not implemented or fully specified contracts. The discussion proposed PendingPayment as an initial order status; the lifecycle, stock reservation, prices, currency, idempotency and cancellation rules still require feature specifications.

## Application boundaries

Commerce owns business rules and authorized operations. Frontend consumes the backend API contract; future WhatsApp/AI integrations use authorized application contracts. AI never receives direct SQL access. Frontend checks help the user but never replace backend authorization or business invariants.

Payment and shipping integrations, storefront/PWA, seller dashboard, mobile application, platform admin, marketplace synchronization and broader CRM remain later scope. No separate microservice deployment is approved.

## Implementation tracking

Use [FEATURE-STATUS.md](FEATURE-STATUS.md) for shared progress with separate backend, frontend and integration evidence. Technical implementation is described in the [backend architecture](../../seller-hub-backend/docs/ARCHITECTURE.md) and [frontend architecture](../../seller-hub-frontend/docs/ARCHITECTURE.md). Historical backend alignment remains in its [review](../../seller-hub-backend/docs/ALIGNMENT-REVIEW.md). A planned capability is not implemented merely because a folder or document exists.

## Security and open decisions

The user selected a self-managed Identity provider and will participate in coding it. Platform Admin login remains deferred; account verification and token issuance are not implemented. Archived login input validation does not count as an active feature.

Seller/store tenancy, actor/customer binding, resource ownership, per-operation permissions and privacy retention must be specified before exposing business data. No business policy can be inferred from the old backend.read example policy, which has been removed from active code.

The WhatsApp provider, LLM provider, actual catalog source and any data-import process remain undecided. Technical libraries are owned by each repository; do not reselect them without a concrete need.

## Baseline capabilities

Backend baseline capabilities are defined in [BASELINE-FEATURES.md](../../seller-hub-backend/docs/BASELINE-FEATURES.md), with implementation status in its TODO.md. Detailed operational policies remain undecided for Seller Hub; the file/admin/WIB reference profile is not automatically adopted. Acceptance criteria for the first business feature remain to be specified.

## Source and precedence

Business context was recovered from agent legacy/docs/PRODUCT.md, PRODUCT-VISION.md, TODO.md and the Commerce-first decision in legacy ADR 0004. PRODUCT-VISION.md preserves the supplied vision. Legacy implementation statuses and technical topology are historical; shared documents define business requirements; each implementation repository documents its active code. See [shared ownership decision](decisions/0001-shared-specifications.md).
