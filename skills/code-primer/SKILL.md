---
name: code-primer
description: Build and save a concept-first prerequisite learning path for selected code. Use when a user asks what background knowledge they need before they can understand that code. Do not use for ordinary code explanation, call tracing, code review, or project-wide onboarding.
---

# Code Primer

Create a persistent, beginner-friendly primer that answers:

> What must this reader learn before opening the selected code, and how does that knowledge help them read it afterward?

The selected code determines the curriculum, but it is not the subject of the main explanation. Teach the prerequisites so they make sense on their own, then return to the code in a short final bridge. Do not make the reader understand code symbols in order to learn the knowledge needed to understand those symbols.

## Use a fixed reader baseline

Assume the reader can read the programming language and understands its ordinary syntax and basic constructs. Do not assume they know the codebase's:

- business logic, domain vocabulary, or data semantics;
- architectural patterns, module boundaries, or local conventions;
- frameworks, libraries, packages, services, or their abstractions;
- security, concurrency, consistency, distributed-systems, or runtime models;
- specialized technical terminology beyond the language baseline.

Do not ask the reader to rate their knowledge. Include non-obvious language or runtime behavior only when the selected code depends on it.

Introduce every necessary technical term only after the reader understands the problem or mechanism it names. Explain it in plain language on first use. Do not use a glossary to repair an explanation that already depends on undefined terms, and do not replace one unexplained term with several others.

The target is a **minimum sufficient learning path**, not the shortest possible document and not mastery of every adjacent subject. Spend space on foundations that unblock understanding; remove details that only describe, implement, review, or operate the selected code.

## Establish the code scope

Use the code range, file, or symbol selected or named by the user. A stated topic or question may narrow the inquiry, but is not required. If the code scope cannot be determined from the request or current context, ask one focused question for it.

Inspect only the related code, configuration, manifests, lockfiles, and project documentation needed to discover the prerequisites and verify the final code bridge. Do not expand into a repository-wide survey. Do not inspect `node_modules` directly unless the user explicitly requests it.

Separate three kinds of evidence while investigating:

- **Observed project context** comes directly from the repository.
- **Inferred project context** is a plausible interpretation that the repository does not establish conclusively.
- **External technical behavior** comes from a language, framework, package, protocol, or platform contract.

Names and comments are clues, not proof of intent or correctness. Keep this evidence discipline in the writing without turning the primer into a research report.

## Plan a true prerequisite path

Before drafting, identify only the knowledge whose absence could cause the reader to misunderstand the selected code. Start with the ordinary problem the code is responding to, phrased without source identifiers or unexplained jargon. Then work backward through the concepts required to understand the solution.

For each candidate concept, ask:

1. What concrete problem or constraint makes this concept necessary?
2. Can that problem be understood using the reader baseline and concepts already taught?
3. If not, what smaller foundation must come first?
4. Would omitting this concept materially distort the reader's interpretation of the selected code?

Expand a prerequisite only when the answer to the last question is yes. Stop when the remaining explanation can be built from ordinary language, a concrete example, and already introduced concepts. If a foundation chain becomes long, group its lower-level details into a compact explanation without hiding a dependency the reader needs.

Arrange the selected concepts in the order a learner can actually acquire them, not the order they appear in the source. Prefer a single visible route from familiar problem to unfamiliar mechanism. Near the beginning of the saved primer, give the reader a short roadmap in plain language. Do not lead with a dense taxonomy, unexplained canonical terms, source symbols, or ambiguous relationship arrows.

Domain and project context may be prerequisites too. Teach the underlying idea in ordinary language before giving it the project's exact name. Keep the concept chapters understandable without opening the repository.

## Teach concepts before naming the code

Write the main body as a coherent technical article, not a coverage report. A strong progression usually:

- opens with a concrete situation or question the reader can already understand;
- develops one idea at a time in dependency order;
- uses a small, generic example or mental model when abstraction alone would be difficult;
- introduces the standard technical name only after explaining what it means;
- explains what the mechanism makes possible and what it does not guarantee;
- addresses a likely misconception only when it protects the reader's mental model;
- connects sections with causal transitions so the next idea feels necessary.

