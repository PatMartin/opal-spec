# AGENTS.md — OPAL Look YOLO

## Mission

OPAL Look YOLO is OPAL's standalone, local-first Ultralytics YOLO inference adapter. It discovers host-installed engines, models, and available compute resources; performs bounded inference on images and caller-supplied frames; and returns predictable, engine-neutral results. It can run independently, in-process through embedded ASGI, or as a remote REST service used by OPAL Look or other clients.

This file sets **permanent engineering guardrails**, not a remediation backlog, migration plan, or request to reconcile contract versions. Follow the currently selected authoritative contract and report discrepancies without silently rewriting contracts or APIs.

## 1. The eight OPAL design tenets

1. **Boundary Elasticity.** Preserve the logical contract whether a capability runs in-process, as a standalone process, over a local socket, or across a network. Deployment boundaries must not alter semantics.
2. **Independent Utility.** Every component should deliver useful behavior alone. YOLO inference must not require OPAL Look, BUS, EVENT, THINK, DEVICE, or a central registry.
3. **Additive Composition.** Services compose through well-defined interfaces. Integrations add value without modifying or weakening the standalone adapter.
4. **Economic Accessibility.** Favor practical local inference and commodity hardware. CPU-only operation must remain viable; GPU acceleration is an optimization, not a prerequisite.
5. **Progressive Capability.** Begin with a dependable, simple prediction path; add model variants, acceleration, and richer capabilities without making basic use more complex.
6. **Interchangeable Parts.** Standardize public capabilities, request semantics, and results—not Ultralytics internals. Keep adapters and execution environments replaceable.
7. **Discover and Use.** *Discover what exists. Use what is available. Require nothing else.* Advertise supported tasks, formats, model capabilities, and hardware instead of assuming availability.
8. **Extend the Right Surface.** *If a capability performs an existing operation differently, extend its parameters. If it performs something meaningfully different, extend the API.* Do not multiply endpoints for minor execution variations.

## 2. Ownership and boundaries

### YOLO owns

- Discovery and reporting of locally available YOLO engines, model weights, task capabilities, and supported compute devices.
- Model selection, validation, load/unload and residency within the adapter's lifecycle.
- Synchronous, capacity-limited image and frame inference, including task-specific outputs and optional bounded native detail.
- Validation of inference options, source resolution, image decoding, and standardized errors.
- Its own health, settings, configuration snapshots, and standalone execution surfaces as defined by its contract.

### YOLO does not own

- RTSP/ONVIF discovery, camera capture, stream decoding, reconnect loops, frame scheduling, MJPEG, video playback, snapshots of live streams, or per-stream analytics. These belong to OPAL Look or a distinct capability.
- EVENT rules/action execution, BUS transport, THINK reasoning, device federation, or global service orchestration.
- Automatic model downloading, model-store administration, or deletion of user-owned model files unless a future contract explicitly introduces and approves those responsibilities.
- Model-specific assumptions that leak into the public REST contract without a portable representation.

**Rule:** Prefer a small adapter that OPAL Look can consume. Do not turn this repository into a second vision-streaming service.

## 3. Engines, models, and capability discovery

- Keep the engine protocol, engine registry, model repository, lifecycle manager, and prediction service separate from HTTP/CLI adapters.
- Do not hardcode one model name, YOLO release, accelerator, or output task as universally available.
- Discovery is read-only unless an explicitly defined operation says otherwise. Report installed versus loaded versus unavailable distinctly.
- Use stable, well-defined model identifiers; detect ambiguous names, duplicate weight stems, unsupported formats, and missing files. Do not silently choose an arbitrary file.
- Validate model/task compatibility before inference. Detect, classify, segment, pose, and oriented-box capabilities must be advertised truthfully; never return invented bounding boxes for non-detection outputs.
- Define which model/engine/device combinations are supported by capability data, not by brittle filename guesses where metadata is available.
- Keep engine-specific knobs in validated `engine_options`, with a published capability schema. Unknown or unsupported options fail clearly rather than being ignored.
- Preserve the distinction between **model discovery**, **model residency**, and **per-request execution**. Embedded CLI invocations may have ephemeral residency; persistent services may retain loaded models.
- Avoid model downloads, hidden first-run network access, or license assumptions. Document where weights originate and their separate usage/redistribution obligations.

