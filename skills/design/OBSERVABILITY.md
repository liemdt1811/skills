# Observability by Design

Principles for ensuring modules are transparent, debuggable, and "production-ready" at the architectural level.

## The Principle: Deep Observability

A **Deep Module** should hide its implementation complexity but **expose its operational health**. Observability is not a "sidecar" added later; it is a primary concern of the interface design.

## 1. The Telemetry Port

Every deep module should have a way to emit telemetry (metrics, logs, traces) without depending on a specific infrastructure provider.

- **The Port**: The module defines a `Telemetry` interface (or a set of "Probes") within its own package.
- **The Dependency**: Telemetry is a **required dependency** of the module, injected at instantiation.
- **The Adapter**: The Imperative Shell (infrastructure layer) implements the interface and injects the concrete provider (e.g., Datadog, Prometheus, or a structured logger).
- **The Benefit**: The core logic remains pure and testable; the operations team gets the data they need without the core knowing how it's collected.

## 2. No Silent Failures

If a module cannot satisfy its contract, it must fail loudly and descriptively.

- **Descriptive Errors**: Errors must name the failing operation and the specific input that caused it.
- **Contextual Wrapping**: As errors move from the core to the shell, wrap them with context (e.g., `"failed to process order: <reason>"`).
- **Internal Health Probes**: For long-lived modules, provide a `Health()` check that verifies internal invariants or critical dependencies.

## 3. The Traceable Path

In asynchronous or distributed flows, ensure the module preserves and propagates **Correlation IDs**.

- Every entry point should accept a context/correlation carrier.
- Every internal log line should include the ID.
- This allows a single user request to be traced through multiple deep modules.

## 4. Performance Transparency

Expose the "Boring" metrics that matter:
- **Latency**: How long the deep implementation takes.
- **Throughput**: How many requests are being handled.
- **Error Rate**: Percentage of calls that return a failure.
- **Saturation**: How close the module is to its internal limits (e.g., buffer sizes, connection pools).

## Integration with Simplify

During the `simplify` pass, apply the **Telemetry Lens**:

- [ ] Does every error message name the failing input?
- [ ] Are there any "catch-all" blocks that swallow errors?
- [ ] Is there a Correlation ID being propagated if the flow is non-trivial?
- [ ] Could a stranger debug a failure in this code using *only* the logs it emits?
