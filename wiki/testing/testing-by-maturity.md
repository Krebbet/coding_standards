# Testing Expectations by Project Maturity

Formal test coverage requirements scale with project maturity and deployment risk. These thresholds apply to *formal* tests; see [[testing/testing-philosophy]] for the universal expectation to test during development.

## Quick Reference

| Stage | Unit Tests | Integration Tests | E2E Tests | CI Gate |
|---|---|---|---|---|
| PoC | Not required | Encouraged | Required before delivery | Run all available tests |
| MVP | Essential units | Interface code | Required before delivery | All tests must pass |
| GA | 100%* coverage | All internal/external interfaces | Full user workflows | >70% overall, 100% critical |

*100% of units that can be meaningfully tested.

---

## Proof of Concept (PoC)

**Timeline:** Up to 4 weeks. **Code quality:** Research code.

**Unit tests:** Not required, but trivial functions are cheap to test — do it anyway.

**Integration tests:** Highly encouraged. They catch pipeline breaks, reduce handoff cost to the next phase, and make PoC→MVP transitions faster.

**E2E tests:** Required before client delivery. Keep scope high-level: run through the same motions the client will.

**CI:** GitHub Actions runs all available tests. If tests exist, they must pass. No specific coverage threshold.

---

## Minimum Viable Product (MVP)

**Timeline:** 4–12 weeks. **Code quality:** Mix of research and production code.

**Unit tests:** Required for essential units (the key processing steps of the algorithm). When a bug is found, write a regression test immediately.

**Integration tests:** Required for code that interfaces with users or other modules.

**E2E tests:** Required before delivery. Scope includes all instructions, configurations, data sources, and project dependencies.

**CI:** PRs approved only when all tests pass. Reviewer uses coverage statistics to assess completeness on critical code. No hard coverage percentage, but all critical functionality must be covered.

---

## General Availability (GA)

**Timeline:** More than 12 weeks. **Code quality:** Production code.

**Unit tests:** 100% coverage of all units that can be meaningfully tested.

**Integration tests:** All code that interfaces with internal and external modules or resources.

**E2E tests:** All common user/client workflows, UI assessment, latency, and external resource connectivity.

**CI:** All tests must pass for PRs. Overall coverage should be >70%. Tools: pytest, bandit (security), flake8 (PEP8), tox (multi-env), mock, coverage.

---

## Multi-Phase Projects

Projects moving through PoC → MVP → GA:
- Code quality and testing requirements increase at each phase — tracking the increase in risk
- There is typically a large code refactor when moving from MVP to GA
- The GA phase is primarily software engineering; data science work is complete by then

---

## Source
- `../raw/DS Code Development Manual/Code Testing/Unit Testing & Integration Testing/Testing Expectations by Project Maturity…md`
- `../raw/DS Code Development Manual/Project Frameworks/Project Maturity Stages…md`

## Related
- [[testing/testing-philosophy]]
- [[testing/writing-good-tests]]
- [[workflow/project-maturity-stages]]
