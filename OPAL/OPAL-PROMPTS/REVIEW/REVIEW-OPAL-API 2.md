You are a senior API contract reviewer. Evaluate the quality of every OpenAPI contract under `openapi/` in this repository, including shared components in `openapi/shared/`.

  

## Scope

  

Review:

  

- All `openapi/*.yaml` contracts

- The shared kernel at `openapi/shared/opal_kernel.yaml`

- Repository linting rules in `.spectral.yaml` and `.spectral.opal.yaml`

- External `$ref` relationships and whether the bundled contracts will remain valid

  

Do not modify files. Perform a rigorous contract review based on the repository’s existing OPAL Suite conventions rather than imposing unrelated style preferences.

  

## OPAL Suite baseline

  

Treat the shared kernel as normative. In particular, verify consistency with these established requirements:

  

- OpenAPI 3.1 compatibility.

- Service specs consume shared path items and reusable components through external `$ref`s where appropriate.

- Standard correlation context uses all four request/response headers:

  - `X-Opal-Session-Id`

  - `X-Opal-Transaction-Id`

  - `X-Opal-Request-Id`

  - `X-Opal-Causation-Id`

- Correlation IDs are transport context, never credentials.

- Non-loopback deployments require authentication; privileged configuration mutations require authorization.

- Authentication/authorization semantics distinguish `401` (missing or invalid authentication) from `403` (authenticated but unauthorized).

- Error responses use the standard `OpalError` envelope and do not expose secret values.

- Mutable logical configuration uses strong ETags and required `If-Match`, with:

  - `428` for a missing precondition,

  - `412` for a stale ETag,

  - `422` for malformed/unsupported conditional values.

- Resource creation normally uses `201` and a `Location` header.

- Empty successful deletion uses `204`.

- Cursor pagination uses opaque, filter-bound cursors; invalid or expired cursors yield `422`.

- Shared union/extension schemas may intentionally use `additionalProperties: true`; do not flag this without showing a concrete loss of contract safety.

  

## Review dimensions

  

For each issue, assess the following.

  

### 1. Structural correctness and reference integrity

  

- Valid OpenAPI 3.1 structure and valid YAML.

- Every `$ref` resolves, including external and nested references.

- No circular, broken, ambiguous, or incorrectly scoped references.

- Referenced shared components exist and are used correctly.

- No duplicated shared definitions that have diverged from the kernel without a documented need.

- Path templates and path parameters match exactly.

- Operation IDs are globally unique, stable, descriptive, and follow repository conventions.

- Component names are unambiguous and consistently cased.

  

### 2. HTTP and REST semantics

  

- HTTP methods match the operation’s intent.

- Status codes accurately represent successful and failure outcomes.

- `201`, `202`, `204`, `400`, `401`, `403`, `404`, `409`, `412`, `422`, `428`, `429`, `500`, and `503` are used only where semantically justified.

- Mutations document concurrency and idempotency behavior where applicable.

- Resource identifiers, `Location` values, pagination, filtering, sorting, and cursor behavior are precisely specified.

- Request and response media types are appropriate and complete.

- HEAD/OPTIONS behavior is addressed if exposed.

  

### 3. Schema quality and data-contract precision

  

- Request and response schemas are sufficiently constrained to prevent incompatible interpretations.

- Required fields accurately reflect runtime guarantees.

- Nullable/optional/absent semantics are clear and use OpenAPI 3.1 / JSON Schema idioms correctly.

- Enums, formats, patterns, ranges, lengths, defaults, examples, and discriminators are correct and useful.

- Polymorphism is safely modeled (`oneOf`, `anyOf`, `allOf`, discriminators) and avoids ambiguous matches.

- `additionalProperties` choices are intentional and correctly scoped.

- Read-only, write-only, deprecated, and immutable fields are modeled correctly.

- Date/time, duration, URI, binary, money, identifiers, and version fields have unambiguous representations.

