---
name: code-primer
description: Build and save the minimum prerequisite learning path for selected code. Use when a user asks what domain, logic, architecture, framework, package, runtime, or systems knowledge they need before they can understand that code. Do not use for ordinary code explanation, call tracing, code review, or project-wide onboarding.
---

# Code Primer

Create a persistent primer that answers:

> What must this reader know before they can understand the selected code?

Treat the code as the destination of a learning path. Do not turn the result into a line-by-line walkthrough, a project inventory, or a project-independent technical article.

## Use a fixed reader baseline

Assume the reader can read the programming language and understands its ordinary syntax and basic constructs. Do not assume they know the codebase's:

- business logic, domain vocabulary, or data semantics;
- architectural patterns, module boundaries, or local conventions;
- frameworks, libraries, packages, or their abstractions;
- relevant security, concurrency, consistency, distributed-systems, or runtime models.

Do not ask the reader to rate their knowledge. Include non-obvious language or runtime behavior only when the selected code actually depends on it.

The target is a **minimum sufficient learning path**, not mastery of every adjacent subject. The reader should finish with enough understanding to interpret the code's responsibility, the important abstractions it relies on, and the consequences of those abstractions in this code.

## Establish the code scope

Use the code range, file, or symbol selected or named by the user. A stated topic or question may narrow the inquiry, but is not required. If the code scope cannot be determined from the request or current context, ask one focused question for it.

Inspect only the related code, configuration, manifests, lockfiles, and project documentation needed to establish context. Do not expand into a repository-wide survey. Do not inspect `node_modules` directly unless the user explicitly requests it.

Separate three kinds of claims while investigating:

- **Observed project context** comes directly from the repository.
- **Inferred project context** is a plausible interpretation that the repository does not establish conclusively.
- **External technical behavior** comes from a language, framework, package, protocol, or platform contract.

Names and comments are useful clues, but are not proof of intent or correctness.

## Construct the Knowledge Map first

Identify only the knowledge whose absence could cause the reader to misunderstand the selected code. Consider, without treating this as a mandatory checklist:

- domain concepts and business rules;
- data meaning, state transitions, and invariants;
- architectural roles and boundaries;
- framework lifecycle and conventions;
- package abstractions and contracts;
- security, concurrency, consistency, distributed-systems, and runtime behavior.

Keep general prerequisites distinct from project-specific context. Project context may identify what a local component or value means, but should not grow into general project documentation.

Arrange the selected concepts in learning order and show meaningful dependencies such as `requires`, `specializes`, `protects`, `guarantees`, or `fails when`. Prefer a small map that exposes the critical path over an exhaustive taxonomy.

Include the concise Knowledge Map near the beginning of the saved primer, then continue into the explanations in the same run. Pause for the user's choice only when the selected scope produces a genuinely large map or materially different learning paths.

## Expand prerequisites only when they unblock understanding

Add concept B as a prerequisite of concept A only when all of the following are true:

1. A cannot be explained accurately from the reader baseline, already explained concepts, and plain language alone.
2. Not knowing B creates a material risk of misunderstanding how A affects the selected code.
3. B is relevant to this use of A, rather than merely useful background.

Stop expanding when deeper knowledge would not change the reader's interpretation of the selected code. Do not impose a fixed recursion depth. If a necessary chain becomes long, compress lower-level foundations into a short foundation group while preserving the dependencies that matter.

For each included concept, explain enough for the reader to understand:

- the problem or constraint the concept addresses;
- a practical mental model of how it works;
- why the selected code depends on it;
- a likely misconception that would distort the code's meaning.

These are coverage requirements, not mandatory subsection headings. Exclude history, exhaustive package options, unused behavior, implementation internals, and details needed only to implement or operate the technology unless they change the interpretation of the selected code.

## Verify consequential external behavior

Use repository evidence for project-specific context. Verify consequential, uncertain, implementation-specific, or version-sensitive external claims with the closest Primary source available, such as:

1. standards, specifications, or RFCs;
2. official language, framework, package, protocol, or platform documentation;
3. authoritative maintainer documentation or source for claims about that implementation.

Match documentation to the dependency version shown by the project when the version affects behavior. Use secondary sources only to discover better sources or as clearly labeled supplementary context. If a necessary claim cannot be verified, state the uncertainty instead of inventing a citation or presenting an inference as fact.

Research should protect the primer from incorrect assumptions, not turn it into a `code-lore` article. Cite only sources that materially support the learning path.

## Save the primer

Every completed primer must be saved as Markdown. Honor an explicit output path or filename. Otherwise:

1. Resolve the writable workspace root that contains the selected code. Ask only when multiple roots are genuinely plausible.
2. Create `docs/primers/` under that root when it does not exist.
3. Derive a concise semantic topic slug and write `docs/primers/<topic-slug>.md`.

Before writing, inspect any existing file at the proposed path:

- If it covers the same code target and learning topic, update it conservatively and preserve useful user-authored material.
- If it covers a different target or topic, choose a more specific semantic slug instead of overwriting it or adding a numeric suffix.
- If the relationship is ambiguous and the choice would risk losing or conflating content, ask the user.

Record the selected file paths and important symbols so future runs can recognize the document's target. Write in the user's language unless another language is requested.

Adapt the document's organization to the code, but ensure it contains:

- the target code and the primer's intended outcome;
- the Knowledge Map and learning order;
- the required general concepts and project context, clearly distinguished;
- an explicit connection from each concept to the selected code;
- important misconceptions, uncertainty, and supporting sources where relevant.

If an existing `docs/lore/` document provides a useful deeper explanation, link to it. Do not invoke `code-lore`, create a lore document, or modify an existing lore document unless the user separately requests that work.

## Finish when the path is sufficient

Before reporting completion, re-read the saved document and confirm that:

- every required concept in the map is explained or intentionally grouped;
- no unexplained technical term blocks a required learning path;
- every included concept changes or protects the interpretation of the selected code;
- project observations, inferences, and external contracts are not conflated;
- the recorded code locations and meaningful citations are accurate.

After saving, report the document path, summarize what it prepares the reader to understand, and disclose any important unresolved context or source limitation. Do not duplicate the full primer in the conversational response.
