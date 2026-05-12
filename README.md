# AIRCP — AI-Readable Catalog Protocol

**An open specification for the Enhanced Catalog Layer of agentic commerce.**

AIRCP defines how online product catalogs should be structured so AI agents can discover, parse, and reason about products with the depth required for relevancy-aware recommendation.

This repository contains the AIRCP specification and reference materials.

- **Specification:** [SPEC.md](./SPEC.md)
- **Website:** [aircp.org](https://aircp.org)
- **Current version:** v0.1 (Public Draft, May 2026)
- **License:** [MIT](./LICENSE)

---

## What AIRCP is

The plumbing of agentic commerce has been assembled over the last 18 months:

- **MCP** (Anthropic, 2024) — how AI tools expose themselves to agents
- **A2A** (Google, 2025) — how agents talk to other agents
- **AP2** (Google + 60 partners, Sept 2025) — how agent-initiated payments are authorized
- **UCP** (Google + Shopify + Stripe + Visa, Jan 2026) — how unified commerce transactions complete

Four protocols for the transactional plumbing. None of them define the catalog data layer — how products should be structured for AI agents to reason about style, fit, image projection, occasion suitability, and the rest of what actually drives a recommendation.

UCP's catalog spec defines title, description, price, variants, rating. That's the same data your search-engine sitemap already exposes. **AIRCP defines what comes next.**

---

## The two-layer model: open catalog, closed reasoning

AIRCP defines two tiers of attributes, both open:

### Tier 1 — Enhanced Product Attributes (buyer-agnostic)

UCP-compatible base data plus enriched descriptive and relational attributes — style classification, image projection, context suitability, product-to-product compatibility. Everything an agent needs to know about a product, regardless of who is buying.

### Tier 2 — Relevancy Attributes (buyer-relevant)

Interpretive attributes designed to be matched against a buyer's profile — fit profile, persona compatibility, occasion suitability. Published openly in the catalog, but interpretively useful only when matched against a buyer model.

**The buyer model is intentionally outside the protocol.** That's the Relevancy Reasoning Layer — where AI agents compete with each other. AIRCP standardizes the data inputs to that layer without prescribing how the layer operates.

This separation means AIRCP can be fully open without commoditizing agents that build proprietary buyer reasoning on top of it.

---

## What AIRCP defines

1. **Discovery** — a well-known URL pattern (`https://{host}/.well-known/aircp.json`) that any agent can query to find an AIRCP-compatible catalog.
2. **Catalog format** — a structured JSON representation of products with Tier 1 (Enhanced Product) and Tier 2 (Relevancy) attributes.
3. **Transaction hooks** — an optional minimal transaction interface for catalogs that have not yet implemented UCP. AIRCP defers to UCP for production transaction flows.
4. **Trust layer** — a verification mechanism for merchant identity and catalog integrity.

AIRCP is implementable by any catalog system. A Shopify store, an enterprise PIM, a marketplace, or a single-product brand can all expose AIRCP.

---

## What AIRCP is not

AIRCP is not a marketplace, a search engine, a payment processor, or a fulfillment system. AIRCP does not host catalogs, route transactions, or take a cut of sales. AIRCP is a specification of how catalogs and agents interact.

AIRCP does not require centralized registration. Any catalog publisher can expose an AIRCP endpoint without notifying any central authority.

---

## How AIRCP composes with the rest of the stack

AIRCP is designed to compose with — not duplicate — the existing protocols:

- **AIRCP and UCP:** An AIRCP-compliant catalog publishes the Enhanced Catalog Layer. A UCP-compliant catalog publishes the transaction layer. Merchants are encouraged to implement both. Agents should defer to UCP's checkout session format for transactions.
- **AIRCP and AP2:** Payment authorization for AIRCP-discovered products should use AP2's Intent Mandate and Cart Mandate flow.
- **AIRCP and A2A:** Agents implementing AIRCP discovery and reasoning can communicate with other agents via A2A.
- **AIRCP and MCP:** A catalog can expose its AIRCP endpoint as an MCP tool, allowing MCP-aware agents to consume AIRCP-formatted product data through the MCP interface.

Full composition details are in [SPEC.md Section 1.5](./SPEC.md#15-relationship-to-other-protocols).

---

## Quick start for implementers

Implementing AIRCP at the Tier 1 level takes a few hours for most catalogs.

### Minimum viable implementation

1. Publish a discovery document at `https://{your-host}/.well-known/aircp.json` declaring `aircp_version: "0.1"` and your catalog endpoint.
2. Expose a catalog endpoint that returns products with all required Tier 1 attributes (`id`, `title`, `description`, `images`, `category`, `price`, `availability`).
3. Declare your supported capabilities (`product_attributes` at minimum).

### Example discovery document

```json
{
  "aircp_version": "0.1",
  "catalog_name": "Example Store",
  "catalog_url": "https://example.com",
  "catalog_endpoint": "https://example.com/api/aircp/catalog",
  "product_endpoint_pattern": "https://example.com/api/aircp/product/{id}",
  "capabilities": ["product_attributes"],
  "verified": false,
  "last_updated": "2026-05-12T10:00:00Z"
}
```

### Example minimal product

```json
{
  "id": "prod_001",
  "title": "Stainless Steel Water Bottle",
  "description": "24oz double-walled vacuum-insulated bottle.",
  "category": "Home & Kitchen > Drinkware",
  "images": [
    {
      "url": "https://example.com/img/bottle.jpg",
      "alt_text": "Stainless steel water bottle"
    }
  ],
  "price": { "amount": 32.00, "currency": "USD" },
  "availability": "in_stock"
}
```

Full attribute reference is in [SPEC.md](./SPEC.md).

---

## Repository structure

| File | Purpose |
|---|---|
| `SPEC.md` | The AIRCP v0.1 specification. The authoritative document. |
| `README.md` | This file. Introduction and quick start. |
| `CONTRIBUTING.md` | How to propose changes, file issues, and contribute. |
| `LICENSE` | MIT License. |

Future additions to this org will include `aircp-benchmarks` (the compliance test suite), `aircp-examples` (reference implementations), and other supporting infrastructure.

---

## Status

AIRCP v0.1 is a **public draft published for community review.** The specification is expected to evolve based on input from implementers, agent developers, and merchants over approximately 90 days following initial publication, after which a v1.0 specification will be released.

Breaking changes between v0.1 and v1.0 are possible and will be documented in the changelog.

---

## Governance

AIRCP is currently maintained by the [Shoop](https://shoop.world) team, with input from the public community via GitHub issues and pull requests.

The Shoop team operates a production implementation of AIRCP and commits to transitioning AIRCP to a multi-party governance structure once the protocol reaches sufficient adoption (defined as: at least 10 production implementations from at least 3 distinct organizations). See Section 11 of the [specification](./SPEC.md) for the full governance disclosure.

---

## Contributing

We welcome implementations, protocol critique, issue reports, and pull requests. See [CONTRIBUTING.md](./CONTRIBUTING.md) for details.

---

## License

AIRCP is published under the [MIT License](./LICENSE). Implementations of AIRCP are subject to their own licenses; AIRCP places no licensing requirements on implementations.

---

## Contact

- **GitHub Issues:** https://github.com/aircp-org/aircp-spec/issues
- **GitHub Repo:** https://github.com/aircp-org/aircp-spec
- **Website:** https://aircp.org