Use this as writing guidance, not as a mandatory heading template. Let headings follow the teaching argument. Prefer sentences and examples over definition lists, matrices, repeated checklists, and research-status labels. Use a diagram or table only when it makes a relationship materially easier to understand.

Keep target filenames, classes, functions, variables, fields, and other source identifiers out of the prerequisite chapters by default. Generic pseudocode is acceptable only when it teaches a concept more clearly than prose and does not require knowledge of the target code.

Do not interrupt the learning flow with citation-heavy prose. Place citations close enough to support consequential external claims, or collect a small set of useful references at the end when that reads better.

## Return to the selected code at the end

After the prerequisite concepts are understandable on their own, add a concise bridge back to the selected code. This is where source identifiers may appear.

The bridge should:

- map each taught concept to the few relevant files or symbols;
- suggest a useful order for reading those locations;
- state what the reader should notice, without walking through every line;
- include only the project context needed to translate the general mental model into local names;
- distinguish observed behavior from unresolved intent where that difference matters.

Do not repeat the full article in code-specific language. The reader should reach this section already understanding the ideas and need only learn where the project expresses them.

## Preserve the primer boundary

The selected code chooses the learning path; it does not authorize every interesting adjacent topic. Do not turn the primer into:

- a line-by-line or method-by-method code explanation;
- a call graph, repository inventory, or project-wide architecture document;
- a code review, bug hunt, risk assessment, or list of implementation improvements;
- a deep survey of alternatives, operational practices, or failure modes not required for comprehension;
- a project-independent `code-lore` article whose scope is no longer controlled by the selected code.

A primer may use the readable narrative style of a technical blog, but every included concept must still prepare this reader for this target. If a detail is interesting yet would not change how the reader understands the selected code, leave it out.

## Verify consequential external behavior

Use repository evidence for project-specific context. Verify consequential, uncertain, implementation-specific, or version-sensitive external claims with the closest Primary source available, such as:

1. standards, specifications, or RFCs;
2. official language, framework, package, protocol, or platform documentation;
3. authoritative maintainer documentation or source for claims about that implementation.

Match documentation to the dependency version shown by the project when the version affects behavior. Use secondary sources only to discover better sources or as clearly labeled supplementary context. If a necessary claim cannot be verified, state the uncertainty instead of inventing a citation or presenting an inference as fact.

Research should prevent incorrect teaching, not expand the curriculum.

## Save the primer

Every completed primer must be saved as Markdown. Honor an explicit output path or filename. Otherwise:

1. Resolve the writable workspace root that contains the selected code. Ask only when multiple roots are genuinely plausible.
2. Create `docs/primers/` under that root when it does not exist.
3. Derive a concise semantic topic slug and write `docs/primers/<topic-slug>.md`.

Before writing, inspect any existing file at the proposed path:

- If it covers the same code target and learning topic, update it conservatively and preserve useful user-authored material.
- If it covers a different target or topic, choose a more specific semantic slug instead of overwriting it or adding a numeric suffix.
- If the relationship is ambiguous and the choice would risk losing or conflating content, ask the user.

Record the selected file paths and important symbols in the final code bridge or a compact traceability note so future runs can recognize the document's target. Write in the user's language unless another language is requested.

If an existing `docs/lore/` document provides a useful deeper explanation, link to it. Do not invoke `code-lore`, create a lore document, or modify an existing lore document unless the user separately requests that work.

## Finish when the reader is ready for the code

Before reporting completion, re-read the saved document from the assumed reader's perspective and confirm that:

- the prerequisite chapters can be understood without opening the selected code;
- source symbols and project names are delayed until they can be understood from concepts already taught;
- every necessary non-baseline term is explained before it carries part of the argument;
- the learning order follows conceptual dependency rather than source order;
- the main body reads as a connected explanation rather than an inventory or report;
- every included concept materially changes or protects the interpretation of the selected code;
- the final bridge accurately connects the learned concepts to the target without becoming a walkthrough;
- project observations, inferences, and external contracts are not conflated;
- recorded code locations and meaningful citations are accurate.

After saving, report the document path, summarize what it prepares the reader to understand, and disclose any important unresolved context or source limitation. Do not duplicate the full primer in the conversational response.
