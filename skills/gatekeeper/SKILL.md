---
name: gatekeeper
description: Assign ownership for investigation, implementation, and verification, then assess the results before integration. Use when the user asks to orchestrate subagents or decide which work to delegate.
disable-model-invocation: true
---

# Gatekeeper

The main agent orchestrates the work and decides what passes each gate. It
owns scope, design decisions, result assessment, and integration decisions.
Subagents execute all work units, including discovery, source and document
reads, implementation, verification, and any integration actions. This
applies to small tasks and work spanning components or repositories. The main
agent bases decisions on the user-provided context and evidence returned by
subagents. Use the configured models for the main agent and subagents.

## Gates

### Decision Gate

Assign each work unit to one subagent with the needed tools. Use the context
already supplied to set boundaries. If boundaries or prerequisites are unclear,
delegate discovery first. Group related callers, services, and tests when they
need to be understood together. For work spanning repositories, coordinate
dependencies across owners and assign an owner for any shared investigation or
end-to-end check. Run independent units in parallel when practical.

While owners work, coordinate dependencies, review returned evidence, or wait.
For missing evidence, corrections, or a time-sensitive decision, give the owner
a specific follow-up and ask for the relevant excerpt, diff, or interim result.
If the owner cannot finish, explicitly reassign the unit to a capable subagent.
If no subagent can perform the work, report the affected scope and the blocker
or missing capability. Continue with independent units that can proceed.

### Verification Gate

Assign verification as its own work unit once the checks and prerequisites are
known. The implementation owner may also own verification when appropriate.

The verification owner runs the checks, diagnoses failures, retries when needed,
and returns the results with evidence. The main agent assesses the owner's
report against the work unit's DoD and decides which completion criteria have
been met.

Use the returned evidence to assess consequential or conflicting claims. Ask
the owner for the specific evidence needed to resolve unsupported claims.
Report any criteria that remain unverified.

### Quality Gate

The main agent reviews the diffs and excerpts returned by the owners for
integration, preservation of expected behavior, and code quality. For
review-only work, assess the reported findings against the returned code
excerpts and relevant product requirements.

Check for unnecessary complexity, excessive abstraction, and speculative
features or extension points (YAGNI). Judge readability from a human
maintainer's perspective. Check that control flow is straightforward, failure
behavior is clear, and variable and function names communicate their purpose.
Route material findings to the owner for correction before integration.

If a change after verification could affect an earlier result, the verification
owner reruns the affected checks on the revised work. Before integration, the
main agent reassesses the affected completion criteria using the new evidence
and marks any criterion that lacks current evidence as unverified. The main
agent decides whether the work is ready and delegates any integration actions.

## Handoff

Start each new subagent in a fresh context without copying the parent
conversation. Pass a self-contained handoff as the initial prompt.

- In Codex, call `spawn_agent` with `fork_turns: "none"`.
- In Claude Code, use the `Agent` tool with a non-fork subagent type that has
  the tools needed for the work.

Include the objective, owned scope, relevant decisions and constraints, and
source references. In every handoff, state the work unit's Definition of Done
(DoD) with two explicit items:

- **Completion criteria** — What must be true for this work unit to be complete.
- **Evidence to return** — What the main agent needs to assess those criteria.

Keep the DoD proportional to the work. For investigation, specify what to
examine and what evidence to provide for any findings.

Request a concise report against the DoD. Include relevant paths and line
references, cited excerpts or diffs needed for gate decisions, checks run,
observed outcomes, unresolved items, and evidence locations. For each
verification scenario, report the expected and observed behavior and mark it
passed, failed, or unverified. Identify the test environment and any stubs used.
Keep full logs and captures at their evidence locations so the owner can
retrieve additional evidence on request.
