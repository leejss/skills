---
name: code-primer
description: Build and save a concept-first prerequisite learning path for selected code. Use when a reader wants to know what they must understand, and why, before reading it. Do not use for ordinary code explanation, call tracing, code review, or project-wide onboarding.
---

# Code Primer

Create and save a primer that answers:

> What knowledge outside the selected code is necessary to interpret it correctly, and why?

The code is evidence and boundary; the subject is the background model that makes it intelligible.

## Purpose

Assume the reader knows ordinary programming-language syntax but not the surrounding context. Provide the minimum sufficient set of concepts and relationships whose absence could materially distort interpretation. Every inclusion should clarify what an idea is, why it exists, or why it matters here.

The selected code determines relevance, not exposition. Knowledge relationships shape the primer; source structure does not.

Make the evidence-backed business situation the primer's memory anchor: who needs what outcome, which state changes, and what failure matters. Connect every major concept to that shared situation and to the kind of future situation in which the knowledge should be recalled. Mark uncertain business intent as inference rather than filling gaps with invention.

## Core shape

- A concise **Knowledge Map** names the essential concepts and why they belong together.
- A self-contained explanation gives the reader a usable background model.
- A concise evidence bridge shows where that model is expressed in the selected code and its surrounding business flow.

Keep general knowledge, observed project context, plausible inference, and external contracts distinguishable. Ground consequential external claims in authoritative, version-relevant sources and preserve meaningful uncertainty.

Evidence should be understandable in place:

- Support code claims with fenced, multi-line excerpts that preserve enough surrounding logic to reveal context. Label their source and explain what they demonstrate.
- Support external contracts with a short verbatim excerpt from the primary source, its link, and an interpretation connected to the business situation and code.

Path inventories, link lists, and isolated one-line fragments are indexes, not evidence. Keep excerpts selective while making the relevant claim independently understandable.

## Boundary

Include only knowledge whose omission would change or weaken understanding of the target. The primer prepares the reader for the code; it does not narrate, trace, evaluate, redesign, or broaden the implementation. Source identifiers support the final bridge rather than organizing the main explanation.

## Deliverable

Persist the primer as Markdown in the user's language and an appropriate workspace location, respecting requested paths and related existing material. It is complete when the reader can connect the business situation, necessary knowledge, supporting evidence, and code without encountering an untaught idea.

Report the saved path, the model it prepares, and any material uncertainty without repeating the primer.
