# Why verify-real-deps exists — a worked example

A motivating example from a real session: a proxy for a third-party AI API shipped **25 / 25 acceptance criteria green**, lint clean, prod-ready clean. First end-to-end run against the live upstream surfaced **eight bugs** across four follow-up sessions:

- Body shape was `{models: [...]}`; the real API takes `{project: "..."}`.
- Tier enum had 3 values; the real API returns more.
- Cache wasn't populated at credential-add time → first request returned 503.
- Response body had `"models/X"` prefix; cache stored bare names → cache miss.
- Non-streaming responses bypassed the SSE parser → all-zero token counts.
- *(Three more in the same class — wire-shape mismatches the fake accepted but the upstream rejected.)*

Every one of those passed unit + integration tests because the fake harness accepted whatever the code sent. None survived contact with the real API.

## The lesson, generalized

A fake server is a *hypothesis* about the contract; the real upstream **is** the contract. Until you run code against the real upstream at least once, every test that uses the fake is testing your hypothesis — not the contract.

Whatever the domain — payments, AI inference, search, geolocation, telephony, OAuth — the same class of wire-shape bug exists. `verify-real-deps` is the discipline that finds it before users do.
