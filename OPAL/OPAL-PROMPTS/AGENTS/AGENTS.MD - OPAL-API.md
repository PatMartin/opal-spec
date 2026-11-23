# AGENTS.md — OPAL API

> **Mission:** Build small, independently useful, interoperable capabilities that can be discovered, composed, and replaced without requiring a central system.
>
> **Engineering discipline:** Explore without limits. Design with discipline. Implement with restraint. Validate with evidence.

This file governs work in `opal-api`. The repository defines OPAL's public contracts; implementations and user interfaces must conform to those contracts. Do not treat this file as permission to redesign an existing API without approval.

## 1. OPAL design tenets

1. **Boundary Elasticity.** A capability may run in-process, as a standalone service, or remotely without changing its fundamental contract. Do not couple API semantics to a deployment topology.
2. **Independent Utility.** Each component must provide useful functionality on its own. Do not require EVENT, THINK, a central registry, or other optional infrastructure for basic operations.
3. **Additive Composition.** Components should become more capable when combined, without making their standalone behavior dependent on that combination. Keep integration explicit and loosely coupled.
4. **Economic Accessibility.** Prefer local, commodity, affordable hardware and open interfaces. Do not require expensive cloud services, specialized accelerators, or subscriptions for core functionality.
5. **Progressive Capability.** Provide a useful minimal path first; add sophisticated capabilities incrementally. Advanced features should not make simple use cases complicated.
6. **Interchangeable Parts.** Standardize interfaces, not implementations. Compatible engines, models, runtimes, providers, and devices should be replaceable without rewriting consumers.
7. **Discover and Use.** **Discover what exists. Use what is available. Require nothing else.** Discover capabilities rather than assuming a fixed deployment; distinguish discovery from authorization.
8. **Extend the Right Surface.** **If a capability performs an existing operation differently, extend its parameters. If it performs something meaningfully different, extend the API.** Do not create duplicate operations for minor implementation differences.

## 2. Contract authority and ownership

- **OpenAPI 3.1 is the source of truth** for REST behavior and the basis for CLI and GUI clients.
- The shared kernel defines cross-service conventions; domain contracts define domain behavior. Avoid duplicating shared definitions or moving domain responsibilities into the kernel.
- Preserve consistent meaning, defaults, validation, errors, identifiers, and state transitions across REST, CLI, and GUI.
- Keep engines responsible for their own execution and engine-specific resources. Higher-level services compose and route; they must not silently take over engine responsibilities.
- Prefer focused contracts over a large universal interface that forces unrelated capabilities into one schema.
- Use explicit, stable terminology. Avoid overlapping verbs, duplicate endpoints, aliases without justification, and parameters with unclear meaning.

## 3. API design requirements

- Define operation purpose, inputs, outputs, defaults, errors, and side effects explicitly.
- Use consistent shared conventions for identifiers, correlation and causation, error envelopes, configuration, and concurrency controls where applicable.
- Use pagination where a collection is a history or otherwise requires it; do not add pagination mechanically to every endpoint.
- Prefer explicit opt-in for event emission, automation, discovery, or other behavior with external effects unless a contract expressly establishes a different default.
- Use bounded queues, timeouts, retries, and resource limits; avoid silent drops, unbounded work, and hidden fallback.
- Expose observable failures and useful diagnostic information. Preserve traceability across service boundaries.
- Do not equate visibility of a device, engine, or capability with permission to operate it.
- Make unsupported capabilities discoverable as unsupported rather than simulating success or silently substituting behavior.

## 4. CLI derivation from OpenAPI

