# Bootstrap mode — the canonical rules for greenfield repos

This document is the **single source of truth** for how greenfield repos onboard their vocabulary and decision record. `feature-doc`, `system-design`, and `improve-codebase-architecture` all defer here when their inputs would normally come from `CONTEXT.md` / `docs/adr/` but those don't exist yet.

## The rules

1. **No file is required to pre-exist.** [`docs/CONTEXT.md`](../../docs/CONTEXT.md), [`docs/adr/`](../../docs/adr/), and [`docs/CONTEXT-MAP.md`](../../docs/CONTEXT-MAP.md) are all created **lazily** — only when the first term is resolved or the first ADR is needed.
2. **Resolve 3–7 core domain terms first** for the plan being discussed. Not exhaustive — the terms most load-bearing for the decisions in scope. Format per [`../formats/CONTEXT-FORMAT.md`](../formats/CONTEXT-FORMAT.md).
3. **Capture ADRs only when all three criteria hold** (hard-to-reverse / surprising / real trade-off). Format per [`../formats/ADR-FORMAT.md`](../formats/ADR-FORMAT.md).
4. **Grilling is shorter in bootstrap mode** — there's less existing model to stress-test against. Output is fewer challenges, more *terminology and decision capture*.
5. **If nothing remains to grill** after bootstrapping (plan is fully exploratory), stop and hand off to [`investigate`](../investigate/SKILL.md) — `grill-plan` is for stress-testing, not exploring.

Other skills should link here rather than re-explaining bootstrap.
