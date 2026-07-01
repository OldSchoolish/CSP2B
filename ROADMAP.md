# CSP2B — Roadmap

This document tracks the current state of CSP2B and what's planned. No timeline is attached — this is a portfolio rebuild project worked on alongside full-time AppSec engineering work.

---

## Current State

CSP2B is in active reconstruction. The architecture is fully defined. Implementation is underway on Phase 1 (static analysis).

---

## In Progress

- [x] Static codebase inventory (HTML parsing, script/resource detection)
- [x] Inline JS and event handler identification
- [x] Dependency tracing (inter-script relationship mapping)
- [x] Policy configuration interface (target CSP profile input)
- [x] Gap report generation (inventory vs target policy comparison)
- [x] Remediation map output (sequenced change recommendations)
- [x] CLI interface (Python/Click)
- [ ] Docker container

---

## Planned

- [ ] Dynamic validation via headless browser (Go)
- [ ] Runtime violation capture and reporting
- [ ] Automated remediation loop (flag → apply → revalidate → confirm)
- [ ] Integration hooks for existing automated test suites
- [ ] Nonce/hash auto-injection (staged, non-destructive)
- [ ] Web interface for non-CLI users
- [ ] Support for framework-specific patterns (React, Vue, etc.)

---

## Known Design Constraints

- Dynamic validation requires an application that can be driven by automated tests or STEs — CSP2B does not generate test coverage, it integrates with existing coverage
- Nonce auto-injection is staged by design — CSP2B proposes changes, it does not apply them without review
- Dependency tracing is based on static logical analysis; runtime-only dynamic imports may not be captured until Phase 2

---

## Out of Scope (for now)

- Real-time CSP monitoring or violation aggregation (separate tooling concern)
- Browser extension interface
- SaaS/hosted version

---

*See [ARCHITECTURE.md](./ARCHITECTURE.md) for the full technical design.*
