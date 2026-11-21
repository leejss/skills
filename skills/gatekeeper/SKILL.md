---
name: gatekeeper
description: Assign ownership for investigation, implementation, and verification, then assess the results before integration. Use when the user asks to orchestrate subagents or decide which work to delegate.
disable-model-invocation: true
---

# Gatekeeper

The main agent owns scope, design decisions, result assessment, code quality
review, and integration. Use the configured models for the main agent and
subagents.

## Gates

Identify the work units needed to complete the request and apply the Decision
Gate to each one. Give each unit one owner, either the main agent or a subagent.
Reassess verification execution when the checks and their prerequisites are
known. Once the owners return their results and evidence, the main agent applies
the Verification Gate and Quality Gate before integration.

### Decision Gate

Evaluate investigation, implementation, and verification execution separately.
Use a brief initial inventory to define work boundaries and assign ownership
for cross-component decisions. Group work by user flow or behavior when it spans
files or layers. Give related callers, services, and tests to the same owner
when they need to be understood together.

Delegate a work unit when all three conditions hold:

- A subagent with the required tools can execute it from a self-contained handoff.
- The main agent can define the work unit's Definition of Done (DoD).
- Parallel execution or reduced context use justifies the handoff and review cost.

Handle small, low-output work directly. Run independent delegated units in
parallel when practical.

Keep investigation, execution, and evidence collection with the assigned owner.
While a delegated unit is in progress, the main agent works on a separate unit
it owns or waits for the delegated unit's evidence. If a decision is blocked,
ask the owner for the specific interim evidence needed. Run a direct check
within the delegated scope only when the decision cannot wait for the owner.
Limit that check to the smallest relevant excerpt or command. Reassign ownership
explicitly if the owner cannot finish the unit.

### Verification Gate

Treat verification execution as its own work unit. Delegate it when the checks
can be specified in a handoff and require repeated simulator runs, lengthy builds
or test suites, or substantial log analysis. Apply this rule even when the main
agent implements the change.

The verification owner runs the checks, diagnoses failures, retries when needed,
and returns the results with evidence. The main agent assesses the owner's
report against the work unit's DoD and decides which completion criteria have
been met.

Inspect the cited evidence for consequential, conflicting, or unsupported claims.
Route additional investigation and verification runs to the owner with a specific
question or missing criterion. Report any criteria that remain unverified.

### Quality Gate

The main agent reviews the diff for integration, preservation of expected
behavior, and code quality. For review-only work, assess the reported findings
against the relevant code and product requirements.

Check for unnecessary complexity, excessive abstraction, and speculative
features or extension points (YAGNI). Judge readability from a human
maintainer's perspective. Check that control flow is straightforward, failure
behavior is clear, and variable and function names communicate their purpose.
Resolve material findings before integration.

## Handoff

Start each delegated work unit in a fresh subagent context without copying
the parent conversation. Pass a self-contained handoff as the initial prompt.

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

Request a concise report against the DoD. Include changed file paths where
relevant, checks run, observed outcomes, unresolved items, and evidence
locations. For each verification scenario, report the expected and observed
behavior and mark it as passed, failed, or unverified. Identify the test
environment and any stubs used. Leave full logs and captures at their evidence
locations for inspection when needed.