## 4. Prediction contract and result fidelity

- Support the contract's standard image-source and frame-input paths without creating parallel prediction semantics.
- Preserve caller-supplied stream/frame IDs, timestamps, sequence numbers, and correlation metadata when the contract requires them. Never invent a source timestamp or imply a frame was captured by this adapter.
- Normalize detections consistently: class identity, label, confidence, and documented box coordinate system. Document image orientation and resizing transformations; ensure returned geometry refers to the promised image space.
- Keep task-specific results (classification, masks, keypoints, oriented boxes) distinct and schema-valid. Do not misrepresent unsupported result types as successful empty detection results.
- Keep native engine payloads optional, size-bounded, and clearly separated from the canonical response; clients must not need native payloads for portable use.
- Keep inference deterministic for the same model/options/input to the extent the runtime allows, and document sources of nondeterminism (hardware, precision, library versions).
- Do not silently clamp confidence thresholds, alter image dimensions, or change output precision without a contract-defined rule.
- Ensure malformed images, invalid options, missing models, wrong tasks, and unavailable hardware produce consistent OPAL errors rather than uncaught tracebacks.

## 5. Image and remote-source safety

- Treat image bytes, image paths, URLs, EXIF, frame metadata, and engine options as untrusted.
- Bound uploaded/source bytes, decoded pixel count, processing time, memory usage, and inference concurrency. Protect against decompression bombs and unbounded image allocations.
- Local paths resolve on the **execution host**, not automatically on the remote CLI host. Do not blur local file and remote upload semantics.
- Remote HTTP(S) image fetching is **disabled by default**. When enabled, enforce an explicit host allowlist, timeouts, response-size limits, content-type validation, and redirect restrictions. Prevent private-network/loopback access and DNS-rebinding/SSRF bypasses wherever remote fetching is supported.
- Never treat a client-controlled path as a model installation command or an unrestricted filesystem capability.
- Make source resolution failure safe and observable; never fall back to a different source without telling the caller.

## 6. Execution, resources, and failure behavior

- Keep CPU inference functional when supported, and make GPU selection explicit or capability-driven. Do not silently require CUDA or a specific GPU.
- Bound concurrent predictions with an explicit reject/wait policy matching the contract. Return `429` or the documented overload response instead of exhausting RAM/VRAM.
- Release inference slots, file handles, tensors, locks, and model references on all success, failure, cancellation, and timeout paths.
- Prevent unload while active requests still use a model; enforce safe model-loading concurrency and avoid duplicate concurrent loads.
- Separate health/liveness from model readiness; a responsive process with no usable model should not claim inference readiness.
- Report model-load failures and accelerator unavailability accurately. Never silently switch device, model, or inference task unless fallback is an explicit policy.
- Do not introduce background inference queues, durable job APIs, or streaming transport solely for convenience. They require a meaningful product and contract decision.

## 7. API, kernel, and interface discipline

- OpenAPI 3.1 is the authoritative public contract. Honor the shared OPAL kernel for common errors, identifiers, correlation, health, settings, profiles, personas, service operations, and configuration semantics as applicable.
- Use documented `/api/v1` paths, HTTP status codes, request/response schemas, security definitions, and `OpalError` envelopes. Do not hide public contract changes behind implementation details.
- Carry `X-Opal-Session-Id`, `X-Opal-Transaction-Id`, `X-Opal-Request-Id`, and `X-Opal-Causation-Id` according to kernel semantics; they are trace context, **not authorization**.
- CLI commands and the browser console should exercise the same API behavior as remote clients. In-process ASGI transport is acceptable; it must not bypass authorization, validation, or service semantics.
- Favor the approved path-derived CLI hierarchy and `operationId`-derived action naming. Preserve established command spellings unless clearly inconsistent with universal conventions.
- Do not add `x-opal-cli`, `x-opal-cli-format`, per-operation CLI mapping metadata, or service-specific command tables. Keep compatibility conveniences in reusable CLI code.
- Report CLI/OpenAPI drift, unknown operations, collisions, and contract-version differences explicitly. **Do not reconcile versions or rename operations without a separately requested task.**
- Keep documentation/examples aligned with behavior that is actually implemented. Mark proposed or experimental capabilities clearly.

