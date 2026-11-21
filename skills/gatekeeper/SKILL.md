---
name: gatekeeper
description: Set completion criteria, delegate work, and assess evidence for completion and integration. Use when the user asks to orchestrate subagents or decide which work to delegate.
disable-model-invocation: true
---

# Gatekeeper

The main agent orchestrates the work and decides what passes each gate. It
owns scope, material design decisions, result assessment, and integration
decisions. Subagents execute all work units, including discovery, source and
document reads, implementation, verification, and any integration actions. This
applies to small tasks and work spanning components or repositories. The main
agent bases decisions on the user-provided context and evidence returned by
subagents. Use the configured models for the main agent and subagents.

## Gates

### Decision Gate

Define each work unit around an outcome one subagent can complete and verify.
Keep routine investigation, review, implementation, verification, and authorized
integration together when they serve that outcome. Split work when a decision,
dependency, or ownership boundary requires a separate outcome. If the main
agent needs discovery to decide what to assign next, give discovery its own
outcome. Assign one owner with the tools to complete each unit, coordinate
dependencies across owners, and run independent units in parallel when
practical.

Define the scope, completion criteria, and required evidence before dispatch.
Specify which follow-through actions the owner can take and under what
conditions, including any integration the user has authorized. The owner
chooses the methods, sequence, and recovery steps within scope and carries
the unit through verification and authorized follow-through. An up-front
integration decision applies when current evidence satisfies its conditions.
The owner reports at completion without waiting for intermediate approval.

Escalate only when a scope or authorization change, conflicting evidence,
unresolved tradeoff, or blocker requires a decision from the main agent. Pause
the affected work, state the decision needed, and provide the relevant evidence.
Continue independent work that can proceed. When integration requires fresh
judgment, the main agent reviews the evidence and decides before integration.

While owners work, coordinate dependencies and review returned evidence. Ask
for a specific excerpt, diff, or interim result when needed for a decision or
correction. If the owner cannot finish, explicitly reassign the unit to a
capable subagent. If no subagent can perform the work, report the affected scope
and the blocker or missing capability.

### Verification Gate

Keep routine verification with the owner. Create a separate verification unit
when a check requires independent judgment, tools the owner lacks, or
coordination across work units.

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
Route material findings to the owner for correction.

If a change after verification could affect an earlier result, the verification
owner reruns the affected checks on the revised work. The main agent reassesses
the affected completion criteria using the new evidence and marks any criterion
that lacks current evidence as unverified.

## Handoff

Start each new subagent in a fresh context without copying the parent
conversation. Pass a self-contained handoff as the initial prompt.

- In Codex, call `spawn_agent` with `fork_turns: "none"`.
- In Claude Code, use the `Agent` tool with a non-fork subagent type that has
  the tools needed for the work.

Include the objective, owned scope, relevant decisions and constraints, source
references, and any conditional authorization for follow-through actions.
Give the work unit a Definition of Done (DoD) with both items below.

- **Completion criteria** — What must be true for this work unit to be complete.
- **Evidence to return** — What the main agent needs to assess those criteria.

Keep the DoD proportional to the work. For investigation or review, specify
the question to answer and the evidence to return for any findings.

Request a concise report against the DoD. Include relevant paths and line
references, cited excerpts or diffs needed for gate decisions, checks run,
observed outcomes, unresolved items, and evidence locations. For each
verification scenario, report the expected and observed behavior and mark it
passed, failed, or unverified. Identify the test environment and any stubs used.
Keep full logs and captures at their evidence locations so the owner can
retrieve additional evidence on request.
