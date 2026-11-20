# OPAL API Design Review

Review the OPAL APIs in scope and propose changes that make them consistent, clear, concise, and well designed. Give me a numbered list of proposals I can approve individually. This run ends with the proposals. Implementation requires explicit approval.

## Review the existing contracts

- Inspect the OpenAPI specifications, documented conventions, and relevant implementation and tests. Include REST, events, CLI, and web UI wherever they expose the same capability.
- Use established OPAL conventions as the baseline. Check naming, resource and operation shapes, defaults, error handling, identifiers, pagination where applicable, and event semantics. Across interfaces, check that the same operation has the same meaning, defaults, outcomes, and meaningful errors. Let each interface present those in its own natural form.
- When written standards, specifications, or implementations disagree, report what each source says or does, cite the affected files or operations, and identify the decision I need to make. You may recommend a resolution. Mark it as pending reconciliation.
- If an existing convention could be improved, make that its own proposal. Explain the concrete benefit and what adopting it across OPAL would cost.

## Apply the design priorities

- Treat simplicity as an 8 out of 10 priority. Favor designs that require fewer decisions from callers, use predictable defaults, and are easy to explain with a realistic example. Challenge duplicate operations, aliases, and overlapping options when they add little value.
- Apply the OPAL design tenet. If a capability performs an existing operation differently, extend its parameters. If it performs a meaningfully different operation, extend the API.
- Make the contract explicit wherever a caller must rely on it. Cover relevant inputs, defaults, results, errors, and state changes. Use the same term for the same concept throughout the contract.
- Prefer changes that preserve existing client behavior. For a breaking proposal, identify exactly what breaks, who is affected, and how clients can migrate. Mark the proposal **Breaking — approval required** and wait for approval before implementation.

## Return a numbered list I can cherry-pick

Put the highest-impact proposals first. Give each item a stable number and keep it independently approvable. List dependencies and mutually exclusive proposals by number.

For each item, include:

- **Current behavior and evidence** — What happens today, where to verify it, and why it matters to a caller.
- **Proposed contract** — The specific change and its effect on each relevant interface. Include a representative request, response, and error where applicable.
- **Benefits and trade-offs** — What gets clearer or simpler, what complexity remains, and any effect on existing callers.
- **Verification** — Use the available OpenAPI validator for proposed OpenAPI changes. Report what you validated, the result, and any remaining checks.

Label unresolved conventions **Reconciliation needed**. End by asking which proposal numbers I want to approve or revise.