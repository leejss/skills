---
name: code-lore
description: Turn selected code into project-agnostic, source-backed knowledge. Use when the user explicitly invokes $code-lore to uncover transferable concepts, principles, constraints, tradeoffs, or failure modes represented by code. Do not use for runtime call tracing, project documentation, or ordinary code review.
---

# Code Lore

Extract the durable knowledge embodied in code and make it consumable outside the project where it was found. Treat the code as an observation and discovery surface, not as the subject of the final explanation.

The result should help a developer understand the underlying idea well enough to recognize, evaluate, and apply it in another system.

## Preserve the boundary

Focus on knowledge such as:

- the problem being solved and the principle used to solve it;
- algorithms, protocols, state models, and design patterns;
- invariants, guarantees, constraints, and preconditions;
- security, concurrency, distributed-systems, and data-consistency concepts;
- failure modes, recovery strategies, and operational consequences;
- meaningful design alternatives and their tradeoffs;
- relationships to adjacent concepts and standards.

Do not turn the task into a call graph, file inventory, framework tutorial, code review, or description of project architecture. Mention execution order only when it is necessary to explain a general mechanism. Ignore boilerplate and incidental implementation choices unless they reveal a useful constraint or failure mode.

Do not present a project-specific business rule as universal knowledge. Generalize only as far as the evidence supports.

## Establish the inquiry

Use the code range selected or named by the user. Accept either:

- a code scope without a topic, in which case discover worthwhile knowledge candidates; or
- a code scope with a stated topic or question, in which case investigate that topic directly.

If the code scope cannot be determined from the invocation or current context, ask one focused question for it. Do not broaden the inspection to the entire repository merely to find more topics.

Before synthesizing, identify what the code directly demonstrates and what it only suggests. Names and comments may guide discovery, but they are not proof of intent or correctness.

## Discover with a Knowledge Map

When the user has not named a topic, inspect the selected code and return a concise provisional Knowledge Map before doing deep research. Include only candidates that are transferable and materially useful.

For each candidate, show:

- the general concept, phrased without project vocabulary;
- the problem or constraint it addresses;
- the strongest code observation that exposed it;
- its relationship to other candidates, when meaningful;
- why it is worth learning;
- confidence that the code genuinely represents the concept.

Use relationship labels that convey meaning, such as `requires`, `protects`, `guarantees`, `trades off`, `fails when`, or `specializes`. Prefer a small, ranked map over an exhaustive taxonomy.

Then stop and let the user choose a topic. Do not silently select one, perform broad external research, or write the full knowledge document in the same turn.

When the invocation already names a clear topic, skip the selection pause and proceed directly to research and synthesis. Still construct the relevant conceptual relationships as part of the result.

## Verify against Primary sources

Research the general concept independently of the project. Prefer the closest authoritative source for each claim:

1. standards, specifications, RFCs, laws, and other normative texts;
2. original research papers or canonical technical publications;
3. official platform, language, framework, protocol, or vendor documentation;
4. authoritative design or decision records from the technology's maintainers.

Use secondary sources only to discover Primary sources or to provide clearly labeled supplementary context. Do not use search snippets, unsourced summaries, community posts, or model memory as Primary sources.

Match each source to the claim it actually supports. Record the title, direct link or stable identifier, relevant section when available, and applicable version or date when it affects the conclusion. Prefer current sources, but use the historically relevant version when the observed code targets an older contract.

Code from the user's project is evidence of the observed implementation, not automatically a Primary source for the general principle. Source code from a language, library, or protocol implementation may be a Primary source only for claims about that implementation.

If no suitable Primary source is available, retain the insight only with a clear `inferred` or `unverified` label. Never manufacture a citation or strengthen a claim to make the document appear complete.

## Synthesize the knowledge

Write the main explanation in project-agnostic terms. Make the knowledge useful by connecting:

- what problem the concept addresses;
- how the mechanism works at the conceptual level;
- what it guarantees and does not guarantee;
- the conditions under which it applies;
- tradeoffs, alternatives, and failure modes;
- related concepts and how they differ;
- Primary sources supporting consequential claims.

Adapt the organization to the topic instead of filling a rigid template. Include a compact Knowledge Map when multiple concepts interact and prose alone would hide those relationships.

Keep provenance visible. Distinguish:

- **Verified**: directly supported by code evidence or an identified Primary source;
- **Inferred**: a reasonable interpretation not explicitly established by the evidence;
- **Unverified**: plausible but unresolved because a required source or context is missing.

Separate normative guidance from observed behavior. A standard may say what an implementation should do while the code shows what this implementation currently does. If they differ, report the discrepancy and its possible significance without silently choosing one as truth.

## Retain traceability without making it the subject

Keep project-specific identifiers out of the general explanation unless removing them would make the concept ambiguous. Put traceability in an optional **Observed implementation** appendix containing only the code locations, symbols, and brief observations needed to show where the knowledge was discovered.

Do not imply that the observed implementation is correct, complete, or recommended merely because it exists. Note meaningful gaps between the code and the source-backed model.

## Deliver proportionally

Default to a conversational Markdown result. Create or modify a file only when the user asks for a persistent artifact or provides an output path.

For a completed topic, lead with the distilled insight rather than the research process. Include the most useful supporting sources near the claims they support, a compact source list for further reading, the optional implementation appendix, and unresolved questions that could materially change the interpretation.

Stop when the selected topic is explained with enough evidence to be safely reused. Recommend adjacent topics when useful, but do not expand into them without the user's request.
