# Occam Review

`occam-review` is a Codex skill for reviewing implementation plans and code
changes after correctness review. It identifies unnecessary abstractions, dead
public surface, redundant validation, silent configuration fallbacks, and
compatibility layers without current consumers. It also rejects checksum work
without a real integrity consumer and separates temporary test probes from
long-lived product contracts. It checks scope fidelity and removes semantic
residue from rejected features after a request is narrowed.

## What It Prevents

Occam Review is meant to catch recognizable implementation patterns such as:

- A request for one feature grows an adjacent feature that was never asked
  for. The review marks the extension for deletion unless an accepted
  requirement or real consumer needs it.
- An adjacent feature is removed after feedback, but names, comments, tests,
  docs, or change summaries keep saying `without X`, `no X`, or `excluding X`.
  The review removes that semantic residue and describes the requested behavior
  directly.
- A helper, wrapper, alias, field, or extension hook is added without a real
  consumer. The review deletes it or records the accepted requirement that
  justifies keeping it.
- Defensive checks, configuration fallbacks, local imports, or compatibility
  layers are added without a trust boundary, explicit optional state or
  documented product default, optional-dependency/import-cycle/material
  startup-cost reason required by the current feature, or current compatibility
  requirement. The review keeps the smallest supported reason and removes the
  rest.
- A checksum is added only to prove that a file changed, or an exploratory test
  is placed in a shared suite without a lasting contract. The review requires
  an integrity consumer and test owner, lifecycle, and retention class.

These examples describe review targets; they do not require every project to
have the corresponding feature or structure.

## News

### 0.1.2 - 2026-08-21

- Added scope-fidelity and semantic-residue checks for unrequested adjacent
  features and leftover `without X`-style naming, documentation, tests, and
  change summaries after scope corrections.
- Added examples documenting the unnecessary complexity and scope patterns
  this skill is designed to prevent.
- No API, MCP, hook, or compatibility impact.

### 0.1.1 - 2026-08-17

- Added a default-deny rule for SHA-256 and other checksums unless a protocol,
  security boundary, cache, reproducible-build requirement, or user request
  needs them.
- Added `temporary probe` and `long-lived contract` test classifications.
- Added test ownership, retention, and placement checks to prevent feature
  work from polluting shared test suites.

For future updates, add the newest version first and record the changed
principles, their user-visible effect, and any compatibility impact.

The repository distributes the skill as an instruction-only Codex plugin. It
does not install an MCP server, hooks, or executable scripts.

## Install

Add this repository as a plugin marketplace, then install the plugin:

```bash
codex plugin marketplace add mindorigin150/occam-review
codex plugin add occam-review@occam-review
```

Start a new Codex conversation after installation so the skill is available.

## Use

Invoke the skill explicitly:

```text
$occam-review
```

Codex can also select the skill when a request matches its description, such
as reviewing a plan, staged diff, migration, or refactor for unnecessary
complexity.

## Update

Refresh the marketplace and reinstall the current plugin version:

```bash
codex plugin marketplace upgrade occam-review
codex plugin add occam-review@occam-review
```

Start a new conversation after updating.

## Uninstall

```bash
codex plugin remove occam-review@occam-review
codex plugin marketplace remove occam-review
```

## License

MIT
