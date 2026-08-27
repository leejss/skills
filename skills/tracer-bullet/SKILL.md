---
name: tracer-bullet
description: Trace an existing feature or build the smallest verified end-to-end slice that resolves a key architectural or integration uncertainty. Use when exploring an unfamiliar codebase through one concrete behavior, or when proving a new capability before expanding it. Do not use for broad architecture surveys, throwaway mockups, or full feature delivery.
---

# Tracer Bullet

Resolve one important uncertainty by following a thin, observable path through the real system. Optimize for verified learning, not feature breadth. A tracer bullet is a production-shaped vertical slice: it crosses the boundaries that matter to the question without trying to complete the whole feature.

Do not reduce the work to horizontal scaffolding, a file inventory, or a mock that bypasses the risky integration. This is not a miniature MVP; user-facing completeness is optional, but the selected technical path must be real enough to test the assumption.

## Select the mode

Choose the mode from the user's requested outcome:

- **Trace** when the user wants to understand, explore, explain, or locate how an existing behavior works. Follow one concrete outcome from entry point to final effect. This mode is read-only by default; naming this skill alone does not authorize code changes.
- **Build** when the user explicitly asks to implement or change behavior. Add the smallest end-to-end slice that exercises the uncertain boundary and produces an observable result.

If the request is ambiguous but one mode is clearly safer and still useful, begin with Trace. Ask only when the choice would materially change the requested outcome or require new authority.

## Choose the bullet

State the uncertainty and the observable outcome that would resolve it. Then choose a path that:

- crosses the highest-risk relevant boundary, such as authentication, persistence, an external service, event delivery, or framework integration;
- starts at a real entry point and ends at a real observable effect;
- is the smallest path that can answer the question without pretending an important segment works;
- follows the repository's existing conventions and instructions where they are relevant.

When exploring an unfamiliar codebase, prefer a representative existing feature that passes through the boundaries under investigation. When building, prefer one narrow happy path. Do not expand into nearby features, broad refactors, exhaustive edge cases, or polish unless they are necessary to make the selected path run.

Sketch the expected path before going deep, for example:

```text
entry point -> orchestration -> domain behavior -> integration or storage -> observable result
```

Adapt the stages to the system. A CLI, event consumer, data pipeline, or library will have different boundaries from a web application.

## Trace an existing path

Follow runtime behavior rather than merely listing folders. Locate the concrete symbols, configuration, data transitions, and integrations that connect the entry point to the result. Keep the search bounded to the selected bullet.

Support the path with evidence such as code locations, an existing focused test, a safe local execution, a request and response, or relevant logs. Distinguish what the evidence confirms from what remains inferred. If execution is unavailable, use the strongest available static evidence and say what could not be verified.

Do not edit files in Trace mode unless the user separately asks for a change.

## Build a new path

Implement one working thread through the real components needed to test the uncertainty. Reuse established project patterns when they fit, but do not build generalized abstractions in anticipation of later slices.

Mocks, stubs, hardcoded values, or temporary adapters are acceptable only when they do not bypass the uncertainty being tested and their limitations are explicit. Prefer a narrow real integration over a broad simulated flow.

Verify the path with the smallest meaningful method for its risk: a focused test, local execution, an actual request, an observed state change, or another direct signal. Do not require unrelated linting or a full test suite by default.

## Decide when to stop

Trace is complete when the path from entry point to outcome is connected by evidence and the relevant behavior has been exercised when practical.

Build is complete when the slice runs end to end, produces the intended observable result, and directly tests the stated uncertainty. Do not claim completion when a critical segment is still assumed, mocked around, or unobserved.

If verification is blocked, stop at the furthest evidence-backed point. Report the blocker, what is known, and the smallest action needed to finish the validation. Do not quietly widen the assignment to work around it.

## Report the result

Lead with whether the tracer bullet resolved the uncertainty. Keep the report concise and include:

- the selected mode, bullet, and reason it was chosen;
- the end-to-end path actually traced or built;
- the strongest evidence or verification result;
- changed files for Build, or key code locations for Trace;
- confirmed learning, remaining uncertainty, and the next logical slice.

Recommend the next expansion point, but do not implement it unless the user requested broader delivery.
