# AIRCP — AI-Readable Catalog Protocol

**An open specification for AI agents to discover, parse, and transact against online product catalogs.**

AIRCP is to commerce what [MCP](https://modelcontextprotocol.io) is to AI tool use: a thin, semantic, agent-friendly layer over the existing commerce internet.

This repository contains the AIRCP specification and reference materials.

- **Specification:** [SPEC.md](./SPEC.md)
- **Website:** [aircp.org](https://aircp.org)
- **Current version:** v0.1 (Public Draft, May 2026)
- **License:** [MIT](./LICENSE)

---

## Why AIRCP exists

The commerce internet was designed for human readers. Product catalogs are rendered as HTML pages optimized for visual consumption, with semantic structure encoded primarily in markup conventions retrofitted onto an architecture built for browsers, not agents.

AI agents acting on behalf of consumers face three structural problems when transacting against this catalog surface:

- **Discovery.** No standard way to find which catalogs exist or how to query them.
- **Interpretation.** Product data is inconsistent across merchants. Attribute names differ, types are unspecified, important context is buried in unstructured description text.
- **Transaction.** Even when an agent identifies the right product, completing the purchase requires merchant-specific integration.

AIRCP addresses these three problems with a thin, semantic protocol that any catalog can implement without changing its underlying commerce infrastructure.

---

## What AIRCP defines

1. **Discovery** — a well-known URL pattern (`https://{host}/.well-known/aircp.json`) that any agent can query to find an AIRCP-compatible catalog.
2. **Catalog format** — a structured JSON representation of products with two tiers of attributes (Core and Enhanced).
3. **Transaction interface** — standard methods for agents to query, add to cart, check out, and receive purchase confirmations.
4. **Trust layer** — a verification mechanism for merchant identity and catalog integrity.

AIRCP is implementable by any catalog system. A Shopify store, an enterprise PIM, a marketplace, or a single-product brand can all expose AIRCP.

---

## What AIRCP is not

AIRCP is not a marketplace, a search engine, a payment processor, or a fulfillment system. AIRCP does not host catalogs, route transactions, or take a cut of sales. AIRCP is a specification of how catalogs and agents interact.

AIRCP does not require centralized registration. Any catalog publisher can expose an AIRCP endpoint without notifying any central authority.

---

## Quick start for implementers

Implementing AIRCP at the Core tier takes a few hours for most catalogs.

### Minimum viable implementation

1. Publish a discovery document at `https://{your-host}/.well-known/aircp.json` declaring `aircp_version: "0.1"` and your catalog endpoint.
2. Expose a catalog endpoint that returns products with all required Core attributes (`id`, `title`, `description`, `images`, `category`, `price`, `availability`).
3. Declare your supported capabilities (`core_attributes` at minimum).

### Example discovery document

```json
{
  "aircp_version": "0.1",
  "catalog_name": "Example Store",
  "catalog_url": "https://example.com",
  "catalog_endpoint": "https://example.com/api/aircp/catalog",
  "product_endpoint_pattern": "https://example.com/api/aircp/product/{id}",
  "capabilities": ["core_attributes"],
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
