---
name: occam-review
description: Audit implementation plans, code changes, staged diffs, and refactors for unnecessary abstractions, dead public or wire surface, redundant defensive validation, silent configuration fallbacks, unjustified local imports, missing responsibility documentation, compatibility aliases without real consumers, and tests that manufacture demand. Use before implementation to constrain the design and before staging, committing, or completing a code review to run a deletion-focused simplicity and readability pass in addition to correctness review.
---

# Occam Review

Apply a separate necessity test after correctness: if an entity can be removed
without losing a current required behavior, remove it.

## Design Gate

Before implementation:

1. State the current required behaviors and trust boundaries.
2. Name the current consumer for every proposed public type, method, field,
   extension hook, compatibility alias, and transport feature.
3. Prefer direct code until a second real implementation or an existing
   cross-cutting invariant makes an abstraction cheaper.
4. Do not treat “might be useful later” or a new test as a consumer.
5. Treat a migration as ownership transfer: update callers and delete the old
   module, import path, wrapper, alias, and re-export. Never infer a backward-
   compatibility requirement merely because the old API existed; retain a shim
   only when the user explicitly requires a compatibility period.

Keep the design no larger than the current end-to-end feature. Account for
already-planned modules in the same implementation, not only the first staged
slice.

An explicitly accepted requirement or architecture decision is current demand
even before its first production caller lands. Record that decision as the
consumer and judge whether each member is necessary to satisfy it. Do not
silently replace product intent with repository call-counting.

## Pre-Stage Gate

Review the index and the complete implementation context:

1. Read `git diff --cached` (or the intended diff before staging).
2. Enumerate every new public method and dataclass or wire field, then record
   its producers and non-test consumers. Do not sample only representative
   fields.
3. Search the whole worktree for every new public symbol and protocol field.
4. Identify:
   - fields written but never read;
   - APIs used only by their own tests;
   - migration shims, legacy re-exports, duplicate names, or aliases not
     explicitly required by the user;
   - wrappers that only return another public value;
   - parallel implementations that can inherit or share existing code;
   - branches for implementations that do not exist;
   - adjacent layers validating the same trusted value;
   - function-local or repeated imports without an optional-dependency,
     import-cycle, or material startup-cost reason;
   - public classes with no concise responsibility docstring;
   - comments that narrate syntax instead of explaining a decision or invariant.
5. Check unstaged modules before calling an interface speculative.
6. Treat an explicitly approved requirement or architectural boundary as
   current demand. Check each method and field against the behavior that the
   decision requires; do not demand an existing call site when the accepted
   contract itself needs the surface.
7. When the read volume is large, use a fresh-context read-only explorer and
   require exact file:line evidence.

Unresolved high-value simplicity findings block staging unless a current
consumer, accepted requirement, architectural decision, or invariant is
documented.

## Validation Rule

Validate once at each trust boundary:

- JSON or other deserialization;
- subprocess stdin/stdout and service responses;
- filesystem paths, symlinks, and generated artifacts;
- user or plugin input;
- external model adapters.

Inside trusted typed code, treat annotations and construction paths as the
contract. Do not pre-check primitive types, signs, or emptiness merely to
replace an immediate natural failure with a friendlier exception. For example,
do not reject a negative timeout before an operation that will immediately
time out anyway.

Keep an internal semantic check only when its absence could silently accept an
illegal state, corrupt data, leak resources, violate ownership, or cross a
security boundary. Uniqueness, legal state combinations, bounded resource
ownership, and path containment are typical reasons to keep a check.

Python annotations do not validate external data. Never remove boundary checks
merely because a signature says `int`, `str`, or `Mapping`.

## Configuration Rule

Fail loudly when required configuration is absent, misspelled, or malformed.
Read required mapping fields with `config["field"]` and required environment
variables with `os.environ["NAME"]`; never hide their absence with `.get()`,
`getenv()`, `setdefault()`, `or default`, exception-based fallback, or
candidate-path probing.

Represent optional configuration as an explicit optional state rather than an
implicit default. If the product explicitly requires a default, materialize
and report it in one authoritative configuration-resolution layer; downstream
consumers must still read the resolved field as required. During review,
classify every fallback-shaped configuration read as `delete`, `explicit
optional state`, or `documented product default`.

## Import and Documentation Rule

Put normal required dependencies at module scope. Use a function-local import
only for an optional dependency, a real import cycle, or a material startup
cost that the current feature is required to avoid. Document that reason when
it would not be obvious; duplicated local imports require the same
justification.

Give each public class a short responsibility docstring. Add inline comments
only for non-obvious rationale, trust boundaries, state transitions, atomicity,
ownership, or failure containment. Do not comment what the next line already
says.

## Test Contract Rule

Test the narrowest stable observable contract: inputs to outputs, failures, or
invariants at a public API or meaningful module boundary. Prefer black-box
tests over assertions about private helpers, call sequences, intermediate
representations, branch structure, or incidental metadata.

A complex internal component may be tested directly when it owns an independent
semantic contract. Exercise that contract end to end (for example, source text
to parsed result or explicit rejection), not its implementation steps.

Keep one representative test per behavioral equivalence class. Merge workflow
cases into compact fixtures and parameterize only materially distinct supported
behaviors or failure risks. Every test must trace to an accepted requirement,
regression, or boundary invariant; a test alone does not create production
demand. Prefer fewer high-signal tests when a higher-boundary test covers the
same risk with useful failure diagnostics.

Retain a realistic fixture when an in-memory mock cannot exercise the boundary
under test, such as process crashes, pipe EOF, stderr draining, timeouts, or
process-group cleanup.

## Review Output

Lead with actionable findings, ordered by deletion value and risk. For each,
provide:

- classification: `delete`, `merge`, `keep`, or `document`;
- exact evidence and current consumers;
- the smaller replacement;
- behavioral and test impact.

Explicitly list apparently speculative entities that must stay because the
complete implementation already consumes them. If no actionable simplicity
issues remain, say so and state the residual risk.
