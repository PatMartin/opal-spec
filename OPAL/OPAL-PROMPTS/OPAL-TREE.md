# Requirement: `opal <module> tree` command tree renderer

Add a `tree` command to the `<module>` Typer app (`opal <module> tree`, also reachable via the
standalone `opal-<module>` alias). It prints the CLI's command/argument tree generated live from
`typer.main.get_command(<module>_app)` at call time — it must always reflect the installed commands,
never a hand-maintained diagram. Model the implementation on `opal_listener/cli/tree_cmds.py`.

> Align commands vertically, but display command arguments horizontally up to 80 characters before moving to the next vertical row.

## Output shape

Each command occupies exactly **one line**: the command name followed by its own parameters as
inline usage labels. Parameters are **never** separate child nodes. The root line is `<label>`
followed by the app's own callback options.

- Positional arguments: `name` when required, `[name]` when optional — no angle brackets.
- Options: the first long flag `--flag` when required, `[--flag]` when optional — no metavar,
  no default value.
- A flag with a secondary option (e.g. `--enabled/--disabled`) renders only the first long form.
- Command groups (nodes with subcommands) recurse one level deeper; leaf commands do not.
- Preserve click's natural ordering — declaration order for params, registration order for
  commands. Do **not** sort.

### Skipped items

- Hidden commands (`hidden=True`) and hidden params — not rendered.
- Params with `expose_value=False` (Typer's `--install-completion`/`--show-completion` plumbing).
- The `--help` option.

## Example (`--ascii` output)

```text
opal-<module> [--api-url] [--token] [--json] [--timeout] [--env-file] [--no-env-file]
|-- publish topic --data-json [--id] [--source] [--timestamp]
|-- tree [--ascii]
|-- rules
|   |-- list
|   |-- create [--json] [--file]
|   |-- get rule_id
|   |-- delete rule_id [--if-match]
|   `-- test rule_id --event-json
`-- service
    |-- start [--config] [--append]
    |-- stop
    `-- view
```

## Color scheme (`rich.tree.Tree` + `rich.console.Console`)

| Element | Style |
|---|---|
| Root label (`<label>`) | `bold green` |
| Guide lines (`├──`, `└──`, `│`) | `blue` (set `guide_style="blue"` on the tree and every `add()`) |
| Command groups | `bold cyan` |
| Leaf commands | `cyan` |
| Required params | `yellow` |
| Optional `[bracketed]` params | `dim` |

Build each label with `rich.text.Text`: create `Text(name, style=<name-style>)`, then
`text.append(f" {label}", style=<param-style>)` per usage label — param styles compose with the
command's own color (e.g. an optional param on a cyan leaf renders dim-cyan, matching opal-listen).

## Additional requirements

- **TTY detection:** colors only when stdout is a terminal — use `rich`'s own auto-detection
  (`Console()` default). Piped/redirected output emits zero ANSI escapes and keeps Unicode
  box-drawing guides on UTF-8 streams. Do not check `sys.stdout.isatty()` manually.
- **`--ascii` flag:** renders a pure-ASCII, color-free variant via `typer.echo` — guide
  connectors `` `-- `` (last child), `|-- ` (sibling), `|` (ancestor continuation), four-space
  indent. Every emitted byte must be ASCII (safe for logs and non-UTF-8 environments): keep help
  text free of non-ASCII characters.
- **`--ascii` itself** must appear in the tree, as the `tree` command's `[--ascii]` usage label.
- Structure the code as small helpers mirroring opal-listen: `_param_label` (returns
  `(text, style)` or `None` to skip), `_usage_labels`, `_visible_commands`, `_is_group`,
  `_styled_label`, `_plain_label`, `_add_commands`, a rich-tree builder, and an ASCII-lines
  builder shared by both renderers.

## Tests

- Default `tree` output: exit 0, first line starts with `<label>`, contains `├──`/`└──`/`│`
  guides, and contains **no** `\x1b[` (test runner is not a TTY).
- `tree --ascii`: no `──` chars, no ANSI escapes, output `.isascii()` is true, `|--` and `` `-- ``
  present.
- Coverage: a required argument appears unbracketed, `[--model]`-style optional options appear
  bracketed, `[--ascii]` appears under `tree`, `--help` and `*-completion` do not appear.
- Hidden click commands are excluded from both renderers (build a test `click.Group` with a
  `hidden=True` command and assert it is absent).