## 8. Configuration, security, and deployment

- Follow the shared OPAL configuration save/load/validate and individual setting conventions where defined; configuration export must exclude secrets, cached inference data, and model binaries.
- Precedence for supported settings should remain explicit and testable: command-line overrides, environment, `.env`, then defaults, subject to the relevant contract.
- Do not embed credentials in source, logs, command output, sample configs, or model metadata. Use environment-based credential references and redact sensitive values.
- Default to loopback binding. Require authentication for non-loopback exposure and avoid insecure fallbacks.
- Maintain identical user-visible semantics across embedded ASGI and remote REST modes; document intentional differences in process lifetime and model residency.
- Avoid mandatory cloud services, network discovery, and heavyweight infrastructure for standalone prediction.
- Keep UI and CLI as clients of the public contract; do not create a second business-logic layer there.

## 9. Testing and verification

For changes to relevant areas, include focused tests and run the available broader suite. Prefer deterministic synthetic images and fake engines for most regression tests; reserve actual Ultralytics model execution for explicit opt-in integration tests with known weights.

Required coverage includes:

- OpenAPI validation and REST/CLI/embedded-ASGI parity, including negative/error cases.
- Model discovery, capability reporting, ambiguous IDs, load/unload, residency, and concurrent use.
- Detection geometry, confidence, labels, frame metadata, and task-specific schema fidelity.
- Option-schema validation, unknown option rejection, invalid images, oversized inputs, malformed frame envelopes, and protected source URLs.
- Source allowlisting, redirect handling, SSRF defenses, authentication, and secret redaction.
- Concurrency exhaustion, cancellation/error cleanup, device unavailability, and readiness behavior.
- CPU-only support and optional GPU-specific tests, with unavailable hardware reported as skipped rather than misrepresented as passing.

Standard local checks, where the repository's environment supports them:

```bash
python -m pytest
ruff check src tests
```

Never claim real model inference, a GPU run, remote integration, or full suite verification unless it actually happened. State any omitted checks and why.

## 10. Engineering discipline and compatibility

- **Inspect before editing.** Identify the precise owner of a behavior, existing contract, tests, and compatible public surfaces.
- **Challenge unnecessary complexity.** Recommend a simpler parameter or reusable component when a proposed abstraction duplicates existing functionality.
- **Make narrow changes.** Avoid unrelated refactors, dependency upgrades, contract-version reconciliation, and new services unless explicitly requested.
- **Preserve existing behavior by default.** Breaking API, CLI, output-format, model-ID, or configuration changes require a clear impact analysis and explicit approval.
- **Keep implementations replaceable.** Do not make HTTP handlers depend on Ultralytics internals that belong in the adapter.
- **Test before declaring success.** Report facts, not assumptions; characterize regressions honestly.
- **Leave the repository understandable.** Update relevant examples, tests, and interface documentation when behavior changes; avoid speculative roadmap promises.

## 11. Agent completion report

At the end of a substantive implementation step, report:

1. **Completed:** behavior changed and files touched.
2. **Defect risk:** a subjective percentage estimate of the chance the change introduced a defect, with a concise explanation; it is not a measured probability.
3. **Verification:** exact tests/commands run, number passed/failed/skipped, and tests not run. Never fabricate counts.
4. **Progress:** step N of M when following a numbered plan; otherwise describe scope completed.
5. **Next:** the next recommended step and any remaining limitations.
6. **Decision points:** ask only when necessary. Present tradeoffs, complexity (1–10), a recommendation, and confidence percentage if alternatives materially differ.

Do not commit, push, rename contracts, reconcile versions, or expand the task's scope unless explicitly requested.
