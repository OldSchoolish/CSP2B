# CSP2B

> Assisting with secure Content Security Policies, one codebase at a time.

---

## The Problem

Content Security Policies are one of the most effective browser-side defenses against XSS and injection attacks — but retrofitting them onto an existing project is a different challenge entirely than implementing them from the start.

Done right, a security-oriented CSP requires inventorying every script, inline handler, and external resource across a codebase, understanding how they relate to one another, and making targeted code adjustments (nonces, hashes, source directives) without breaking functionality. In a large or legacy project, that process is slow, error-prone, and easy to get wrong.

CSP2B is here to pick up the pace of these challenges by automating the inventory, analysis, and policy recommendation process — giving security engineers and developers a clear picture of what they're working with and a path toward a functional, restrictive CSP.

---

## What It Does

CSP2B takes a hybrid static/dynamic approach to CSP analysis:

### Phase 1 — Static Analysis *(implemented)*
- Scans a codebase and inventories all scripts, inline JavaScript, and external resource references
- Traces logical script dependencies — if applying a nonce to one script would affect another, CSP2B surfaces that relationship
- Identifies candidates for nonce and hash application
- Recommends the most restrictive functional CSP based on findings, anchored to a target policy profile you define

### Phase 2 — Dynamic Validation *(roadmap)*
- Uses a headless browser to validate which scripts actually trigger policy violations vs which pass cleanly
- Designed to integrate with existing automated test suites or STEs to exercise application logic
- Supports an automated remediation loop: flag a violation → apply nonce/hash → revalidate → repeat

---

## Who It's For

CSP2B is useful for:
- **Security engineers** auditing existing applications for CSP coverage
- **Developers** implementing or tightening CSP on projects where it wasn't a day-one consideration
- **AppSec teams** looking to reduce the manual effort of CSP retrofitting at scale

---

## Stack

| Component | Language | Rationale |
|---|---|---|
| Static analyzer | Python | Rich AST/parsing ecosystem, readable, widely adopted in security tooling |
| CLI | Python (Click) | Clean interface, easy to extend |
| Dynamic validator | Go *(planned)* | Concurrency and speed for headless browser retry loops |
| Container | Docker | Portability across environments |

---

## Project Status

CSP2B is an active rebuild of a tool originally developed and validated in a professional AppSec environment. The architecture and static analysis phase are well-defined; the dynamic validation phase is designed and roadmapped.

| Feature | Status |
|---|---|
| Static codebase inventory | 🔨 In progress |
| Dependency tracing | 🔨 In progress |
| CSP policy recommendation | 🔨 In progress |
| CLI interface | 🔨 In progress |
| Docker container | 🔨 In progress |
| Dynamic headless validation | 📋 Roadmap |
| Nonce/hash auto-injection | 📋 Roadmap |
| Automated remediation loop | 📋 Roadmap |
| Web interface | 📋 Roadmap |

---

## Architecture

See [ARCHITECTURE.md](./ARCHITECTURE.md) for a detailed breakdown of the static analysis pipeline, policy recommendation model, and dynamic validation design.

---

## Roadmap

See [ROADMAP.md](./ROADMAP.md) for planned features and implementation priorities.

---

## Background

CSP2B grew out of real-world AppSec work — the kind where you inherit a mature codebase with no CSP, a tight timeline, and the immediate question of *where do I even start?* The goal was always to make that starting point faster, more systematic, and less dependent on manual grep-and-pray.

---

*CSP2B is a portfolio reconstruction project. No proprietary code, data, or organizational specifics from prior employment are included.*
