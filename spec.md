# AIRCP v0.1 Specification

**AI-Readable Catalog Protocol**

| | |
|---|---|
| **Version** | 0.1 |
| **Status** | Public Draft |
| **Published** | May 12, 2026 |
| **Last revised** | May 12, 2026 |
| **License** | [MIT](./LICENSE) |
| **Authors** | The Shoop team |
| **Maintained at** | https://github.com/aircp-org/aircp-spec |
| **Website** | https://aircp.org |

> *Organizing the inventories of the world into AIRCP.*

---

## Abstract

AIRCP — AI-Readable Catalog Protocol — is an open specification that defines the **Enhanced Catalog Layer** of agentic commerce: how online product catalogs should be structured so AI agents can discover, parse, and reason about products with the depth required for relevancy-aware recommendation.

AIRCP defines two tiers of attributes:

- **Tier 1 — Enhanced Product Attributes** (buyer-agnostic): UCP-compatible base data plus enriched descriptive and relational attributes — material, color, weight, style classification, the image a product projects, the contexts it works in, product-to-product compatibility. Everything an agent needs to know about a product, regardless of who is buying.
- **Tier 2 — Relevancy Attributes** (buyer-relevant): Interpretive attributes designed to be matched against a buyer's profile — fit profile, persona compatibility, occasion suitability. Published in the catalog, but interpretively useful only when matched against a buyer model that the publishing protocol does not define.

Both tiers are open and published in AIRCP-format catalogs. The catalog publishes product knowledge. Buyer knowledge — and the reasoning that matches Tier 2 attributes against a specific buyer — sits in the **Relevancy Reasoning Layer**, which is intentionally outside the protocol. The Relevancy Reasoning Layer is where AI agents differentiate from each other. AIRCP standardizes the data inputs to that layer without prescribing how the layer operates.

AIRCP is designed to compose with the rest of the agentic commerce stack: Universal Commerce Protocol (UCP) for checkout and order management, Agent Payments Protocol (AP2) for payment authorization, Agent2Agent (A2A) for agent-to-agent communication, and Model Context Protocol (MCP) for tool exposure. AIRCP is implementable by any catalog system without modification of underlying commerce infrastructure.

