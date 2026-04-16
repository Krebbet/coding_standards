# Project Maturity Stages

Projects are categorised by their maturity stage. Each stage carries different expectations for code quality, review depth, and testing rigour.

## Definitions

**Research code** — quickly written for exploration, PoC models, or data investigation. Generally not extensible, not tested, and not production-quality. Receives a conceptual review.

**Production code** — written for a robust software product. Tested, extensible, well-formatted, with intuitive APIs. Receives a detailed review.

**Conceptual review** — assesses: does the code achieve the desired result? Is it bug-free? Can it be understood with reasonable effort? Focuses on *what*, not *how*.

**Detailed review** — assesses all of the above plus: does it follow software best practices (extensibility, low coupling, efficiency, testability)? Focuses on both *what* and *how*.

---

## Proof of Concept (PoC)

**Purpose:** Prove out a concept at speed.

**Audience:** Technical folk continuing the research; decision-makers evaluating investment.

**Code quality:** Research code.

**Review:** Conceptual.

**Deployment:** Not intended for deployment. Adding features requires high effort.

**Testing:** See [[testing/testing-by-maturity]].

---

## Minimum Viable Product (MVP)

**Purpose:** Release a working product for limited use.

**Audience:** Small group of somewhat technical users; decision-makers evaluating scale.

**Code quality:** Mix of research and production code — essential units should be production code.

**Review:** Conceptual for research code; detailed for production code.

**Deployment:** Limited deployment. Adding features requires medium-to-high effort. Users may need to troubleshoot.

**Testing:** See [[testing/testing-by-maturity]].

---

## General Availability (GA)

**Purpose:** Production product for wide use.

**Audience:** Large group of non-technical users relying on the product daily.

**Code quality:** Production code throughout.

**Review:** Detailed.

**Deployment:** Wide deployment. Adding features requires low-to-medium effort. Users should never need to troubleshoot.

**Testing:** See [[testing/testing-by-maturity]].

---

## Multi-Phase Projects

When a project progresses through PoC → MVP → GA:
- Code quality, testing, and review requirements increase at each phase
- There is typically a significant refactor at the MVP → GA transition
- By the GA phase, data science work is largely complete; the focus shifts to software engineering

---

## Source
- `../raw/DS Code Development Manual/Project Frameworks/Project Maturity Stages…md`

## Related
- [[testing/testing-by-maturity]]
- [[workflow/code-review]]
- [[workflow/pull-requests]]