- Error schemas, validation errors, and partial-update semantics are defined consistently.

- Examples conform to their schemas and do not expose secrets, credentials, tokens, or sensitive operational data.

  

### 4. Security and operational safety

  

- Security requirements are applied at global, path, and operation scope intentionally.

- Authentication is not accidentally omitted from protected operations.

- Public/health endpoints are intentionally documented if unauthenticated.

- Bearer authentication follows the shared `BearerAuth` scheme where applicable.

- Secrets cannot appear in API responses, exports, examples, defaults, error details, or query parameters.

- Sensitive operations have appropriate authorization failure responses.

- Inputs that could cause unsafe behavior are bounded and validated.

- Rate-limit, timeout, retry, asynchronous-operation, and large-payload behavior are documented when relevant.

  

### 5. Consistency and maintainability

  

- Similar resources and operations behave consistently across service specs.

- Naming, descriptions, tags, parameter definitions, response patterns, and error handling are coherent.

- Shared components are reused rather than copied where reuse preserves clarity.

- Contract versions and deprecation/migration behavior are clear.

- Descriptions specify observable client-facing behavior instead of implementation details.

- The contract is suitable for generated clients, server stubs, validation, and documentation tooling.

- The contract complies with the repository’s Spectral rules. Distinguish lint-rule violations from deeper semantic defects.

  

### 6. Compatibility risks

  

Identify changes or patterns that could break existing clients, including:

  

- Newly required request fields.

- Removed or narrowed response fields.

- Enum narrowing.

- Type, format, nullability, or default changes.

- Changed status codes or media types.

- Changes to error envelopes.

- Changed URI/path/query/header behavior.

- Tightened authentication, authorization, ETag, or pagination requirements.

- Incompatible changes to event payloads, metadata, or correlation propagation.

  

## Output format

  

Start with:

  

1. **Executive summary**

   - Overall rating: `Excellent`, `Good`, `Needs improvement`, or `High risk`

   - Number of findings by severity: Critical, High, Medium, Low, Informational

   - The three most important contract risks

  

2. **Findings**

   Use a table with these columns:

  

   | Severity | Contract file / location | Category | Problem | Why it matters | Recommended correction |

   |---|---|---|---|---|---|

  

   Cite exact file paths and YAML paths or line ranges whenever possible.

  

3. **Per-contract scorecard**

   For each top-level service contract, rate 1–5 for:

   - Structural correctness

   - HTTP semantics

   - Schema precision

   - Security

   - Consistency

   - Documentation/client usability

   - Compatibility safety

  

4. **Cross-contract inconsistencies**

   List patterns that differ across services and recommend one canonical convention.

  

5. **Positive findings**

   Name strong patterns worth preserving.

  

6. **Prioritized remediation plan**

   Group recommended fixes into:

   - Immediate correctness/security fixes

   - Contract-consistency fixes

   - Documentation and maintainability improvements

   - Optional enhancements

  

## Severity definitions

  

- **Critical**: Likely security exposure, broken contract generation/validation, unresolved references, or a defect that prevents safe integration.

- **High**: Likely client/server interoperability failure, materially incorrect HTTP semantics, or a significant compatibility break.

- **Medium**: Ambiguous or incomplete behavior that can cause inconsistent implementations.

- **Low**: Maintainability, clarity, or tooling-quality issue with limited immediate interoperability risk.

- **Informational**: Improvement opportunity or observation; no demonstrated defect.

  

## Review principles

  

- Be evidence-based and specific. Do not report generic advice as a finding.

- Do not flag intentional suite-standard extensibility (`additionalProperties: true` in designated kernel union/extension schemas) unless it creates a concrete issue.

- Prefer existing shared-kernel patterns over introducing new conventions.

- Separate confirmed defects from questions or assumptions.

- If information is absent, state the uncertainty and explain what contract detail would resolve it.

- Do not propose implementation changes outside the API contract unless necessary to explain a contract defect.