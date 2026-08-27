---
name: recap
description: Create a focused Markdown record from the current conversation in learning, project, handoff, or archive mode. Use only when the user explicitly invokes $recap.
---

# Recap

Turn the conversation available at invocation time into one durable Markdown document. Preserve the user's intent, distinguish evidence from inference, and optimize the document for the selected mode rather than producing a generic transcript summary.

## Select one mode

Recognize the mode from the user's explicit wording, including clear Korean or English equivalents:

- **learning**: reusable technical learning, independent of a particular project
- **project**: how the relevant part of the current project actually works
- **handoff**: enough current state and next actions for someone to continue the work
- **archive**: a source-faithful, chronological record of how the conversation developed

If exactly one mode is clear, proceed without confirmation. If the mode is omitted, ambiguous, or multiple modes are requested for one file, ask the user to choose one before inspecting additional sources or writing a file. Never choose a default mode. A user may request multiple modes as separate documents; confirm the intended set only when it is unclear.

After selecting the mode, read only its reference:

- For learning, read [references/learning.md](references/learning.md).
- For project, read [references/project.md](references/project.md).
- For handoff, read [references/handoff.md](references/handoff.md).
- For archive, read [references/archive.md](references/archive.md).

## Source boundary

Use the conversation and context that are actually available at invocation time. A compacted conversation summary is evidence, but it is not a verbatim record. Never imply that inaccessible or omitted turns were reviewed. If missing context materially limits the result, state the limitation in the document.

Do not include secrets, credentials, access tokens, personal data, or other sensitive values merely because they appeared in the conversation or workspace. Redact the value while retaining the minimum context needed to make the document useful.

Write in the conversation's primary language unless the user requests another language. Preserve code identifiers, API names, commands, and established technical terms when translating them would reduce precision.

## Write the file

Honor an explicit output path or filename. Otherwise:

1. Resolve the active workspace root. If more than one root is plausible, ask which one to use.
2. Create a `recaps/` directory at that root when it does not exist.
3. Use `YYYY-MM-DD-<mode>.md` based on the user's local date.
4. If the filename already exists, append `-2`, `-3`, and so on. Never overwrite an existing recap unless the user explicitly asks to replace it.

The document should have a descriptive H1 title rather than only the mode name. Include the creation date and selected mode near the top. Use headings that fit the material; omit empty or irrelevant sections instead of filling a rigid template. Keep links and paths useful from the generated file's location.

After writing, report the selected mode, the created file, any important source limitation, and a concise description of what the document emphasizes.
