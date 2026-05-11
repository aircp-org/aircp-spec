# AIRCP v0.1 Specification

**AI-Readable Catalog Protocol**

| | |
|---|---|
| **Version** | 0.1 |
| **Status** | Public Draft |
| **Published** | May 12, 2026 |
| **License** | [MIT](./LICENSE) |
| **Authors** | The Shoop team |
| **Maintained at** | https://github.com/aircp-org/aircp-spec |
| **Website** | https://aircp.org |

> *Organizing the inventories of the world into AIRCP.*

---

## Abstract

AIRCP — AI-Readable Catalog Protocol — is an open specification that defines how online product catalogs should be structured to be discoverable, parseable, and transactable by AI agents. AIRCP is to commerce what MCP (Model Context Protocol) is to AI tool use: a thin, semantic, agent-friendly layer over the existing commerce internet.

This document specifies AIRCP version 0.1. The protocol defines two tiers of product attributes (Core and Enhanced), a discovery mechanism using a well-known URL, transaction hooks for agent-initiated purchases, and a trust layer for merchant verification. AIRCP is designed to be implementable by any catalog system without modification of underlying commerce infrastructure.

AIRCP exists because the next decade of commerce will be conducted by AI agents acting on behalf of consumers, and the protocols that govern how those agents discover and transact against catalogs have not yet been standardized. AIRCP proposes a path.

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Design Principles](#2-design-principles)
3. [Conformance](#3-conformance)
4. [Catalog Discovery](#4-catalog-discovery)
5. [Tier 1: Core Attributes](#5-tier-1-core-attributes)
6. [Tier 2: Enhanced Attributes](#6-tier-2-enhanced-attributes)
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

---

## 2. Design Principles

The following principles guided the design of AIRCP v0.1. Future revisions of the specification should be evaluated against these principles.

### Principle 1: Catalog-agnostic

AIRCP must be implementable by any catalog system without modification of the underlying commerce infrastructure. A merchant should be able to add AIRCP support to an existing Shopify, WooCommerce, BigCommerce, custom PIM, or single-page e-commerce site by adding a single well-known endpoint.

### Principle 2: Agent-first, not human-first

AIRCP optimizes for AI agent consumption, not human readability. Attribute names are explicit. Types are strict. Edge cases are surfaced rather than hidden. Where this conflicts with human-readable conventions (verbose attribute names, redundant type information), agent-first wins.

### Principle 3: Two tiers, one protocol

AIRCP defines two tiers of attributes. Tier 1 (Core) is mandatory and deterministic: every conforming catalog must expose Core attributes, and Core attributes have unambiguous types and meanings. Tier 2 (Enhanced) is optional and semantic: it includes attributes like persona compatibility, taste suitability, and pairing recommendations that require sophisticated production methodology.

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
  "transaction_endpoint": "https://example.com/api/aircp/transaction",
  "verified": false,
  "last_updated": "2026-05-12T10:00:00Z",
  "contact": "aircp@example.com",
  "capabilities": [
    "core_attributes",
    "enhanced_attributes",
    "transaction_hooks",
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
- **Description:** List of AIRCP capabilities this catalog supports. Valid values: `"core_attributes"`, `"enhanced_attributes"`, `"transaction_hooks"`, `"search"`. Future versions may define additional capabilities.

```json
"capabilities": ["core_attributes", "enhanced_attributes", "search"]
```

---

## 5. Tier 1: Core Attributes

Tier 1 (Core) attributes are mandatory for every product in a conforming catalog. They are deterministic: there is no ambiguity in their values, and any two implementations should produce identical Core attributes for the same product.

This section defines all Core attributes. Implementations MUST produce all required Core attributes for every product. Implementations SHOULD produce all optional Core attributes when the information is available.

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

---

## 6. Tier 2: Enhanced Attributes

Tier 2 (Enhanced) attributes are optional and semantic. They describe taste, fit, suitability, and other dimensions that require interpretive judgment beyond what is encoded in deterministic product data.

> AIRCP defines Tier 2 attributes as **interface contracts** — specifying what each attribute is and how it should be structured — without prescribing how implementations should produce them. Different implementations are expected to produce Tier 2 values using different methodologies (knowledge libraries, AI inference, expert review, etc.), and competition on production quality is expected and encouraged.

Implementations that produce Tier 2 attributes MUST declare `"enhanced_attributes"` in the discovery document's capabilities array. Implementations MAY produce a subset of Tier 2 attributes (e.g., only `fit_profile` and `persona_compatibility`, omitting others).

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

AIRCP defines optional transaction hooks for agent-initiated purchases. Catalogs that support transactions MUST declare `"transaction_hooks"` in their discovery document capabilities and expose a `transaction_endpoint`.

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
    "core_attributes",
    "enhanced_attributes",
    "transaction_hooks",
    "search"
  ]
}
```

---

## 13. Acknowledgments and License

### 13.1 Acknowledgments

AIRCP draws inspiration from several preceding open standards:

- **MCP (Model Context Protocol):** Anthropic's protocol for AI tool use, which demonstrated that thin, agent-first protocols can compound community adoption rapidly.
- **schema.org:** The web's structured-data vocabulary, which informed our approach to attribute naming and type specifications.
- **OpenAPI:** The widely-adopted API specification format, which guided our approach to transaction hooks.
- **Web Application Manifest and /.well-known:** RFC 8615, which provides the discovery pattern AIRCP uses.

AIRCP would not exist without the trail blazed by these efforts. Where AIRCP diverges from any of these conventions, the divergence is intentional and documented.

### 13.2 License

This specification is published under the [MIT License](./LICENSE).

Implementations of AIRCP are subject to their own licenses. AIRCP places no licensing requirements on implementations.

### 13.3 Contact

For questions, proposals, or contributions to AIRCP:

- **GitHub:** https://github.com/aircp-org/aircp-spec
- **Website:** https://aircp.org
- **Specification authors:** spec@aircp.org

---

*End of AIRCP v0.1 Specification.*
