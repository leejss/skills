# Project mode

Create a project-specific technical note explaining how the discussed behavior actually works in the active workspace. Favor verified implementation details over conversational recollection.

## Set a bounded scope

Derive the subject from the conversation and keep the document centered on that feature, flow, component, or decision. Do not attempt to document the entire repository unless the user explicitly asks for that scope.

Inspect the files named or implicated by the conversation, then follow only direct dependencies needed to support the explanation. Search by relevant symbols and paths before opening broad areas of the repository. Stop when the behavior, important boundaries, and remaining uncertainty are adequately evidenced.

If there is no accessible project or the intended project is ambiguous, ask the user to identify the workspace before writing.

## Explain the actual system

Include the parts that materially help someone understand or change the scoped behavior:

- purpose and responsibility
- entry points and execution or data flow
- roles of relevant modules, files, functions, and data structures
- state, persistence, configuration, and external integrations
- important design decisions and constraints
- failure behavior and meaningful edge cases
- where a future change would be made and what else it may affect
- validation evidence and known gaps

Use repository-relative paths from the generated document when practical. Name relevant symbols so the reader can find the implementation even if line numbers later shift. Use a diagram only when it makes a multi-step or branching flow materially clearer.

## Stay evidence-based

Separate verified code behavior, conversational claims, and inference. Never present a planned change as current behavior. Do not invent configuration values, runtime results, test outcomes, or architectural intent.

Preserve project-specific terminology when it is needed to explain the system. Redact sensitive values and avoid copying large code blocks; quote only the smallest fragment needed to clarify non-obvious behavior.
