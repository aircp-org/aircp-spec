# Contributing to AIRCP

Thanks for your interest in AIRCP. The protocol is a public draft, and community input is exactly what we need.

This document explains how to contribute and what kinds of contributions are most valuable right now.

---

## What we're looking for in v0.1

AIRCP v0.1 is a draft published for review. The most valuable contributions during the draft phase are:

1. **Implementation feedback.** If you tried to implement AIRCP and ran into ambiguity, friction, or impossibility, that is the highest-signal feedback we can receive. Open an issue describing the specific section, what you expected, and what actually happened.

2. **Schema clarifications.** If an attribute definition is unclear, contradictory, or missing an important type constraint, propose a clarification.

3. **Missing attributes.** If you believe a Tier 1 (Enhanced Product) or Tier 2 (Relevancy) attribute is missing that meaningfully limits the protocol, propose it.

4. **Reference implementations.** If you have built or are building an AIRCP-compatible catalog or agent, we want to know about it. Send a pull request to add yourself to the implementations registry (when published) or open an issue describing your implementation.

5. **Compliance benchmarks.** If you have ideas for what the benchmark suite should test, contribute to the discussion at `aircp-org/aircp-benchmarks` (to be published).

---

## What we are *not* looking for in v0.1

To keep the protocol focused, some kinds of changes are out of scope for v0.1:

- **Architectural rewrites** that abandon the two-tier model (Tier 1 Enhanced Product Attributes / Tier 2 Relevancy Attributes) or the separation between the Enhanced Catalog Layer and the Relevancy Reasoning Layer. These may be appropriate for a future major version but will not be considered in v0.1 → v1.0.
- **Proposals to standardize the Relevancy Reasoning Layer.** AIRCP intentionally does not define how agents match Tier 2 attributes against a specific buyer. That layer is where AI agents compete; standardizing it would erase that differentiation and slow innovation in the agent space.
- **Centralized infrastructure proposals.** AIRCP is deliberately decentralized. Proposals that require a central authority (registry, validator, etc.) will be deferred unless they are clearly optional infrastructure.
- **Closed-source dependencies.** AIRCP must remain implementable without any proprietary software or service.
- **Pricing or monetization.** AIRCP is free to implement and will remain so. The protocol does not concern itself with how implementations are funded.

---

## How to contribute

### Reporting issues

Open an issue at https://github.com/aircp-org/aircp-spec/issues with one of these labels:

- `spec-clarification` — the spec text is unclear or ambiguous
- `spec-bug` — the spec contradicts itself or contains an error
- `feature-request` — propose a new attribute, capability, or behavior
- `implementation-feedback` — feedback from an attempted implementation
- `discussion` — open-ended discussion that does not yet propose a specific change

Please include:

- The section of the spec you are referring to (cite section numbers)
- What you expected
- What is happening or what the spec actually says
- A proposed change, if you have one

### Proposing changes (pull requests)

If you have a specific change to propose, send a pull request against `main`.

Pull requests should:

- Address a single, well-scoped change
- Update `SPEC.md` directly (this is the authoritative document)
- Update the version number and changelog if the change is non-trivial
- Include rationale in the PR description: why this change, what alternative was considered, what backwards-compatibility implications it has

PR review timelines: we aim to respond within 5 business days during the draft period.

### Discussing big ideas

For larger discussions that are not yet ready to be issues or PRs, open a [discussion](https://github.com/aircp-org/aircp-spec/discussions).

---

## Code of conduct

Be civil. Be specific. Critique the spec, not the people.

We expect contributors to follow the standards of the [Contributor Covenant](https://www.contributor-covenant.org/). Concerns can be reported via [GitHub Issues](https://github.com/aircp-org/aircp-spec/issues).

---

## Decision-making

During the v0.1 draft phase, decisions on the specification are made by the AIRCP maintainers (currently the Shoop team) after considering community input.

We commit to:

1. **Transparency.** All decisions affecting the specification will be discussed publicly via GitHub issues, PRs, or discussions.
2. **Responsiveness.** Issues and PRs will receive a response within 5 business days.
3. **Rationale.** Decisions to accept or decline proposals will include written rationale.
4. **Reversibility.** During the v0.1 → v1.0 transition, decisions can be revisited if implementation experience reveals problems.

As AIRCP adoption grows, the maintainer model is expected to evolve. The Shoop team commits to transitioning to a multi-party governance structure once the protocol reaches sufficient adoption (defined as: at least 10 production implementations from at least 3 distinct organizations). See Section 11 of the [specification](./SPEC.md) for details.

---

## Conflict of interest

The Shoop team operates a production implementation of AIRCP on [shoop.world](https://shoop.world) and (forthcoming) [shoop.cloud](https://shoop.cloud). This is a structural conflict of interest.

We mitigate this by:

1. Publishing the full specification openly under MIT license.
2. Not gatekeeping the protocol. Any party may implement AIRCP without registration or permission.
3. Publishing public benchmarks against which any implementation can be measured.
4. Welcoming protocol critique, including critique that disadvantages Shoop's implementation.
5. Committing to transition governance once adoption thresholds are met.

If you believe a specific decision favors Shoop's implementation in a way that harms the broader protocol, please say so directly in the relevant issue or PR. We take this kind of feedback seriously.

---

## Licensing of contributions

By contributing to this repository, you agree that your contributions will be licensed under the [MIT License](./LICENSE), the same license as the rest of the project.

You retain copyright to your contributions. By contributing, you grant the AIRCP maintainers and downstream users the rights described in the MIT License.

---

## Acknowledgments

AIRCP is built on the work of many preceding open standards: [MCP](https://modelcontextprotocol.io), [schema.org](https://schema.org), [OpenAPI](https://www.openapis.org/), and the broader `/.well-known` (RFC 8615) tradition. We are grateful for the trail they blazed.

Thanks in advance for taking the time to contribute.

— The AIRCP maintainers
