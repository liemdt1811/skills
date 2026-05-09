# Design Personas

When designing a new system or deepening a module, use these parallel sub-agent personas to explore the design space. Based on the principle of "Design It Twice" — your first idea is rarely your best.

These personas are used by `system-design`, `improve-codebase-architecture`, and `investigate`.

## The Personas

### 1. The Minimalist
- **Goal**: Minimize the interface surface area.
- **Strategy**: 1–3 entry points max. Hide everything else. If a feature can be accomplished by combining existing primitives, don't add a new one.
- **Metric**: High **Leverage** (Functionality / Interface size).

### 2. The Extensible (The Architect)
- **Goal**: Support many use cases and future growth.
- **Strategy**: Use hooks, providers, or plugin-style interfaces. Focus on the "Seam" where behavior can be altered without editing the module.
- **Metric**: High **Flexibility** (Ease of change without breaking the contract).

### 3. The Ergonomic (The Developer Advocate)
- **Goal**: Make the most common caller's life trivial.
- **Strategy**: Design for the "Happy Path." Provide high-level defaults and "Convention over Configuration."
- **Metric**: Low **Cognitive Load** (Time-to-first-successful-call).

### 4. The Hardened (The Security/Robustness Expert)
- **Goal**: Prevent abuse and ensure reliability.
- **Strategy**: Focus on "Illegal States Unrepresentable" and strict trust boundaries. Explicit timeouts, retries, and validation at every entry point.
- **Metric**: High **Resilience** (Failure-resistance and security posture).

### 5. The Observability-First (The SRE)
- **Goal**: Ensure the module's internal state is transparent.
- **Strategy**: Design with built-in telemetry, probes, and structured error propagation. No "Silent Failures."
- **Metric**: High **Debuggability** (Time-to-root-cause during incidents).

## Usage Pattern

When exploring an interface:

1. **Frame the problem space**: State the constraints and dependency categories.
2. **Dispatch Personas**: Assign 2-3 of these personas to separate "Parallel Brains" (or sub-agent invocations).
3. **Compare**: Contrast the results by **Depth**, **Locality**, and **Seam placement**.
4. **Hybridize**: Pick the strongest elements to form the final recommendation.
