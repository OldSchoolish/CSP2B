# CSP2B — Architecture

## Design Philosophy

CSP2B operates in **policy-first mode.**

Most CSP tooling takes a descriptive approach: scan the codebase, see what's there, recommend a policy that accommodates it. The problem with that approach is that it lets existing patterns — including insecure ones — define your security posture.

CSP2B inverts this. You define your target CSP profile before analysis begins. The engine then inventories the codebase and surfaces the gap between where you are and where you want to be. The output isn't "here's what your code does" — it's "here's what needs to change to meet your security bar."

This distinction matters. A policy derived from existing behavior is descriptive. A policy that defines a security standard and measures against it is prescriptive. CSP2B is the latter.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────┐
│                   CSP2B Pipeline                  │
│                                                     │
│  [1. Policy Config] → [2. Static Analysis]          │
│                              ↓                      │
│                    [3. Dependency Tracing]           │
│                              ↓                      │
│                    [4. Gap Report + Remediation Map] │
│                              ↓                      │
│                    [5. Dynamic Validation]*          │
│                              ↓                      │
│                    [6. Remediation Loop]*            │
│                                                     │
│  * Phase 2 — Roadmap                                │
└─────────────────────────────────────────────────────┘
```

---

## Phase 1 — Policy Configuration

Before any analysis runs, the user defines a target CSP profile. This profile represents the desired security posture — the bar the codebase needs to meet.

**Configuration includes:**
- Allowed source directives (`script-src`, `style-src`, `img-src`, etc.)
- Nonce vs hash preference for inline scripts
- Strictness level (e.g. no `unsafe-inline`, no `unsafe-eval`)
- Scope — full project or targeted directories

**Why this comes first:**
The target policy anchors everything downstream. The static analysis engine doesn't decide what's acceptable — the security engineer does. The engine's job is to measure the gap, not define the standard.

---

## Phase 2 — Static Analysis

The static analysis engine scans the codebase and builds a complete inventory of all resources that would be governed by a CSP.

**What it inventories:**
- External script references (`<script src="...">`)
- Inline JavaScript blocks
- Inline event handlers (`onclick`, `onload`, etc.)
- External stylesheet references
- Inline styles
- External resource references (fonts, images, media, frames)

**Language/tooling:**
- Python — chosen for its rich HTML/AST parsing ecosystem and readability
- Parses HTML for resource references, then follows into linked JS files where applicable

**Output:**
A structured inventory of every resource found, its type, location, and whether it currently conflicts with the target policy profile.

---

## Phase 3 — Dependency Tracing

Applying a nonce or hash to one script can break others that depend on it. The dependency tracer maps these relationships before any remediation is suggested.

**What it traces:**
- Scripts that reference or load other scripts
- Inline handlers that call external functions
- Logical groupings where a policy change to one asset affects others

**Why this matters:**
Blindly applying nonces without understanding dependencies is a common source of CSP-related breakage. CSP2B surfaces these relationships explicitly so remediation can be sequenced correctly.

**Output:**
A dependency graph overlaid on the inventory — each asset annotated with what it affects and what affects it.

---

## Phase 4 — Gap Report and Remediation Map

With the inventory and dependency graph complete, CSP2B produces two outputs:

**Gap Report:**
- What exists in the codebase that conflicts with the target policy
- Severity of each conflict relative to the configured policy strictness
- Prioritized list of what needs to change

**Remediation Map:**
- For each conflicting asset: what change is needed (nonce, hash, refactor, source directive addition)
- Sequenced based on dependency graph — changes that unblock others come first
- Estimated scope of each change

This is the primary deliverable of Phase 1. Everything downstream is validation.

---

## Phase 5 — Dynamic Validation *(Roadmap)*

The static analysis phase tells you what *should* be affected by a policy change. The dynamic validation phase tells you what *actually* triggers a violation at runtime.

**Approach:**
- Headless browser exercises the application against the target CSP
- Integrates with existing automated test suites or STEs to drive application logic
- Captures real violation reports — which directives fire, which scripts are blocked

**Stack:**
- Go — chosen for concurrency and speed; headless browser interactions benefit from parallel execution
- Designed to integrate with Playwright or Puppeteer via Go bindings

**Output:**
A validated violation report — confirming or correcting the static analysis findings with runtime evidence.

---

## Phase 6 — Automated Remediation Loop *(Roadmap)*

For violations surfaced during dynamic validation, CSP2B supports an automated remediation loop:

```
Flag violation → Apply nonce/hash → Revalidate → Confirm resolved → Next
```

**Design goals:**
- Non-destructive — changes are staged, not applied directly
- Dependency-aware — remediation respects the dependency graph from Phase 3
- Audit trail — every change is logged with before/after state

---

## Component Summary

| Component | Language | Role |
|---|---|---|
| Policy configurator | Python | Defines target CSP profile before analysis |
| Static analyzer | Python | Inventories codebase resources |
| Dependency tracer | Python | Maps inter-script relationships |
| Gap reporter | Python | Compares inventory to target policy |
| CLI | Python (Click) | User interface for all Phase 1 operations |
| Container | Docker | Portability across environments |
| Dynamic validator | Go *(planned)* | Headless browser runtime validation |
| Remediation loop | Go *(planned)* | Automated nonce/hash application and revalidation |
| Web interface | TBD *(planned)* | GUI for non-CLI users |

---

## Key Design Decisions

**Policy-first, not inventory-first**
The security bar is set by the engineer, not derived from existing code. This prevents insecure patterns from being laundered into a "recommended" policy.

**Dependency tracing before remediation**
Remediating without understanding dependencies breaks things. CSP2B maps relationships first so changes can be sequenced correctly.

**Static before dynamic**
Static analysis is fast and broad. Dynamic validation is slower but precise. Running static first narrows the scope of what dynamic validation needs to exercise.

**Go for dynamic, Python for static**
Python's parsing ecosystem makes it the right tool for static analysis. Go's concurrency model makes it the right tool for the headless browser retry loop where speed and parallel execution matter.

---

*See [ROADMAP.md](./ROADMAP.md) for implementation priorities and timeline.*
