# AGENTS.md — OPAL-BUS

## Mission and scope

OPAL-BUS is the lightweight event transport component of OPAL, a local-first, modular system for ubiquitous computing and intelligence. Its job is to move structured events between independently useful components with minimal coupling and predictable resource usage.

**Boundary:** BUS owns event transport, subscriptions, connection lifecycle, and transport diagnostics. It does **not** own business rules, workflow orchestration, action execution, inference, media processing, or application-specific decisions. Keep rule evaluation and action execution in OPAL-EVENT or other explicit consumers. Do not expand BUS into a general message broker platform without approval.

This file expresses design constraints, not a replacement for the repository's actual contracts. When implementation, documentation, and this file disagree, report the discrepancy and request a decision rather than silently changing behavior.

## OPAL design tenets

1. **Boundary elasticity.** Components may be embedded, local, or remote without changing their fundamental contracts. Avoid mandatory network hops for in-process use.
2. **Independent utility.** BUS must remain useful and testable on its own; other OPAL services must not require BUS for their core operations unless their contract explicitly says otherwise.
3. **Additive composition.** Optional integration adds capabilities without making unrelated components dependent on BUS.
4. **Economic accessibility.** Favor lightweight Python implementations, commodity hardware, modest memory/CPU use, and no required cloud infrastructure.
5. **Progressive capability.** Start with simple, reliable behavior; add complexity only when a demonstrated use case warrants it.
6. **Interchangeable parts.** Depend on documented contracts and narrow adapters, not concrete implementations or service internals.
7. **Discover and use.** Expose documented capabilities and observable status; do not require callers to know internal implementation details.
8. **Extend the right surface.** When a capability performs an existing operation differently, extend parameters. When it has meaningfully different semantics, extend the API. Avoid duplicative endpoints and incompatible variants.

Additional guiding principle: **Be promiscuous at the edges; be principled at the core.** Support external integration through adapters while preserving a small, consistent internal event contract.

## Transport architecture

- Preserve the agreed ZeroMQ **XSUB/XPUB** broker architecture unless a change is explicitly approved.
- Keep the transport backend behind a narrow, implementation-neutral interface. Do not force all callers to import ZeroMQ-specific types.
- Support configured TCP transport; use in-process or same-host transports where appropriate and supported. Do not assume multicast or cross-subnet discovery.
- Treat PUB/SUB as **best-effort, non-durable broadcast**. Never claim guaranteed delivery, replay, ordering across publishers, or acknowledgments without a separate explicitly designed mechanism.
- A subscriber may miss messages while disconnected or joining. Document this plainly; do not hide it with misleading retry semantics.
- Do not turn broadcast events into a work queue. Job distribution, acknowledgments, retries, and durable delivery are different semantics and require a separately approved design.
- Preserve transparent forwarding: the broker must not silently rewrite topics, envelopes, or payloads.
- Keep raw audio, video, and large artifacts out of event messages; publish compact summaries and references instead.

## Event wire contract

- Follow the OPAL shared event contract and its versioned envelope rather than inventing a BUS-specific event format.
- For the agreed ZeroMQ wire format, use exactly **two frames**: topic and JSON envelope. The topic frame must match the envelope's `topic`.
- Preserve required identity, source, timestamp, and correlation/causation metadata as defined by the current normative contract.
- Use the established topic convention, such as `opal.<service>.<resource>.<event>`, with consistent names and documented versions.
- Respect the agreed **1 MiB maximum message size** unless the normative contract is explicitly revised.
- Reject malformed messages at defined ingress/validation boundaries; avoid silently modifying valid messages in transit.
- Changes to envelope shape, topic semantics, and wire framing require compatibility analysis and approval.

## Resource limits and failure behavior

- All internal queues, worker pools, buffers, and retained histories must have explicit finite bounds.
- Use the established **drop-newest** overload policy where the shared event standard applies; expose drop counters and reasons.
- Publication from other OPAL services is optional and **disabled by default**. Failure of best-effort publication must not fail the originating operation.
- Never block a producer indefinitely or allow unbounded retries, memory growth, or uncontrolled background tasks.
- Apply include/exclude filters and rate limits at their documented ownership boundaries; do not add hidden message filtering to the transparent broker.
- Shutdown must stop accepting new work, release sockets and tasks, and respect bounded drain/termination deadlines.
- Distinguish transport unavailability from failures in unrelated rule/action consumers.

