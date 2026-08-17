# Occam Review

`occam-review` is a Codex skill for reviewing implementation plans and code
changes after correctness review. It identifies unnecessary abstractions, dead
public surface, redundant validation, silent configuration fallbacks, and
compatibility layers without current consumers. It also rejects checksum work
without a real integrity consumer and separates temporary test probes from
long-lived product contracts.

## News

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
