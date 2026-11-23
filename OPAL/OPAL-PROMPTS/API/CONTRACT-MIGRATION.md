# Migrate one OPAL module to a revised OpenAPI contract

```text
Module checkout = <path to the implementation repository, or . for the current checkout>
Target contract = <path to the implementation repository>/openapi/*.yaml
```

Migrate the working module to the target contract. Execute Step 1 now. After each numbered step, give the report below and wait for me to say `continue`. If I request `continuous` mode, give the same reports and proceed automatically.

## Authority and OPAL principles

- The target OpenAPI file and its full `$ref` tree, including the shared OPAL kernel, define public behavior. Read schemas, normative descriptions and OPAL extensions together. Use the declared URL prefix even when `info.version` changes.
- Treat the selected source contract and upstream `opal-api` as read-only. Update the module’s pinned copy or generated bundle through its normal workflow. If two target requirements conflict, cite both locations, explain the observable consequence, and recommend the simplest resolution for my decision. Continue independent work. Identify impacts on sibling modules and keep edits in the selected checkout.
- Use OPAL’s clean-cutover policy. Implement authorized breaking changes, retire superseded interfaces, retain target-defined aliases, and give callers the replacement. Add a transition shim only if I explicitly request it. Protect existing user data and unrelated working-tree changes.
- Preserve standalone operation. Exercise the module with optional peers offline and event publishing disabled where supported. Reuse the module’s service logic and engine adapters. Keep REST, the Typer CLI and the existing SPA aligned where each exposes the capability. Follow the target’s `x-opal-cli` bindings and established mappings. Flag a missing mapping that would require choosing a new public command.
- Apply shared semantics where the target uses them, including `OpalError`, correlation IDs, session optionality, opt-in events, and write preconditions. Favor the simplest implementation that satisfies the user workflow. For a needed design proposal, extend parameters when an existing operation is performed differently and reserve new operations for meaningfully different capabilities. Bring proposals beyond the target to me for a decision.

## Step 1 — Establish the baseline and size the migration

1. Read the checkout’s instructions, test configuration and CI commands. Find the previous contract in the module’s pin, vendored files or Git history. Record paths, `info.version` and revisions for the old and target contracts and their references. Hash uncommitted inputs and use that target throughout. Resolve and validate it. If the old spec is unavailable, label the baseline as observed behavior and identify the gaps.
2. Before editing, run the full feasible regression suite with module coverage. Use the repository’s coverage configuration, or measure the actual source package with available development tooling. Record commands, counts, pre-existing failures and blocked checks.
3. Compare the resolved contracts and check the current implementation against the target. Account for additions, removals, renames, changed payloads, defaults, headers, errors and side effects. Include inherited kernel changes and pre-existing conformance gaps. Use a compact table with columns for **operation or workflow**, **old → target behavior**, **affected code and callers**, and **acceptance check**. Cite a method/path, operation ID, or schema pointer for each change. Group operations when they share the same change and check.
4. Show me a numbered plan with one observable acceptance criterion per step. Count this baseline as Step 1 and final verification as the last step. Choose the total from the actual work. If new evidence changes it later, explain the revision and retain completed step numbers.

## Implement and verify each subsequent step

- Migrate one testable workflow at a time through the affected models, service logic, persistence, HTTP handler and clients. Update affected examples and regenerate derived artifacts using the repository’s own process. When stored formats change, upgrade a previous-version fixture, reopen it, and verify both the data and recovery from a failed upgrade.
- **Run regression tests at every step.** Test through the public entry point against expectations taken from the target. Check success, failure and side effects. For example, if a stale write must be rejected, verify the response and the stored value. Rerun affected callers and previously migrated workflows, and run the full suite whenever feasible. Add a regression test when existing checks miss a specific risk. When the target supersedes an old assertion, cite the clause and test its replacement. Diagnose new failures, fix those introduced by the step, and rerun the affected checks before marking it complete.
- In the final step, rerun the full feasible suite and applicable CI checks. Reconcile the target operations with runtime evidence, verify retired interfaces and affected clients, and check the packaged and served contracts, where applicable, for semantic agreement with the target. Exercise a representative end-to-end workflow. Identify any required behavior that still lacks verification and report the migration as partial if an acceptance criterion remains open.

## Required report after every step, including baseline and final verification

Use the heading **Step {i} of {N} — {name}**.

1. **What I did.** State the observable result, important files and operations, and whether the acceptance criterion passed.
2. **Regression testing.** Give each command actually run and its test counts, including total, passed, failed, skipped, errors and expected failures where applicable. Identify baseline failures, new failures, and checks you could not run. Say which integration checks used substitutes and which exercised real components.
3. **Code coverage.** Report the actual module/package scope, covered/total executable statements and percentage, branch coverage when available, and coverage for touched files when measurable. Show the percentage-point change from the baseline or last comparable run. Label focused and full-suite measurements. Compare runs only when test scope and coverage settings match. If measurement is blocked, give the reason and the command needed to obtain it.
4. **Estimated defect risk.** Give one rounded percentage for the chance this step introduced at least one defect that escaped the checks. Label it as engineering judgment. Base it on the change, test evidence and remaining gaps. For a read-only baseline, report 0% introduced by this step. List any known defect explicitly.
5. **How I can test it.** Give copy-paste commands from the module root, any required setup or fixture, and the expected response, exit code, or state change. Include a meaningful failure case when applicable.
6. **Next step.** Name the next numbered step. At the final step, summarize caller migration actions, baseline-to-final coverage and remaining verification gaps.
