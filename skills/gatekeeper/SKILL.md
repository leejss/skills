---
name: gatekeeper
description: Decide which parts of a coding task to delegate and assess the results before integration. Use when the user asks to orchestrate subagents or decide which work to delegate.
disable-model-invocation: true
---

# Gatekeeper

The main agent owns scope, design decisions, integration, and the final result.
Use the configured models for the main agent and subagents.

## Gates

Identify the work units needed to complete the request. Apply the Decision Gate
to each unit to decide whether the main agent should execute it or delegate it.
Reassess who should run verification once the implementation is ready. After
collecting the results and evidence, the main agent applies the Verification
Gate and Quality Gate before integrating the changes.

### Decision Gate

Evaluate investigation, implementation, and verification execution separately.
Delegate a work unit when all three conditions hold:

- A subagent can execute it independently from a bounded handoff.
- The main agent can specify the expected result and the evidence needed to assess it.
- Parallel execution or reduced context use justifies the handoff and review cost.

Handle small, low-output work directly. Repeated simulator runs, screenshots,
and verbose logs can justify delegating verification even when the main agent
handles implementation.

### Verification Gate

The main agent decides whether the evidence satisfies the completion criteria.
Use the reported outcomes and inspect the evidence needed for that decision.
When evidence is missing or ambiguous, request a targeted check.
Report any criteria that remain unverified.

### Quality Gate

The main agent reviews the diff for integration, preservation of expected
behavior, and code quality. Check for unnecessary complexity, excessive
abstraction, and speculative features or extension points (YAGNI).

Judge readability from a human maintainer's perspective. Check that control
flow is easy to follow, failure behavior is clear, and variable and function
names communicate their purpose. Resolve material findings before integration.

## Handoff

Start each delegated work unit in a fresh subagent context without copying
the parent conversation. Pass a self-contained handoff as the initial prompt.

- In Codex, call `spawn_agent` with `fork_turns: "none"`.
- In Claude Code, use the `Agent` tool with a non-fork subagent type that has
  the tools needed for the work.

Include the objective, scope, relevant decisions and constraints, source
references, and completion criteria.

Request a concise result with changed file paths where relevant, checks run,
observed outcomes, unresolved items, and evidence locations. For verification,
include pass, fail, or unverified for each scenario and identify the test
environment and any stubs used.
