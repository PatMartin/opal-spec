# Create or Improve an OPAL README

Inspect this repository and create a consistent, succinct, informative, and intuitive README.md for this OPAL module.

The README should primarily help someone who wants to **use the module**. It should provide a practical guide to getting started and accomplishing common tasks, with links to deeper documentation.

## Before Writing

1. Inspect the existing README, project assets, source code, CLI help, packaging, configuration, tests, and documentation.
2. If a README exists, ask whether I want you to:
   - **Integrate** these improvements into the existing README, preserving its useful structure and content.
   - **Rewrite** it using the structure below, preserving accurate, useful information.
3. Identify the graphic representing this project.
   - If confidently identified, place it at the top with descriptive alt text.
   - If uncertain which graphic to use, ask before proceeding.
   - If none exists, omit it. Do not create or substitute a graphic without asking.
4. Ask clarifying questions about consequential missing or contradictory information. Continue independent work while awaiting answers.
5. Do not invent commands, capabilities, defaults, installation steps, URLs, or configuration precedence.

## Writing Principles

- Lead with vision, then quickly establish this module’s concrete value.
- Use plain language, short paragraphs, and descriptive headings.
- Be useful and engaging without marketing hype.
- Keep usage examples **CLI-only**. Link to Python and REST documentation where relevant.
- Demonstrate standalone value before suite integrations.
- Use content budgets as ceilings, not quotas. Omit unnecessary sections.
- Keep detailed reference material in linked documentation.
- Describe current capabilities accurately and label uncertainty explicitly.
- Do not promise future stability, compatibility, delivery dates, or milestone outcomes.

## README Structure

### 1. Project Graphic

Place the confirmed project graphic at the top, when available.

Use a repository-relative path for a repository asset where practical. Include descriptive alt text and keep its displayed size reasonable.

### 2. Overview

Start with one sentence about OPAL’s broader vision: computing and intelligence that can perceive, respond, and help through natural interaction.

Follow with a short paragraph explaining:

- What this module does.
- Who benefits from it.
- What useful outcomes it enables.
- How it works independently and fits into OPAL.

Make the module’s particular value clear. Avoid a generic introduction that could describe any OPAL module.

### 3. Development Status

Include this prominent note:

> **Work in progress.** I’m actively building OPAL and will break things often. Expect breaking changes to commands, configuration, and interfaces. No stability or backward-compatibility guarantees are made.

Briefly mention any additional material limitations supported by repository evidence. Link to details when available.

### 4. Compact Table of Contents

Use a compact, wrapping line of Markdown links separated by middle dots. Do not use a tall vertical list or a navigation table.

Example:

**Contents:** [Quickstart](#quickstart) · [Core concepts](#core-concepts) · [Common tasks](#common-tasks) · [Configuration](#configuration) · [Troubleshooting](#troubleshooting) · [Documentation](#further-documentation) · [Contributing](#contributing-and-license)

- Include only relevant sections actually present.
- Use short labels.
- Verify every anchor against the final headings.
- Avoid listing individual task examples unless essential.

### 5. What You Can Do

Provide two or three short, compelling previews of useful outcomes.

- Emphasize standalone capabilities.
- Clearly identify any outcome requiring other OPAL modules.
- Link each preview to the corresponding example or scenario in Common Tasks.
- Describe the benefit here; leave the instructions for Common Tasks.
- Prefer available capabilities for these opening examples.

### 6. Quickstart

Provide one recommended installation path and the shortest path to a useful result.

Include:

1. Essential prerequisites.
2. Installation commands.
3. Required initial configuration, if any.
4. A useful first CLI command.
5. Expected output or another clear indication of success.
6. Cleanup or stopping instructions, if the example starts a persistent process.

Link to alternative installation methods rather than documenting them all here.

Commands should work in the documented sequence, with working directories and placeholders clearly explained.

Verify the quickstart whenever possible. If verification cannot be completed, prominently label the entire section **Unverified** and explain exactly what was not checked.

Do not present planned functionality as a working quickstart.

### 7. Core Concepts

Explain only the concepts needed to understand subsequent usage.

- Include no more than five short explanations.
- Define unfamiliar OPAL terms where needed.
- Omit this section if the examples are understandable without it.

### 8. Common Tasks

Organize this section into two parts.

#### With This Module

Provide three to five useful standalone CLI examples, or fewer if appropriate.

Each example should explain:

- **Outcome:** What the user will accomplish.
- **Requirements:** Necessary inputs, dependencies, or setup.
- **Command:** A concise, usable CLI example.
- **Result:** What success looks like.

Avoid repeating installation instructions or reproducing the full CLI reference.

#### With the OPAL Suite

Provide two or three brief integration scenarios, or fewer if appropriate.

For each scenario:

- Describe the useful outcome.
- Identify the additional OPAL modules required.
- Explain this module’s role.
- Link to an existing complete integration guide.

Keep these scenarios brief; do not embed full multi-module tutorials.

If no guide exists, explicitly note that the guide is not yet available. Do not fabricate links.

### 9. Configuration

Document the settings most users need.

Where helpful, use a compact table with:

- Setting.
- Purpose.
- Default.
- How to configure it.

Explain actual configuration precedence, including CLI arguments, environment variables, `.env` files, and configuration files only where supported.

Derive defaults and precedence from implementation evidence. Do not assume consistency with other OPAL modules.

Link to the complete configuration reference when available.

### 10. Troubleshooting

Include three to five real, likely problems, or fewer if supported by evidence.

Pair each symptom with:

- Its likely cause.
- A specific diagnostic command or corrective action.

Avoid generic filler and unsupported diagnoses.

### 11. Further Documentation

Provide a small set of descriptive links to available resources, such as:

- Complete CLI reference.
- Advanced usage.
- Suite integration guides.
- Configuration reference.
- Architecture.
- Python and REST API documentation.
- Roadmap or planned capabilities.

Do not include nonexistent destinations.

### 12. Contributing and License

Briefly explain how to contribute and identify the actual license.

Link to existing contribution guidance and license files. If licensing is missing or unclear, ask rather than infer.

## Visual Guidance

Use visuals whenever they materially improve understanding, never merely for decoration.

Choose the format that best supports the explanation:

| Format | Appropriate use |
|---|---|
| Screenshot or sample output | Show what success looks like. |
| Flowchart | Explain meaningful decisions or branching workflows. |
| Architecture diagram | Clarify module boundaries and relationships. |
| Sequence diagram | Explain interactions where order matters. |
| Table | Compare options, settings, or capabilities. |
| Chart | Present meaningful measurements with conditions and sources. |

- Place visuals beside the explanation they support.
- Prefer Mermaid for suitable diagrams.
- Keep diagrams compact and labels clear.
- Avoid diagrams that merely repeat a simple sentence.
- Never invent measurements or present illustrative output as captured output.
- Do not add a mandatory visuals section.

## Capability and Verification Labels

Clearly distinguish these states where relevant:

- **Verified:** Successfully exercised under stated conditions.
- **Unverified:** The capability exists, but the example has not been successfully tested.
- **Planned:** Not implemented; any command shown is proposed syntax.

Unverified and planned material may be included when useful, but must not look like dependable current instructions.

Prefer roadmap links for extensive planned material.

## Review Checklist

Before finishing, confirm:

### Reader Experience

- [ ] The opening conveys OPAL’s vision and this module’s specific benefit.
- [ ] Development status is prominent and makes no stability promises.
- [ ] Navigation is compact and uses horizontal space effectively.
- [ ] Opening task previews link to corresponding Common Tasks.
- [ ] Quickstart ends in a recognizable useful result.
- [ ] Standalone usage precedes suite integrations.
- [ ] Usage examples are CLI-only.
- [ ] Sections remain within the content budgets without filler.
- [ ] Visuals clarify something meaningful.

### Accuracy

- [ ] Commands and options match the current CLI.
- [ ] Prerequisites and required setup are explicit.
- [ ] Configuration defaults and precedence match the implementation.
- [ ] Current, unverified, and planned capabilities are distinguishable.
- [ ] Expected output is accurate or clearly labeled illustrative.
- [ ] Suite dependencies are identified.
- [ ] No commands, capabilities, measurements, or links were invented.

### Validation

- [ ] Quickstart was exercised where feasible.
- [ ] Any unverified quickstart is prominently labeled with specific gaps.
- [ ] Example commands were checked to the extent possible.
- [ ] Internal anchors and repository-relative links resolve.
- [ ] External links were checked where access permits.
- [ ] The project graphic resolves and has descriptive alt text.
- [ ] Markdown and Mermaid syntax were checked.
- [ ] Useful existing information was preserved.
- [ ] Unresolved consequential questions were raised.

## Completion Report

After creating or updating README.md, briefly report:

1. What changed.
2. What was verified and how.
3. What remains unverified, planned, or blocked.
4. Any decisions still needed from me.

Do not claim checks or successful execution that did not occur.