- Derive the **command hierarchy from resource paths** and the **semantic action from `operationId`**. HTTP methods validate intent; they are not the primary naming source.
- Derive positional arguments, flags, requiredness, types, validation, and help from standard OpenAPI parameters, schemas, summaries, and descriptions.
- Resolve internal and external `$ref` references, including shared kernel Path Items.
- Do not introduce `x-opal-cli`, `x-opal-cli-format`, equivalent vendor extensions, per-operation command maps, or service-specific derivation tables.
- Every supported REST operation must produce exactly one unique canonical CLI command. Fail validation on ambiguity, collisions, or unsupported derivation rather than guessing or omitting an operation.
- Use generic structured JSON/file/stdin input when a request body cannot be cleanly expressed as flags.
- Keep CLI-only conveniences in reusable client code rather than embedding them in API contracts.
- Preserve existing CLI names unless clearly inconsistent with approved universal conventions. Assess aliases individually; do not preserve them by recreating a mapping system.

### Temporary migration exception

Existing `x-opal-cli` metadata may be retained as a **read-only compatibility baseline** during migration steps 1–8. Do not add new mappings. Remove the old metadata only after the generated CLI passes fidelity checks and the change is approved. Delete this exception after the migration is complete.

## 5. Compatibility and change control

- Do not change REST paths, methods, payload meaning, defaults, or response semantics solely to improve naming or implementation convenience.
- Treat changes to `operationId` as potentially breaking generated SDKs and other clients.
- Identify breaking changes explicitly and request approval before making them.
- Prefer existing OPAL conventions. If a convention is flawed, propose a specific improvement and explain the tradeoff; do not silently diverge.
- Present discretionary design changes as **numbered, independently selectable proposals**, with complexity (1–10), benefits, drawbacks, and a recommendation.
- Challenge unnecessary complexity, unsupported assumptions, and inconsistent designs directly and constructively. Agreement is not a substitute for analysis.
- Do not add compatibility shims, duplicate routes, or aliases without a demonstrated need and approval.

## 6. Simplicity and implementation discipline

- Optimize for understandable contracts and a small conceptual surface; simplicity is a high priority (approximately **8/10**).
- Prefer standard OpenAPI constructs, existing shared abstractions, and general rules over new metadata layers or one-off exceptions.
- Do not add infrastructure merely because it may be useful later. Start with the simplest independent capability and compose upward.
- Separate **Engine** (execution capability), **Plugin** (integration), **Model** (model identity/artifacts), **Runtime** (execution environment), and **Device** (hardware) when these concepts are relevant. Do not conflate them.
- Avoid central coordinators where optional peer discovery or independent services suffice.
- Keep changes scoped to the requested step. Report adjacent problems rather than expanding the implementation without approval.

## 7. Verification and evidence

- Validate modified OpenAPI documents and resolve all relevant references.
- Run applicable contract lint, schema validation, and regression tests; preserve service-specific and cross-service checks.
- Verify that shared kernel changes do not unintentionally alter other service contracts.
- For CLI-related work, test complete operation coverage, command uniqueness, argument binding, request serialization, help generation, and compatibility against the approved baseline.
- Test failure modes, unsupported capabilities, and bounded behavior—not only happy paths.
- Never claim code was executed, tests passed, or integration succeeded without evidence.
- Distinguish observed facts from assumptions and proposed behavior.

## 8. Required report after each implementation step

Report:

1. **Completed:** Changes made and affected files.
2. **Defect risk:** Estimated probability (%) of introducing a defect, with reasoning.
3. **Tests performed:** Exact commands, tests run, passed, failed, and skipped; say when tests were not run.
4. **Independent verification:** Exact commands the maintainer can execute.
5. **Testing improvements:** Remaining gaps and recommended coverage.
6. **Progress:** Current step and total steps, when working from a staged plan.
7. **Next:** Intended next step.
8. **Decisions needed:** Numbered choices with complexity (1–10), tradeoffs, recommendation, and confidence percentage.

**Stop at an approval boundary.** Do not proceed to the next migration step or make unapproved breaking changes.

## 9. Definition of done

A change is complete only when the intended contract behavior is explicit, affected references resolve, relevant validation and tests pass (or failures are clearly disclosed), compatibility implications are documented, and no unnecessary complexity or duplicated contract authority was introduced.