AIRCP exists because the protocols for transaction (UCP, AP2), agent communication (A2A), and tool use (MCP) are well-defined and rapidly adopting — but the Enhanced Catalog Layer they all assume is largely missing. Today's product catalogs expose enough structured data for browsers and search engines, not for AI agents that need to reason about style, image projection, occasion fit, and product-to-product compatibility, let alone buyer-specific relevancy. AIRCP fills that gap.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Design Principles](#2-design-principles)
3. [Conformance](#3-conformance)
4. [Catalog Discovery](#4-catalog-discovery)
5. [Tier 1: Enhanced Product Attributes](#5-tier-1-enhanced-product-attributes)
6. [Tier 2: Relevancy Attributes](#6-tier-2-relevancy-attributes)
7. [Transaction Hooks](#7-transaction-hooks)
8. [Trust and Verification](#8-trust-and-verification)
9. [Versioning](#9-versioning)
10. [Compliance Benchmarks](#10-compliance-benchmarks)
11. [Governance](#11-governance)
12. [Reference Examples](#12-reference-examples)
13. [Acknowledgments and License](#13-acknowledgments-and-license)

---

## 1. Introduction

### 1.1 The problem

The commerce internet was designed for human readers. Product catalogs are rendered as HTML pages optimized for visual consumption, with semantic structure encoded primarily in markup conventions (schema.org, Open Graph) that were retrofitted onto an architecture built for browsers, not agents.

AI agents acting on behalf of consumers face three structural problems when transacting against this catalog surface:

- **Discovery:** There is no standard way for an agent to discover which catalogs exist, what they contain, or how to query them. Agents resort to web scraping, search engine APIs, or per-merchant integrations.
- **Interpretation:** Product data is inconsistent across merchants. Attribute names differ, types are unspecified, important context like materials, sizing, and suitability is buried in unstructured description text or absent entirely.
- **Transaction:** Even when an agent identifies the right product, completing the purchase requires merchant-specific integration. There is no standard way for an agent to add to cart, check out, or confirm a purchase across catalogs.

AIRCP addresses these three problems by specifying a thin, semantic protocol that any catalog can implement without changing its underlying commerce infrastructure.

### 1.2 What AIRCP is

AIRCP is a specification, not a service. The protocol defines:

1. A discovery mechanism: a well-known URL pattern that any agent can query to find an AIRCP-compatible catalog.
2. A catalog format: a structured JSON representation of products with Core and Enhanced attributes.
3. A transaction interface: standard methods for agents to query, add to cart, check out, and receive purchase confirmations.
4. A trust layer: a verification mechanism for merchant identity and catalog integrity.

AIRCP is implementable by any catalog system. The protocol is content-agnostic and does not prescribe specific product categories, business models, or commerce platforms. A Shopify store, an enterprise PIM, a marketplace, or a single-product brand can all expose AIRCP.

### 1.3 What AIRCP is not

AIRCP is not a marketplace, a search engine, a payment processor, or a fulfillment system. AIRCP does not host catalogs, route transactions, or take a cut of sales. AIRCP is a specification of how catalogs and agents interact; the underlying infrastructure remains the responsibility of merchants, payment processors, and agents.

AIRCP does not require centralized registration. Any catalog publisher can expose an AIRCP endpoint without notifying any central authority. A registry of known AIRCP-compatible catalogs may emerge over time as community infrastructure, but is not required for the protocol to function.

### 1.4 Status of this document

AIRCP v0.1 is a draft published for public review and community feedback. The specification is expected to evolve based on input from implementers, agent developers, and merchants over a period of approximately 90 days following initial publication, after which a v1.0 specification will be released.

> ⚠️ Implementers should not consider v0.1 stable for production use without acknowledgment that the specification may change. Breaking changes between v0.1 and v1.0 are possible and will be documented in the changelog.

### 1.5 Relationship to other protocols

AIRCP is one layer of a multi-protocol stack that defines agentic commerce. It composes with — and explicitly does not duplicate — the following standards.

#### The agentic commerce stack

| Layer | Protocol | What it defines | Status |
|---|---|---|---|
| Tool exposure | **MCP** (Anthropic, 2024) | How AI tools expose themselves to agents | Widely adopted |
| Agent communication | **A2A** (Google, 2025) | How agents talk to other agents | Adopting |
| Payment authorization | **AP2** (Google + 60 partners, Sept 2025) | Verifiable user consent for agent-initiated payments via Intent Mandates, Cart Mandates, and Payment Mandates | Adopting (Apache 2.0) |
| Commerce transaction | **UCP** (Google + Shopify/Etsy/Wayfair/Target/Walmart, Jan 2026) | Unified checkout sessions, identity linking, order management, real-time pricing/inventory | Adopting (Apache 2.0) |
| **Enhanced Catalog** | **AIRCP** (this specification) | **Tier 1 enriched product attributes (style, image, context, product-to-product compatibility) and Tier 2 relevancy attributes (designed to match against a buyer model)** | **Public draft (MIT)** |
| Relevancy Reasoning | *intentionally out of protocol scope* | *How an agent matches Tier 2 attributes against a specific buyer's profile* | *Agent-side, proprietary* |

#### The two-layer model: open catalog, closed reasoning

AIRCP defines the **Enhanced Catalog Layer** — what merchants publish. It does *not* define the **Relevancy Reasoning Layer** — how agents match published Tier 2 attributes against a specific buyer to produce a personalized recommendation. This separation is deliberate.

- The Enhanced Catalog Layer is a coordination problem: every merchant should expose product data the same way, so every agent can read it. Solving coordination problems requires open standards. AIRCP is MIT licensed for this reason.
- The Relevancy Reasoning Layer is a differentiation problem: agents compete on how well they understand their users and how well they match products to users. Solving differentiation problems requires proprietary innovation. AIRCP intentionally does not standardize this layer.

Tier 2 attributes are published openly in the catalog, but they are interpretively useful only when matched against a buyer model — and the buyer model is the agent's proprietary asset, not part of the protocol. An agent without a sophisticated buyer model can still consume Tier 2 attributes, but it must rely on user-stated criteria within a single conversation rather than a persistent buyer profile. Agents with sophisticated buyer models (such as continuously-learning taste models built from quiz responses, revealed preferences, and purchase behavior) gain disproportionately more value from Tier 2 attributes than stateless agents do.

This separation means AIRCP can be fully open without commoditizing agents that build proprietary buyer reasoning on top of it.

#### Why the stack has a gap

UCP, AP2, A2A, and MCP each solve real problems and together cover most of the agentic commerce surface. But all four assume the catalog they are reasoning about is already AI-readable in the enhanced sense — that style classification, image projection, product-to-product compatibility, and other interpretive product attributes are present and machine-parseable. In practice, this is rarely true. The catalog data retailers expose today was designed for browsers and search engines. UCP's catalog spec defines title, description, price, variants, options, media, and rating — descriptive transaction-oriented data. It does not define style classification, product-to-product compatibility, image projection, or any of the interpretive attributes that turn a product listing into something an AI agent can reason about.

A concrete example: an agent helping a user find "a minimalist black wool coat that works for both business and casual settings and pairs with my existing wardrobe" can complete the transaction via UCP and authorize payment via AP2 — but only if some catalog has already exposed the style_classification, image_projection, occasion_versatility, and pairs_with attributes in a structured form. UCP does not define these. AIRCP Tier 1 does. Now extend the query: "...for my body type, in my style preference, that I won't return." That extension requires matching against the user's Shooping Cart profile — which is what Tier 2 attributes enable, when an agent has the proprietary buyer model to do the matching.

#### How AIRCP composes with each layer

- **AIRCP and UCP:** An AIRCP-compliant catalog publishes the Enhanced Catalog Layer. A UCP-compliant catalog publishes the transaction layer. The same merchant can — and is encouraged to — implement both. An agent that discovers a product via AIRCP and wants to complete the purchase should defer to UCP's checkout session format. AIRCP does not define a competing checkout flow; see Section 7.
- **AIRCP and AP2:** Payment authorization for AIRCP-discovered products should use AP2's Intent Mandate and Cart Mandate flow. AIRCP does not define a competing payment authorization mechanism.
- **AIRCP and A2A:** Agents implementing AIRCP discovery and reasoning can communicate with other agents via A2A. AIRCP does not define agent-to-agent communication.
- **AIRCP and MCP:** A catalog can expose its AIRCP endpoint as an MCP tool, allowing MCP-aware agents to consume AIRCP-formatted product data through the MCP interface. AIRCP and MCP are complementary.

#### Why AIRCP is published separately rather than as a UCP extension

UCP's three core capabilities at launch (Checkout, Identity Linking, Order Management) are oriented toward the transaction surface. The Enhanced Catalog Layer is a distinct concern with distinct authors — typically not the engineers who own checkout but the teams who own product information management, merchandising, and AI-driven recommendation. Conflating these layers in one specification would slow adoption of both. Publishing AIRCP as a thin separate standard that explicitly composes with UCP allows each protocol to evolve at the speed of its domain.

We expect — and welcome — future versions of UCP to formally reference AIRCP-compliant catalog endpoints as an input source, and future versions of AIRCP to formally reference UCP-compliant transaction endpoints as the recommended checkout layer. Section 7 of this specification reflects that intent.

---

## 2. Design Principles

The following principles guided the design of AIRCP v0.1. Future revisions of the specification should be evaluated against these principles.

### Principle 1: Catalog-agnostic

AIRCP must be implementable by any catalog system without modification of the underlying commerce infrastructure. A merchant should be able to add AIRCP support to an existing Shopify, WooCommerce, BigCommerce, custom PIM, or single-page e-commerce site by adding a single well-known endpoint.

### Principle 2: Agent-first, not human-first

AIRCP optimizes for AI agent consumption, not human readability. Attribute names are explicit. Types are strict. Edge cases are surfaced rather than hidden. Where this conflicts with human-readable conventions (verbose attribute names, redundant type information), agent-first wins.

### Principle 3: Two tiers, one protocol

AIRCP defines two tiers of attributes. Tier 1 (Enhanced Product) is buyer-agnostic: it describes the product itself, including its intrinsic properties (material, color, weight) and relational properties (style classification, image projection, products it pairs with). Tier 1 has unambiguous types and meanings. Tier 2 (Relevancy) is buyer-relevant: it includes attributes like persona compatibility, fit profile, and occasion suitability that are designed to be matched against a buyer's profile. Tier 2 attributes are published openly in the catalog, but the reasoning that matches them against a specific buyer is intentionally out of protocol scope — that is the Relevancy Reasoning Layer, owned by each agent implementation.

The protocol defines Tier 2 as a set of interface contracts. Implementations are responsible for producing Tier 2 values. AIRCP does not prescribe how Tier 2 values are produced; competition on production methodology is expected and encouraged.

### Principle 4: No central authority required

AIRCP is a specification, not a service. No party is required to register, pay, or apply for permission to implement AIRCP. The specification is MIT-licensed and freely implementable. A registry of AIRCP-compatible catalogs may emerge as community infrastructure, but is explicitly not required for the protocol to function.

### Principle 5: Forward-compatible by default

AIRCP catalogs include a version field. Agents are expected to handle catalogs of older versions gracefully. The protocol defines a minimum compatibility envelope and a process for non-breaking extension.

---

## 3. Conformance

This section uses the conformance keywords defined in RFC 2119 (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY). When these keywords appear in capitals elsewhere in this document, they carry the meanings defined in that RFC.

### 3.1 Conforming catalog

A conforming AIRCP catalog **MUST**:

1. Expose a discovery document at the well-known URL defined in Section 4.
2. Include all Core (Tier 1) attributes for every product in the catalog.
3. Use the AIRCP JSON schema for all attribute values.
4. Declare the AIRCP version it conforms to.

A conforming catalog **SHOULD**:

1. Expose Enhanced (Tier 2) attributes where available.
2. Support transaction hooks for agent-initiated checkout.
3. Sign catalog responses for trust verification (see Section 8).

### 3.2 Conforming agent

A conforming AIRCP agent **MUST**:

1. Resolve catalogs via the well-known URL pattern.
2. Parse and interpret Core attributes according to their defined types.
3. Handle missing Enhanced attributes gracefully without failure.
4. Respect rate limits and conformance signals returned by catalogs.

---

## 4. Catalog Discovery

AIRCP uses the well-known URL pattern defined in RFC 8615 for catalog discovery. Any HTTPS domain MAY expose an AIRCP-compatible catalog by serving a discovery document at the path defined below.

### 4.1 Discovery endpoint

The discovery document MUST be available at:

```
https://{host}/.well-known/aircp.json
```

Where `{host}` is the fully qualified domain of the catalog publisher. The response MUST be a JSON document with the structure defined in Section 4.2.

### 4.2 Discovery document structure

```json
{
  "aircp_version": "0.1",
  "catalog_name": "Example Store",
  "catalog_url": "https://example.com",
  "catalog_endpoint": "https://example.com/api/aircp/catalog",
  "product_endpoint_pattern": "https://example.com/api/aircp/product/{id}",
  "search_endpoint": "https://example.com/api/aircp/search",
  "ucp_endpoint": "https://example.com/.well-known/ucp.json",
  "transaction_endpoint": "https://example.com/api/aircp/transaction",
  "verified": false,
  "last_updated": "2026-05-12T10:00:00Z",
  "contact": "aircp@example.com",
  "capabilities": [
    "product_attributes",
    "relevancy_attributes",
    "ucp_compatible",
    "search"
  ]
}
```

### 4.3 Discovery document fields

#### `aircp_version` — **REQUIRED**

- **Type:** `string`
- **Description:** The AIRCP specification version this catalog conforms to. MUST be a valid semver string. v0.1 implementations MUST set this to `"0.1"`.

```json
"aircp_version": "0.1"
```

#### `catalog_name` — **REQUIRED**

- **Type:** `string`
- **Description:** Human-readable name of the catalog. Used by agents to identify the catalog source.

```json
"catalog_name": "Example Store"
```

#### `catalog_url` — **REQUIRED**

- **Type:** `string (URL)`
- **Description:** The canonical URL of the catalog's primary surface. For e-commerce sites, this is typically the homepage.

```json
"catalog_url": "https://example.com"
```

#### `catalog_endpoint` — **REQUIRED**

- **Type:** `string (URL)`
- **Description:** The endpoint at which the full catalog or paginated catalog listings can be retrieved. Agents query this endpoint to enumerate products.

```json
"catalog_endpoint": "https://example.com/api/aircp/catalog"
```

#### `product_endpoint_pattern` — **REQUIRED**

- **Type:** `string (URL pattern)`
- **Description:** URL pattern for retrieving individual product details. MUST include the substring `{id}` as a placeholder for the product identifier.

```json
"product_endpoint_pattern": "https://example.com/api/aircp/product/{id}"
```

#### `search_endpoint` — *OPTIONAL*

- **Type:** `string (URL)`
- **Description:** Endpoint for semantic search across the catalog. If present, agents may submit natural language queries. If absent, agents must enumerate the catalog directly.

```json
"search_endpoint": "https://example.com/api/aircp/search"
```

#### `transaction_endpoint` — *OPTIONAL*

- **Type:** `string (URL)`
- **Description:** Endpoint for agent-initiated transactions (add to cart, checkout, confirm purchase). If absent, the catalog is browse-only via AIRCP; transactions must be completed through the catalog's native commerce surface.

```json
"transaction_endpoint": "https://example.com/api/aircp/transaction"
```

#### `verified` — **REQUIRED**

- **Type:** `boolean`
- **Description:** Whether the catalog has been verified through an AIRCP trust authority (see Section 8). v0.1 implementations may set this to `false`. Production implementations are encouraged to seek verification.

```json
"verified": false
```

#### `last_updated` — **REQUIRED**

- **Type:** `string (ISO 8601 timestamp)`
- **Description:** When the discovery document was last updated. Agents use this to detect catalog changes.

```json
"last_updated": "2026-05-12T10:00:00Z"
```

#### `contact` — *OPTIONAL*

- **Type:** `string (email)`
- **Description:** Contact email for issues related to AIRCP implementation. Used by agents to report inconsistencies or errors.

```json
"contact": "aircp@example.com"
```

#### `capabilities` — **REQUIRED**

- **Type:** `array of strings`
- **Description:** List of AIRCP capabilities this catalog supports. Valid values: `"product_attributes"`, `"relevancy_attributes"`, `"transaction_hooks"`, `"search"`. Future versions may define additional capabilities.

```json
"capabilities": ["product_attributes", "relevancy_attributes", "search"]
```

---

## 5. Tier 1: Enhanced Product Attributes

Tier 1 attributes describe a product comprehensively — both its intrinsic properties (material, color, weight) and its relational properties (style classification, image projection, contexts where it works, products it pairs with). Tier 1 is **buyer-agnostic**: every Tier 1 attribute is true about the product itself, independent of who is buying it.

Tier 1 extends what UCP's catalog spec defines (title, description, price, variants, options, media, rating) with the enriched descriptive and relational attributes an AI agent needs to reason about a product before matching it to a specific buyer.

This section defines all Tier 1 attributes. Implementations MUST produce all required Tier 1 attributes for every product. Implementations SHOULD produce all optional Tier 1 attributes when the information is available. Implementations that wish to be UCP-compatible SHOULD also implement UCP's catalog schema; AIRCP Tier 1 is designed to nest cleanly inside a UCP-compliant catalog response via the `metadata` field, or to be published in parallel at a separate AIRCP endpoint.

### 5.1 Identity attributes

#### `id` — **REQUIRED**

- **Type:** `string`
- **Description:** Unique identifier for this product within the catalog. MUST be stable across catalog updates. Agents use this to track products across queries and sessions.

```json
"id": "prod_a47x9q"
```

#### `sku` — *OPTIONAL*

- **Type:** `string`
- **Description:** Stock keeping unit, if the catalog uses SKUs. For catalogs with variants, sku refers to the specific variant.

```json
"sku": "SHIRT-WHT-MED"
```

#### `gtin` — *OPTIONAL*

- **Type:** `string`
- **Description:** Global Trade Item Number (UPC, EAN, ISBN). Highly recommended where available to enable cross-catalog product matching.

```json
"gtin": "00012345678905"
```

#### `brand` — *OPTIONAL*

- **Type:** `string`
- **Description:** Brand name. SHOULD be the canonical brand name as the brand uses it (e.g., `"Patagonia"`, not `"patagonia"` or `"PATAGONIA"`).

```json
"brand": "Patagonia"
```

### 5.2 Descriptive attributes

#### `title` — **REQUIRED**

- **Type:** `string`
- **Description:** Product title. Should be the title as a human consumer would see it on the product page. Length recommended under 100 characters.

```json
"title": "Lightweight Merino Wool Crew Neck Sweater"
```

#### `description` — **REQUIRED**

- **Type:** `string`
- **Description:** Product description in plain text or markdown. May be long. Should contain factual information about the product; marketing prose is allowed but discouraged.

```json
"description": "100% merino wool crew neck sweater. Lightweight 250gsm weight suitable for layering. Ribbed cuffs and hem. Machine washable on wool cycle."
```

#### `images` — **REQUIRED**

- **Type:** `array of objects`
- **Description:** Product images. MUST include at least one image. Each image object has: `url` (string, required), `alt_text` (string, optional), `width` (integer, optional), `height` (integer, optional).

```json
"images": [
  {
    "url": "https://example.com/img/sweater_main.jpg",
    "alt_text": "White merino sweater, front view",
    "width": 1200,
    "height": 1600
  }
]
```

#### `category` — **REQUIRED**

- **Type:** `string`
- **Description:** Top-level category for this product. Free-form string. Implementations are encouraged to use Google Product Category names where applicable.

```json
"category": "Apparel > Tops > Sweaters"
```

#### `tags` — *OPTIONAL*

- **Type:** `array of strings`
- **Description:** Free-form tags. Used for filtering and discovery. May include style descriptors, occasion tags, season tags, or merchant-defined categorizations.

```json
"tags": ["merino", "sweater", "layering", "fall", "winter"]
```

### 5.3 Pricing attributes

#### `price` — **REQUIRED**

- **Type:** `object`
- **Description:** Current price. Object contains: `amount` (number, required), `currency` (string ISO 4217, required), and optional fields `original_amount` (for sale items) and `price_per_unit` (for products sold by weight/volume).

```json
"price": {
  "amount": 145.00,
  "currency": "USD",
  "original_amount": 195.00
}
```

#### `availability` — **REQUIRED**

- **Type:** `string (enum)`
- **Description:** Product availability. MUST be one of: `"in_stock"`, `"out_of_stock"`, `"limited"`, `"preorder"`, `"backorder"`, `"discontinued"`.

```json
"availability": "in_stock"
```

#### `inventory_count` — *OPTIONAL*

- **Type:** `integer`
- **Description:** Estimated number of units available. Optional but recommended for limited availability products.

```json
"inventory_count": 47
```

### 5.4 Variant attributes

#### `variants` — *OPTIONAL*

- **Type:** `array of objects`
- **Description:** Product variants (different sizes, colors, materials). Each variant is an object containing the same Core attributes as the parent product, plus a `variant_attributes` object describing what makes this variant distinct.

```json
"variants": [
  {
    "id": "prod_a47x9q_sm",
    "variant_attributes": {
      "size": "S"
    },
    "price": { "amount": 145.00, "currency": "USD" },
    "availability": "in_stock"
  }
]
```

### 5.5 Physical attributes

#### `dimensions` — *OPTIONAL*

- **Type:** `object`
- **Description:** Physical dimensions of the product. Object contains: `length`, `width`, `height` (all numbers), and `unit` (string, MUST be one of: `"mm"`, `"cm"`, `"in"`).

```json
"dimensions": {
  "length": 28,
  "width": 22,
  "height": 1,
  "unit": "in"
}
```

#### `weight` — *OPTIONAL*

- **Type:** `object`
- **Description:** Product weight. Object contains: `value` (number) and `unit` (string, MUST be one of: `"g"`, `"kg"`, `"oz"`, `"lb"`).

```json
"weight": {
  "value": 350,
  "unit": "g"
}
```

#### `materials` — *OPTIONAL*

- **Type:** `array of objects`
- **Description:** Materials composition. Each entry has: `name` (string, required) and `percentage` (number 0-100, optional).

```json
"materials": [
  { "name": "merino wool", "percentage": 100 }
]
```

### 5.6 Logistics attributes

#### `shipping` — *OPTIONAL*

- **Type:** `object`
- **Description:** Shipping information. Object contains: `ships_from` (string ISO 3166-1 country code), `available_destinations` (array of country codes or `"global"`), `free_shipping_threshold` (object with `amount` and `currency`), and `estimated_days` (object with `min` and `max` integers).

```json
"shipping": {
  "ships_from": "US",
  "available_destinations": ["US", "CA"],
  "free_shipping_threshold": { "amount": 100, "currency": "USD" },
  "estimated_days": { "min": 3, "max": 7 }
}
```

#### `returns` — *OPTIONAL*

- **Type:** `object`
- **Description:** Return policy. Object contains: `window_days` (integer), `restocking_fee` (object with amount/currency, or null), `conditions` (string).

```json
"returns": {
  "window_days": 30,
  "restocking_fee": null,
  "conditions": "Unworn, with tags"
}
```

### 5.7 Style and context attributes

These attributes describe a product in terms of its style, the image it projects, the contexts where it works, and how it relates to other products. They are buyer-agnostic: every value is true about the product itself, not about who is buying it. They are what distinguishes Tier 1 from UCP's transaction-oriented catalog spec, and they are what makes Tier 1 useful to AI agents reasoning about products *before* knowing who the buyer is.

#### `style_classification` — *OPTIONAL*

- **Type:** `array of strings`
- **Description:** Stylistic categories the product belongs to. Implementations SHOULD draw from a published vocabulary; the protocol does not prescribe one. Common categories include: `minimalist`, `maximalist`, `classic`, `contemporary`, `avant-garde`, `streetwear`, `preppy`, `bohemian`, `industrial`, `mid_century_modern`, `scandinavian`. For non-apparel categories (home, beauty, outdoor), implementations use category-appropriate style vocabularies.

```json
"style_classification": ["minimalist", "contemporary"]
```

#### `image_projection` — *OPTIONAL*

- **Type:** `array of strings`
- **Description:** What the product signals or projects when used or worn. Examples: `quiet_confidence`, `craft_aware`, `understated_luxury`, `playful`, `serious`, `aspirational`, `accessible`, `expert`, `beginner`. These are buyer-agnostic — they describe what the product says, not how a specific buyer experiences it.

```json
"image_projection": ["quiet_confidence", "craft_aware", "understated"]
```

#### `context_suitability` — *OPTIONAL*

- **Type:** `array of strings`
- **Description:** Contexts in which the product is appropriate. Examples for apparel: `workplace_business_casual`, `client_facing`, `formal_event`, `weekend_casual`, `athletic`, `travel`. Examples for home: `formal_living_room`, `casual_family_room`, `home_office`, `outdoor_patio`. Examples for beauty: `everyday_wear`, `professional_setting`, `evening_out`, `special_occasion`.

```json
"context_suitability": ["workplace_business_casual", "client_facing", "travel"]
```

#### `pairs_with` — *OPTIONAL*

- **Type:** `array of objects`
- **Description:** Other product categories or specific products this product pairs with. Each entry contains: `category` (string, e.g., "chinos", "wool_trousers"), `strength` (number 0.0–1.0, indicates how strong the pairing is), `notes` (optional string explaining the pairing). For apparel, describes outfit compatibility. For home goods, describes design coordination. For beauty, describes complementary products.

```json
"pairs_with": [
  { "category": "chinos", "strength": 0.92, "notes": "Color and silhouette align well" },
  { "category": "wool_trousers", "strength": 0.88 },
  { "category": "denim", "strength": 0.35, "notes": "Weaker pairing; tonal mismatch" }
]
```

#### `substitute_for` — *OPTIONAL*

- **Type:** `array of objects`
- **Description:** Products this product can serve as a substitute for, with substitution strength. Used by agents when a primary recommendation is out of stock or out of budget. Each entry: `category_or_product_id` (string), `strength` (number 0.0–1.0), `notes` (optional string).

```json
"substitute_for": [
  { "category_or_product_id": "merino_polo", "strength": 0.78 },
  { "category_or_product_id": "cashmere_crewneck", "strength": 0.65, "notes": "Lower price point, similar silhouette" }
]
```

#### `weather_suitability` — *OPTIONAL*

- **Type:** `object`
- **Description:** Weather and seasonal suitability. Object contains: `temp_range_celsius` (array of two integers, min/max), `seasons` (array of strings: `spring`, `summer`, `fall`, `winter`), `conditions` (array of strings: `dry`, `humid`, `rainy`, `cold_dry`, `cold_wet`).

```json
"weather_suitability": {
  "temp_range_celsius": [10, 22],
  "seasons": ["spring", "fall"],
  "conditions": ["dry", "mild_outdoor"]
}
```

#### `aesthetic_compatibility` — *OPTIONAL*

- **Type:** `object`
- **Description:** Describes which design or aesthetic systems the product belongs to. For home goods, this is especially important. Object contains: `design_era` (e.g., "mid_century_modern", "art_deco", "minimalist"), `color_family` (e.g., "warm_neutral", "cool_neutral", "earth_tones", "jewel_tones"), `material_palette` (array of strings describing material/finish characteristics).

```json
"aesthetic_compatibility": {
  "design_era": "mid_century_modern",
  "color_family": "warm_neutral",
  "material_palette": ["walnut", "brass", "linen", "matte_finish"]
}
```

---

## 6. Tier 2: Relevancy Attributes

Tier 2 attributes are **buyer-relevant**: they describe a product in terms designed to be matched against a specific buyer's profile, preferences, body, context, and history. Where Tier 1 says "this is a slim-fit merino sweater in oatmeal," Tier 2 says "this sweater fits petite-to-average builds well, suits a minimalist-professional persona, works for client-facing settings."

Tier 2 attributes are published openly in the catalog like Tier 1. **They are interpretively useful only when matched against a buyer model.** An agent without a sophisticated buyer model can still consume Tier 2 attributes — using them as filters against user-stated criteria within a single conversation — but agents with persistent, continuously-learning buyer models gain disproportionately more value.

This separation is deliberate. AIRCP defines what Tier 2 attributes are (Enhanced Catalog Layer). AIRCP does *not* define how an agent matches them against a specific buyer (Relevancy Reasoning Layer). The Relevancy Reasoning Layer is where AI agents differentiate from each other; standardizing it would erase that differentiation and slow innovation in the agent space.

> AIRCP defines Tier 2 attributes as **interface contracts** — specifying what each attribute is and how it should be structured — without prescribing how implementations should produce them. Different implementations are expected to produce Tier 2 values using different methodologies (knowledge libraries, AI inference, expert review, etc.), and competition on production quality is expected and encouraged.

Implementations that produce Tier 2 attributes MUST declare `"relevancy_attributes"` in the discovery document's capabilities array. Implementations MAY produce a subset of Tier 2 attributes (e.g., only `fit_profile` and `persona_compatibility`, omitting others).

### 6.1 Persona compatibility

#### `persona_compatibility` — *OPTIONAL*

- **Type:** `object`
- **Description:** Object mapping persona archetype identifiers to compatibility scores (0.0–1.0). Indicates how well this product fits each of a defined set of consumer personas. Each implementation defines its own persona universe; the protocol does not prescribe persona definitions. The persona universe used by an implementation MUST be documented and available to agents on request.

```json
"persona_compatibility": {
  "minimalist_professional": 0.92,
  "sustainable_aesthete": 0.78,
  "tech_forward_pragmatist": 0.45
}
```

### 6.2 Fit profile

#### `fit_profile` — *OPTIONAL*

- **Type:** `object`
- **Description:** Object describing how a product fits the body or use case. For apparel, may include fields like `fit_type` (regular/slim/loose), `best_for_body_shapes` (array of body shape identifiers), and `runs` (one of: `"small"`, `"true_to_size"`, `"large"`). For non-apparel, fit_profile describes ergonomic or functional suitability.

```json
"fit_profile": {
  "fit_type": "regular",
  "best_for_body_shapes": ["rectangle", "inverted_triangle"],
  "runs": "true_to_size"
}
```

### 6.3 Style attributes

#### `style_descriptors` — *OPTIONAL*

- **Type:** `array of strings`
- **Description:** Free-form style descriptors for the product. May include aesthetic tags (minimalist, maximalist, vintage), context tags (workplace appropriate, formal, casual), or trend tags. The vocabulary is not standardized; agents are expected to handle unknown values gracefully.

```json
"style_descriptors": ["minimalist", "workplace_appropriate", "timeless"]
```

#### `color_attributes` — *OPTIONAL*

- **Type:** `object`
- **Description:** Detailed color information beyond the primary color name. Object contains: `primary_color` (string), `color_family` (string), `hex` (string), `warmth` (one of: `"warm"`, `"neutral"`, `"cool"`), `saturation` (one of: `"muted"`, `"medium"`, `"vibrant"`).

```json
"color_attributes": {
  "primary_color": "oatmeal",
  "color_family": "beige",
  "hex": "#D6CCBA",
  "warmth": "warm",
  "saturation": "muted"
}
```

### 6.4 Occasion and use

#### `occasion_suitability` — *OPTIONAL*

- **Type:** `object`
- **Description:** Object mapping occasion identifiers to suitability scores (0.0–1.0). Standard occasions: `"casual_daily"`, `"workplace"`, `"formal_event"`, `"athletic"`, `"outdoor"`, `"travel"`, `"loungewear"`. Implementations MAY define additional occasions; standard occasion identifiers SHOULD be used where applicable.

```json
"occasion_suitability": {
  "casual_daily": 0.9,
  "workplace": 0.85,
  "formal_event": 0.2,
  "outdoor": 0.6
}
```

#### `season_suitability` — *OPTIONAL*

- **Type:** `object`
- **Description:** Object mapping seasons (`"spring"`, `"summer"`, `"fall"`, `"winter"`) to suitability scores (0.0–1.0). Useful for agents performing seasonal recommendations.

```json
"season_suitability": {
  "spring": 0.7,
  "summer": 0.3,
  "fall": 0.95,
  "winter": 0.95
}
```

### 6.5 Pairing and combinatorial attributes

#### `pairs_well_with` — *OPTIONAL*

- **Type:** `array of objects`
- **Description:** Suggested pairings with other product categories or types. Each object contains: `category` (string) and `pairing_strength` (number 0.0–1.0). Pairings may reference broad categories (e.g., `"chino_pants"`) or specific product IDs in the same catalog.

```json
"pairs_well_with": [
  { "category": "chino_pants", "pairing_strength": 0.9 },
  { "category": "denim_jeans", "pairing_strength": 0.85 },
  { "category": "tailored_trousers", "pairing_strength": 0.75 }
]
```

#### `complements` — *OPTIONAL*

- **Type:** `array of strings (product IDs)`
- **Description:** Specific product IDs from this catalog or others (using format `"{catalog_host}:{product_id}"`) that complement this product. Used by agents to construct outfits, kits, or bundles.

```json
"complements": [
  "prod_b58y2r",
  "example.com:prod_c69z3s"
]
```

### 6.6 Quality and provenance

#### `quality_signals` — *OPTIONAL*

- **Type:** `object`
- **Description:** Object containing quality-related signals. Fields may include: `construction_quality` (one of: `"economy"`, `"standard"`, `"premium"`, `"luxury"`), `durability_score` (number 0-10), `expected_lifespan_years` (number), `origin_country` (ISO 3166-1 code).

```json
"quality_signals": {
  "construction_quality": "premium",
  "durability_score": 8.5,
  "expected_lifespan_years": 5,
  "origin_country": "IT"
}
```

#### `sustainability` — *OPTIONAL*

- **Type:** `object`
- **Description:** Object containing sustainability-related attributes. Fields may include: `certifications` (array of strings), `recycled_content_percentage` (number 0-100), `carbon_footprint_kg_co2e` (number), `repairable` (boolean), `end_of_life_options` (array of strings).

```json
"sustainability": {
  "certifications": ["GOTS", "Fair Trade"],
  "recycled_content_percentage": 0,
  "repairable": true,
  "end_of_life_options": ["recyclable", "biodegradable"]
}
```

### 6.7 Care and maintenance

#### `care_instructions` — *OPTIONAL*

- **Type:** `object`
- **Description:** Object containing care information. For apparel: `wash_method`, `dry_method`, `iron_method`, `dry_clean`. For other products: `cleaning_method`, `storage_recommendations`, `special_care`.

```json
"care_instructions": {
  "wash_method": "machine_cold_wool",
  "dry_method": "lay_flat",
  "iron_method": "low_heat",
  "dry_clean": false
}
```

### 6.8 Reasoning and explainability

#### `reasoning_summary` — *OPTIONAL*

- **Type:** `string`
- **Description:** A short (under 280 characters) natural-language summary of why this product matches certain contexts or personas. Intended to be displayed by agents to end users to explain recommendations. Generated by the implementation; format and style are at the implementation's discretion.

```json
"reasoning_summary": "A workhorse sweater for someone who prioritizes longevity. The 250gsm weight makes it versatile across seasons, and the merino composition handles a wider temperature range than most cotton or synthetic alternatives."
```

### 6.9 Implementer-specific extensions

#### `extensions` — *OPTIONAL*

- **Type:** `object`
- **Description:** Optional object containing implementer-specific attributes that are not part of the AIRCP specification. Keys MUST be namespaced by the implementer (e.g., `"shoop.verdict_color"`, `"acme.internal_score"`). Agents SHOULD ignore extensions they do not understand. Extensions are how implementations innovate ahead of the protocol.

```json
"extensions": {
  "shoop.verdict_color": "strong_buy",
  "shoop.taste_match_score": 0.91
}
```

---

## 7. Transaction Hooks

> **Important — read first.** AIRCP does not define a competing checkout protocol. As of v0.1, the recommended path for transaction completion against AIRCP-discovered products is **Universal Commerce Protocol (UCP)** for checkout, identity linking, and order management, and **Agent Payments Protocol (AP2)** for payment authorization. Both are open Apache 2.0 specifications co-developed by Google and the major commerce and payments industry. See Section 1.5 for the relationship between AIRCP and these protocols.

The transaction hooks defined in this section are an **optional fallback** for catalogs that have not yet implemented UCP. They define a minimal transaction interface so that AIRCP-only catalogs can still be transacted against by AIRCP-aware agents. We expect this section to be deprecated in AIRCP v1.0, with formal redirection to UCP for all transaction flows.

Catalogs that support AIRCP transaction hooks MUST declare `"transaction_hooks"` in their discovery document capabilities and expose a `transaction_endpoint`. Catalogs that implement UCP SHOULD declare `"ucp_compatible"` in their capabilities instead, and link to their UCP discovery endpoint in the `ucp_endpoint` field of the AIRCP discovery document.

### 7.1 Transaction flow

The AIRCP transaction flow is:

1. Agent submits a `transaction_intent` to the `transaction_endpoint`, specifying the product, variant, quantity, and shipping/billing details.
2. Catalog responds with a `transaction_session` containing a `session_id`, total cost, and any required next steps (e.g., payment authorization URL).
3. Agent completes any required steps (e.g., payment) and submits `transaction_confirmation` to finalize the purchase.
4. Catalog responds with a `transaction_result` containing the final status, order ID, and any post-purchase information.

### 7.2 Transaction intent

POST to `transaction_endpoint` with the following structure:

```json
{
  "action": "create_intent",
  "items": [
    {
      "product_id": "prod_a47x9q",
      "variant_id": "prod_a47x9q_md",
      "quantity": 1
    }
  ],
  "shipping_address": {
    "name": "...",
    "line1": "...",
    "city": "...",
    "postal_code": "...",
    "country": "US"
  },
  "agent_metadata": {
    "agent_id": "agent_xyz",
    "acting_on_behalf_of": "user_abc"
  }
}
```

### 7.3 Transaction session response

```json
{
  "session_id": "sess_5x9q2v",
  "total": { "amount": 158.95, "currency": "USD" },
  "breakdown": {
    "subtotal": { "amount": 145.00, "currency": "USD" },
    "shipping": { "amount": 8.95, "currency": "USD" },
    "tax": { "amount": 5.00, "currency": "USD" }
  },
  "requires": ["payment_authorization"],
  "payment_url": "https://example.com/pay/sess_5x9q2v",
  "expires_at": "2026-05-12T11:00:00Z"
}
```

### 7.4 Transaction confirmation

POST to `transaction_endpoint` with:

```json
{
  "action": "confirm",
  "session_id": "sess_5x9q2v",
  "payment_authorization": "auth_token_returned_from_payment_url"
}
```

### 7.5 Transaction result

```json
{
  "status": "confirmed",
  "order_id": "ord_abc123",
  "order_url": "https://example.com/orders/ord_abc123",
  "estimated_delivery": "2026-05-19",
  "tracking_available_at": "https://example.com/orders/ord_abc123/tracking"
}
```

### 7.6 Error handling

Transactions may fail at any stage. Catalogs MUST return HTTP 400-class errors for client errors and 500-class for server errors, with a JSON body containing:

```json
{
  "error": "out_of_stock",
  "error_description": "Product prod_a47x9q variant md is no longer available.",
  "resolution": "Try a different variant or notify the user."
}
```

Standard error codes: `"out_of_stock"`, `"invalid_variant"`, `"shipping_unavailable"`, `"payment_failed"`, `"session_expired"`, `"rate_limited"`, `"unauthorized"`, `"internal_error"`.

---

## 8. Trust and Verification

AIRCP includes an optional trust layer for merchant verification. v0.1 defines the verification mechanism but does not establish a centralized trust authority. Future versions of the specification may define community trust authorities or distributed verification mechanisms.

### 8.1 Self-asserted verification

In v0.1, catalogs MAY set the `"verified"` field in their discovery document to `true` if they have completed self-attestation. Self-attestation requires:

1. Publishing a verification document at `https://{host}/.well-known/aircp-verification.json`
2. Including a cryptographic signature over the discovery document using a publicly verifiable key (e.g., a JWS in the discovery document headers)
3. Listing the merchant's legal entity name, registered country, and a verifiable contact method

Agents SHOULD treat self-attestation as a weak trust signal in v0.1. Stronger verification mechanisms are expected in v1.0.

### 8.2 Catalog response signing

Catalogs MAY sign catalog responses to prove integrity. Signing uses standard JWS (JSON Web Signature). Agents that verify signatures MUST handle unsigned responses gracefully.

### 8.3 Rate limiting and abuse signals

Catalogs MUST include rate limit headers in API responses:

```
X-AIRCP-RateLimit-Limit: 1000
X-AIRCP-RateLimit-Remaining: 847
X-AIRCP-RateLimit-Reset: 1715520000
```

Agents that exceed rate limits SHOULD back off exponentially. Catalogs MAY return HTTP 429 with a `"retry_after"` field indicating seconds until the next allowed request.

---

## 9. Versioning

AIRCP uses semantic versioning (MAJOR.MINOR). Changes within a MAJOR version are backward compatible. Changes that break compatibility require a MAJOR version increment.

### 9.1 Version negotiation

Catalogs declare their AIRCP version in the discovery document. Agents inspect this field and adapt their behavior accordingly. Agents MUST gracefully handle catalogs of versions they were not designed for, falling back to the highest common version.

### 9.2 Forward compatibility

Within a MAJOR version, agents MUST:

1. Ignore unknown attributes rather than failing.
2. Ignore unknown capability declarations in the discovery document.
3. Handle additional optional fields in transaction responses.

### 9.3 Deprecation

Attributes or capabilities marked for deprecation in a future version MUST be supported by implementations for at least one MAJOR version cycle (approximately 12 months expected). Deprecation announcements will be published on AIRCP.org and via the protocol's published changelog.

---

## 10. Compliance Benchmarks

AIRCP publishes a public benchmark suite for testing implementations. The benchmark suite is hosted at `https://github.com/aircp-org/aircp-benchmarks` and may be run by any implementation to verify conformance.

### 10.1 Core benchmark

The Core benchmark tests:

- Presence of all required discovery document fields
- Schema validity of returned product data against the AIRCP JSON Schema
- Presence of all required Core attributes on every product
- Type correctness of all attribute values
- Pagination, rate limiting, and error response correctness

### 10.2 Enhanced benchmark

The Enhanced benchmark tests Tier 2 attribute quality against a public gold-standard test set. Implementations are scored on attribute coverage (what percentage of products have each Enhanced attribute) and attribute quality (alignment with the gold standard for that attribute).

The gold standard is maintained by the AIRCP community and reflects expert judgment in apparel, home, beauty, outdoor, and other categories. Implementations that produce Enhanced attributes are encouraged to publish their benchmark scores.

### 10.3 Reference implementations

Reference implementations of AIRCP-compatible catalogs and AIRCP-aware agents are listed at https://aircp.org/implementations. Reference implementations are encouraged but not certified by any central authority.

---

## 11. Governance

AIRCP is governed as an open specification. The current governance model is:

### 11.1 Specification maintenance

The AIRCP specification is currently maintained by the Shoop team, with input from the public community via GitHub issues and pull requests. Maintenance responsibilities include:

- Reviewing and merging proposed changes
- Releasing new versions of the specification
- Maintaining the official benchmarks
- Resolving disputes about specification interpretation

### 11.2 Future governance

As AIRCP adoption grows, governance is expected to evolve toward a multi-party model. Possible future structures include:

- A foundation modeled on the OpenJS Foundation or Cloud Native Computing Foundation
- A working group modeled on the W3C or IETF
- A community-elected steering committee

The Shoop team commits to transitioning AIRCP to a multi-party governance structure once the protocol reaches sufficient adoption (defined as: at least 10 production implementations from at least 3 distinct organizations).

### 11.3 Conflict of interest disclosure

The Shoop team is the primary author of AIRCP and operates a production implementation of AIRCP on shoop.world and (forthcoming) shoop.cloud. This creates a structural conflict of interest. We mitigate this by:

1. Publishing the full specification, including Enhanced attributes, openly under MIT license.
2. Not gatekeeping the protocol. Any party may implement AIRCP without registration or permission.
3. Publishing public benchmarks against which any implementation can be measured.
4. Welcoming community contributions and protocol critique via GitHub.
5. Committing to transition governance once adoption thresholds are met.

---

## 12. Reference Examples

This section provides complete reference examples of AIRCP-compatible catalog responses.

### 12.1 Minimal Core-only product

```json
{
  "id": "prod_min_001",
  "title": "Stainless Steel Water Bottle",
  "description": "24oz double-walled vacuum-insulated bottle.",
  "category": "Home & Kitchen > Drinkware",
  "images": [
    {
      "url": "https://example.com/img/bottle.jpg",
      "alt_text": "Stainless steel water bottle"
    }
  ],
  "price": {
    "amount": 32.00,
    "currency": "USD"
  },
  "availability": "in_stock"
}
```

### 12.2 Full Core + Enhanced product (apparel)

```json
{
  "id": "prod_a47x9q",
  "sku": "SWTR-OAT-MED",
  "gtin": "00084012345678",
  "brand": "Northbound",
  "title": "Lightweight Merino Wool Crew Neck Sweater",
  "description": "100% merino wool crew neck...",
  "category": "Apparel > Tops > Sweaters",
  "tags": ["merino", "sweater", "layering"],
  "images": [
    { "url": "https://example.com/img/sweater.jpg" }
  ],
  "price": {
    "amount": 145.00,
    "currency": "USD"
  },
  "availability": "in_stock",
  "inventory_count": 47,
  "materials": [
    { "name": "merino wool", "percentage": 100 }
  ],
  "weight": { "value": 350, "unit": "g" },
  "shipping": {
    "ships_from": "US",
    "available_destinations": ["US", "CA"],
    "estimated_days": { "min": 3, "max": 7 }
  },
  "persona_compatibility": {
    "minimalist_professional": 0.92,
    "sustainable_aesthete": 0.78
  },
  "fit_profile": {
    "fit_type": "regular",
    "runs": "true_to_size"
  },
  "style_descriptors": [
    "minimalist",
    "workplace_appropriate",
    "timeless"
  ],
  "color_attributes": {
    "primary_color": "oatmeal",
    "color_family": "beige",
    "warmth": "warm"
  },
  "occasion_suitability": {
    "casual_daily": 0.9,
    "workplace": 0.85,
    "formal_event": 0.2
  },
  "pairs_well_with": [
    { "category": "chino_pants", "pairing_strength": 0.9 },
    { "category": "denim_jeans", "pairing_strength": 0.85 }
  ],
  "reasoning_summary": "A workhorse sweater for someone who prioritizes longevity. The 250gsm weight makes it versatile across seasons."
}
```

### 12.3 Sample discovery document

```json
{
  "aircp_version": "0.1",
  "catalog_name": "Northbound",
  "catalog_url": "https://northbound.example",
  "catalog_endpoint": "https://northbound.example/api/aircp/catalog",
  "product_endpoint_pattern": "https://northbound.example/api/aircp/product/{id}",
  "search_endpoint": "https://northbound.example/api/aircp/search",
  "transaction_endpoint": "https://northbound.example/api/aircp/transaction",
  "verified": false,
  "last_updated": "2026-05-12T10:00:00Z",
  "contact": "aircp@northbound.example",
  "capabilities": [
    "product_attributes",
    "relevancy_attributes",
    "transaction_hooks",
    "search"
  ]
}
```

---

## 13. Acknowledgments and License

### 13.1 Acknowledgments

AIRCP exists as a semantic layer within a multi-protocol stack. We acknowledge and build on the following open standards:

- **MCP (Model Context Protocol):** Anthropic's protocol for AI tool exposure, which demonstrated that thin, agent-first protocols can compound community adoption rapidly. MIT licensed.
- **A2A (Agent2Agent Protocol):** Google's protocol for agent-to-agent communication.
- **AP2 (Agent Payments Protocol):** The open protocol for agent-initiated payment authorization via Intent Mandates, Cart Mandates, and Payment Mandates. Co-developed by Google with 60+ partners including Mastercard, PayPal, Coinbase, and Adyen. Apache 2.0 licensed.
- **UCP (Universal Commerce Protocol):** The open protocol for unified commerce transactions — checkout sessions, identity linking, order management — co-developed by Google with Shopify, Etsy, Wayfair, Target, Walmart, and endorsed by Stripe, Salesforce, Visa, and others. Apache 2.0 licensed. AIRCP-compliant catalogs SHOULD be UCP-compliant for transactions.
- **schema.org:** The web's structured-data vocabulary, which informed our approach to attribute naming and type specifications.
- **OpenAPI:** The widely-adopted API specification format, which guided our approach to the optional transaction hooks fallback.
- **Web Application Manifest and /.well-known:** RFC 8615, which provides the discovery pattern AIRCP uses.

AIRCP would not exist without the trail blazed by these efforts. Where AIRCP diverges from any of these conventions, the divergence is intentional and documented. Where AIRCP composes with these standards, the composition is documented in Section 1.5.

### 13.2 License

This specification is published under the [MIT License](./LICENSE).

Implementations of AIRCP are subject to their own licenses. AIRCP places no licensing requirements on implementations.

### 13.3 Contact

For questions, proposals, or contributions to AIRCP:

- **GitHub repository:** https://github.com/aircp-org/aircp-spec
- **Issues (bugs, spec ambiguities):** https://github.com/aircp-org/aircp-spec/issues
- **Discussions (proposals, questions):** https://github.com/aircp-org/aircp-spec/discussions
- **Website:** https://aircp.org

---

*End of AIRCP v0.1 Specification.*