## API, CLI, and configuration

- REST/OpenAPI is the management and configuration contract; the event transport is the data plane. Do not replace REST administration with ad hoc ZeroMQ control messages.
- If a CLI is provided, derive its hierarchy from OpenAPI resource paths and its semantic actions from `operationId`, following OPAL-API conventions.
- Do not add `x-opal-cli`, `x-opal-cli-format`, per-operation CLI mappings, or equivalent metadata systems.
- Preserve existing CLI names unless clearly inconsistent with universal conventions; request approval for breaking changes.
- Reuse shared OPAL kernel schemas and conventions for errors, IDs, health, settings, and configuration where applicable.
- Keep configuration explicit and portable. Do not log secrets or persist secret values when references suffice.
- Validate configuration before applying it. Make restart-required transport changes explicit rather than pretending they hot-reload safely.
- Keep management clients on the public contract; do not bypass the REST API to mutate service internals.

## Security and trust boundaries

- Bind management interfaces conservatively by default. Do not expose a broker or administration endpoint to untrusted networks accidentally.
- Respect configured authentication, authorization, and ZeroMQ CURVE transport controls when present; do not silently weaken security for convenience.
- Never execute code, shell commands, HTTP actions, or plugins as a side effect of merely forwarding a message.
- Treat all inbound topics, headers, and payloads as untrusted. Apply limits before expensive parsing or allocation.
- Avoid including credentials, sensitive content, or large media payloads in logs and diagnostics.

## Observability and diagnostics

- Expose meaningful readiness, liveness, connection status, queue depth, throughput, rejection/drop counts, and transport errors.
- Separate broker health from optional subscribers and downstream services; an unavailable consumer should not automatically make the broker unready.
- Use structured logs and shared OPAL request, event, transaction, and causation identifiers where available.
- Do not emit a new event for every broker-forwarded event: that creates amplification and feedback loops.
- Keep diagnostics lightweight and bounded.

## Engineering and compatibility discipline

- Prefer straightforward, readable Python with narrow interfaces and minimal dependencies.
- Do not introduce a registry, distributed coordinator, persistence layer, workflow engine, or additional service solely to solve a local implementation problem.
- Preserve public API paths, methods, schemas, `operationId` values, topic conventions, and wire behavior unless changes are explicitly approved.
- Prefer additive, backward-compatible changes. Identify migration implications for producers, consumers, SDKs, and CLIs.
- Keep implementation details behind adapters so unit tests can run without a live broker or network.
- Challenge unnecessary complexity and flawed assumptions constructively; do not agree with a proposed design merely because it was requested.
- If the repository's current scope differs from the transport-only boundary described here, **report the discrepancy before refactoring**.

## Testing requirements

- Validate the OpenAPI contract and all referenced shared kernel schemas when present.
- Test framing, topic/envelope agreement, schema validation, subscriptions, disconnect/reconnect behavior, startup/shutdown, and resource limits.
- Test malformed frames, oversize messages, slow subscribers, overload/drop policy, broker restart, and cancellation.
- Use deterministic in-process or loopback integration tests where practical; mark network-dependent tests explicitly.
- Do not assert reliable delivery or ordering guarantees that ZeroMQ PUB/SUB does not provide.
- Verify that disabled or failed optional publication cannot break originating service operations.
- Record exact test commands and actual pass/fail/skip counts; never claim tests were run unless they were.

## Agent workflow and reporting

For each substantial change:

1. State the problem, contract implications, and smallest proposed solution.
2. Identify affected files and interfaces before editing.
3. Preserve behavior outside the approved scope.
4. Run relevant tests and report commands, passed, failed, and skipped counts.
5. Estimate defect risk as a percentage and explain the estimate.
6. Provide independent verification steps.
7. Identify remaining limitations and possible testing improvements.
8. State what was completed and what should happen next.
9. Stop and ask for approval before breaking changes or scope expansion. For decisions, give options, complexity (1–10), tradeoffs, and a recommended choice with confidence percentage.

**Success criterion:** OPAL-BUS remains a small, observable, secure, bounded, interchangeable event transport that other OPAL components can use without inheriting their application logic.
