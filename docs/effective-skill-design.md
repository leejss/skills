# Effective Skill Design

This guide explains how to design agent skills as reusable workflows that are invoked accurately, execute predictably, and produce verifiable results.

It combines three sources of evidence:

- Skill structure, invocation, progressive disclosure, and authoring recommendations from the [official OpenAI Build skills documentation](https://learn.chatgpt.com/docs/build-skills)
- Recurring patterns for information hierarchy, completion criteria, composition, and maintenance observed across a variety of agent skills
- General design principles derived from analyzing how skills are invoked, structured, and composed

Rules directly confirmed in the official documentation are labeled **OpenAI standard**. Repeatable patterns found in practice are labeled **Practical technique**. Recommendations synthesized from both are labeled **Recommended standard**. This guide assumes no specific repository layout, skill names, or distribution policy. Platform-specific configuration is separated into an optional appendix.

## 1. What Is an Effective Skill?

A skill is not a long prompt file. It is an execution contract selected in a specific situation to perform one task repeatably.

An effective skill satisfies the following conditions:

1. **Accurate invocation**: It is selected for requests that need it and not selected for similar requests outside its scope.
2. **A clear job**: It has one job and one outcome direction.
3. **A predictable process**: Its inputs, preconditions, branches, steps, and stop conditions are explicit.
4. **Verifiable completion**: Completion is determined by observable evidence rather than the model's impression.
5. **Appropriate information volume**: The body contains only what every execution needs; everything else is loaded conditionally.
6. **Safe composition**: Its boundaries with other skills, tools, and user decisions are explicit.
7. **Maintainable structure**: Each rule has one owner, and drift between documented and actual behavior is verified.

In short:

> Effective skill = right trigger + one job + explicit contract + observable completion

## 2. The Skill Execution Model

### 2.1 Progressive Disclosure

**OpenAI standard.** ChatGPT and Codex do not read every `SKILL.md` from the start. They first inspect each skill's name and `description`, select a skill, and then read its full body. Codex uses up to 2% of the context window for the initial skill list, or up to 8,000 characters when the context size is unknown. When many skills are available, descriptions may be truncated first.

Place information in the following layers:

| Layer            | Role                                   | Always loaded? |
| ---------------- | -------------------------------------- | -------------- |
| `name`           | Identification and leading word        | Yes            |
| `description`    | Invocation conditions and scope        | Yes            |
| `SKILL.md` body  | Execution contract after selection     | After selection |
| `references/`    | Detailed rules for a specific branch   | When needed    |
| `scripts/`       | Deterministic execution                | When invoked   |
| `assets/`        | Reusable templates and resources       | When needed    |
| Host metadata    | UI, invocation policy, tool dependency | Host-dependent |

Progressive disclosure is more than a token-saving technique. It is an information architecture that prevents critical execution steps from being buried in conditional reference material.

### 2.2 Separate Invocation from Execution

A skill has two contracts:

```text
description: when to select this skill
SKILL.md body: what to do and how to do it after selection
```

Do not repeat a long trigger list in the body. Do not explain the workflow in the description.

### 2.3 There Is No Central Workflow Engine

**Recommended standard.** Composition in a typical skill system is not guaranteed by a separate DAG executor or central orchestrator. The model or host composes skills using information such as:

- Skills explicitly invoked by the user
- Descriptions of model-invoked skills
- Explicit subordinate-skill invocations in `SKILL.md`
- Workspace instructions and external configuration
- Task size, preconditions, and the current phase

In other words, composition is prompt-based orchestration. Distinguish between mentioning a skill by name and instructing the host to invoke it.

## 3. Design One Job

### 3.1 Define the Job by Its Outcome

**OpenAI standard.** One skill should focus on one job.

Define the job by its outcome, not by a list of capabilities.

Good examples:

- Diagnose a difficult bug through a reproducible feedback loop.
- Synthesize the current conversation into one specification.
- Review a change along two axes: Standards and Spec.
- Turn a human procedure into an executable guidance tool.

Bad examples:

- Help with software development in general.
- Handle planning, implementation, testing, deployment, and documentation.
- Improve code quality.

### 3.2 Find the Defining Constraint

State in one sentence what distinguishes this job from the ordinary approach that appears similar.

Examples:

- Specification synthesis: Synthesize what is already known instead of restarting the requirements interview.
- Prototyping: Build a throwaway artifact that answers one design question instead of producing application code.
- Bug diagnosis: Do not speculate about the cause before the feedback loop goes red.
- Change review: Do not mix general bug hunting with standards-compliance review.

Place this constraint near the beginning of the body. One sentence that changes behavior is better than a long philosophical explanation.

### 3.3 When to Split a Skill

Consider splitting a skill when any of the following is true:

- An independent job needs its own trigger.
- A discipline must be reusable by other skills.
- Different branches share very little guidance.
- The whole skill cannot be tied to one outcome and one completion criterion.
- Deterministic behavior can be isolated in a script.

Example splits:

```text
delivery-orchestrator
├── test-first-implementation
└── change-review
```

```text
discovery-orchestrator
├── decision-interview
└── terminology-management
```

The parent skill owns orchestration. Child skills own reusable disciplines.

## 4. Choose an Invocation Mode

### 4.1 Explicit and Implicit Invocation

**OpenAI standard.** Users can select a skill explicitly, and supported agents can select it implicitly when the request matches its description. An example of disabling implicit invocation on OpenAI hosts appears in the optional appendix.

### 4.2 Model-Invoked Skills

Use model invocation when any of the following is required:

- The model should discover the skill even when the user does not know its name.
- Another skill must invoke this skill.
- The skill provides a reference discipline shared by multiple skills.

The description of a model-invoked skill is a context pointer for the model. It must contain the actual trigger branches.

### 4.3 User-Invoked Skills

User invocation is suitable for:

- A large workflow that a person should start intentionally
- Work where external state changes or scope approval matter
- Work where explicit selection is part of the product experience
- Work whose always-loaded description costs more than automatic invocation is worth

Hosts may express the same policy through frontmatter, metadata files, manifests, or other mechanisms. Separate the core principle from host-specific syntax, and follow the official documentation for the host you use.

### 4.4 The Selection Question

Use this question to decide:

> Should the model invoke this skill usefully and safely even if the user does not type its name?

- Yes: model-invoked
- No: user-invoked

Do not choose model invocation merely because the skill is reusable. The deciding factor is whether automatic discovery is needed.

## 5. Write the Description as a Context Pointer

### 5.1 The Description Has Two Roles

**Practical technique.** A context pointer communicates two things:

1. What the skill does
2. Which branches require its full content to be read

A useful default structure is:

```text
<What it does>. Use when <actual invocation situations>.
Do not use when <confusable non-invocation situation>.
```

Example:

```yaml
description: >
  Diagnose hard bugs using a reproducible feedback loop.
  Use when behavior is broken, intermittent, unexpectedly slow,
  or resists a first-pass fix. Do not use for implementing a new feature.
```

### 5.2 Front-Load the Description

**OpenAI standard.** Put the core use case and trigger word first so the skill remains selectable even if its description is truncated.

Recommended order:

1. Job
2. Main trigger branches
3. Boundary with sibling skills
4. Artifact or execution method, only when needed

### 5.3 One Trigger per Branch

Do not list many synonyms. Keep only triggers that open distinct execution paths.

Bad:

```text
Use when the user says debug, diagnose, investigate, troubleshoot,
find the bug, fix the issue, solve the failure...
```

Good:

```text
Use for a hard bug, an intermittent failure, or a performance regression.
```

### 5.4 Exclude These from the Description

- Step-by-step procedures
- Background and philosophy
- The complete output template
- Every tool name
- Implementation details available in the body
- Promotional language intended to persuade people

## 6. Write the Body as an Execution Contract

### 6.1 Core Elements of the Body

Not every section is mandatory, but the body should answer these questions:

| Question                            | Body element         |
| ----------------------------------- | -------------------- |
| What must be received?              | Inputs               |
| What is required before starting?   | Preconditions        |
| What happens, and in what order?     | Process              |
| When does the path change?          | Branches             |
| When is a step complete?            | Completion criteria  |
| What is produced?                   | Output               |
| What happens on failure?            | Failure handling     |
| Which actions require confirmation? | Safety and authority |

### 6.2 Inputs

Distinguish between inputs the user must provide and facts the agent should discover from the environment.

```markdown
## Inputs

Resolve:

- The target branch or working diff
- The review baseline
- The originating issue or specification

Find workspace and environment facts yourself. Ask the user only for decisions or
information that cannot be discovered safely.
```

Do not ask the user for facts that can be found in files, configuration, Git state, or official documentation.

### 6.3 Preconditions

Specify a precondition only when its absence makes a correct result impossible.

```markdown
## Preconditions

The issue tracker configuration must be available through the workspace
instructions or an external configuration source.

If it is missing, tell the user which workspace setup is required and stop.
```

### 6.4 Use an Imperative Process

**OpenAI standard.** Write steps in the imperative and state their inputs and outputs.

Weak:

```text
Consider the code quality.
Try to understand the change.
Review things carefully.
```

Strong:

```text
Read every changed file.
Trace each changed public interface to its direct consumers.
Compare every acceptance criterion against observable behavior.
```

Each sentence should materially change the model's default behavior.

### 6.5 Outcome First, Then Necessary Process Constraints

**Recommended standard.** Define the outcome and success criteria first. Fix the detailed process only when the exact path is itself a product requirement.

A detailed order is necessary when:

- A discipline such as red-green directly determines result quality.
- A confirmation gate is required before an irreversible action.
- Step order affects data loss or security.
- Later steps can tempt the model to finish earlier steps prematurely.

Otherwise, define the goal, constraints, evidence, and output shape, and let the model choose an efficient path.

### 6.6 Branches

Write branches as `condition -> action`.

```markdown
| Situation            | Action                                                |
| -------------------- | ----------------------------------------------------- |
| A spec exists        | Review against its acceptance criteria                |
| Only an issue exists | Treat the issue body as the originating request       |
| Neither exists       | Run Standards review and mark Spec review unavailable |
```

Do not hide distinct paths inside a long paragraph.

### 6.7 Output

Specify the output's shape, content, and rule for choosing its storage location.

```markdown
## Output

Write one Markdown report containing:

- Scope and baseline
- Evidence gathered
- Findings ordered by severity
- Unresolved questions
- Verification commands

Use the environment's existing artifact-location convention. If none exists,
return the report directly unless the task requires a persistent file.
```

Avoid vague instructions such as `Report the findings` that define neither shape nor quality.

### 6.8 Failure Handling

If a skill describes only the success path, the model may guess or repeat unnecessarily when blocked.

```markdown
## Failure handling

- If the bug cannot be reproduced, stop and report the missing evidence.
- If a required tool is unavailable, use the documented fallback.
- If no safe fallback exists, ask the user before changing the approach.
- If evidence contradicts the hypothesis, discard it and return to instrumentation.
```

## 7. Prevent Premature Completion with Completion Criteria

### 7.1 Give Important Steps Observable Completion Criteria

**Practical technique.** End each important step with `Done when`.

```markdown
### 1. Build the feedback loop

Create one command that reproduces the reported bug.

**Done when:** the command fails for the reported behavior and passes for
the nearest known-good behavior.
```

### 7.2 Properties of Good Completion Criteria

- **Checkable**: Completion can be observed.
- **Exhaustive**: It is difficult to stop after checking only part of the work.
- **Local**: It can be evaluated within the current step.
- **Evidence-bearing**: It leaves a command, diff, file, or link that supports the result.

Weak:

```text
Done when the codebase is understood.
Done when enough research has been done.
Done when the result looks good.
```

Strong:

```text
Done when every changed public interface has a corresponding consumer list,
including interfaces with no consumers.
```

### 7.3 Post-Completion Pull

When future steps are visible, the model may rush the current step. First make the completion criteria more explicit. Split work into phase, handoff, or subagent boundaries only when premature completion remains a repeated problem and the current step can run independently.

## 8. Design the Information Hierarchy

### 8.1 Separate Steps from Reference Material

**Practical technique.** Skill content falls into two broad categories:

- **Steps**: Actions the agent performs in sequence
- **Reference**: Definitions, rules, and examples applied when making decisions

A skill may be workflow-oriented, reference-oriented, or hybrid.

| Type      | Core structure                              | Examples                              |
| --------- | ------------------------------------------- | ------------------------------------- |
| Workflow  | Inputs, steps, branches, completion, output | Diagnosis, migration, setup procedure |
| Reference | Definitions, principles, criteria, checklist | Design discipline, review standard   |
| Hybrid    | Steps plus reference                        | Test-driven implementation, task decomposition |

Do not force a reference skill into an artificial sequence of steps. Instead, make its scope and exhaustiveness bar explicit.

### 8.2 Branch-Based Disclosure

Split content by branch, not by file length.

- Needed on every execution path: `SKILL.md`
- Needed only on some paths: `references/`
- Must repeat deterministically: `scripts/`
- Reuses a fixed shape: `assets/`

### 8.3 Co-Location

Keep a concept's definition, rules, exceptions, and examples together. Spreading the same idea across sections encourages the reader to see one rule and miss another constraint.

### 8.4 Sprawl

Even when every sentence is valid, excessive length thins attention. Reduce content in this order:

1. Remove facts that are easy to discover from the environment.
2. Extract conditional reference material.
3. Split references by branch.
4. Move repeated rules to the skill that owns them.
5. Move deterministic logic into scripts.

## 9. Define the Boundaries Between Instructions, Scripts, References, and Assets

### 9.1 Instruction First

**OpenAI standard.** Prefer instructions over scripts when the work requires neither deterministic behavior nor an external tool.

Instructions are suitable for:

- Reading and evaluating a codebase
- Conducting a requirements interview
- Applying review criteria
- Decomposing work into tickets
- Choosing an artifact structure based on context

Scripts are suitable for:

- Generating the same file structure
- Performing an exact string transformation
- Validating formats and schemas
- Repeating API calls
- Providing a complex shell UX
- Automating error-prone mechanical procedures

Selection question:

> Do you expect the same output for the same input?

- Yes: script candidate
- No, contextual judgment is central: instruction candidate

### 9.2 When Including a Script

- Document its usage and inputs in the body.
- Explain how to verify its output.
- Do not duplicate the script's implementation in the body.
- Define fallbacks and stop conditions for failures.

### 9.3 When Including Reference Material

- Include the condition for reading it in the pointer.
- Do not require the entire folder to be read unconditionally.
- Keep references within the skill's domain.
- If another skill needs the same discipline, invoke the skill that owns the reference.

## 10. Compress Behavior with a Leading Word

### 10.1 Leading Words

**Practical technique.** A leading word uses a concept the model already knows to compress a long behavioral explanation into one word or short phrase.

Examples:

- `red`: a state in which the bug is actually reproduced
- `tight`: a loop that is fast, reliable, and inexpensive to repeat
- `tracer bullet`: a small vertical slice connected from end to end
- `frontier`: the set of decisions whose prerequisites are resolved and can be addressed now
- `seam`: a boundary that can be observed and tested externally
- `fog of war`: an area whose path is unclear because decisions remain unresolved

Bad:

```text
Build a test command that is fast, reliable, and cheap to repeat.
```

Good:

```text
Build a tight feedback loop that goes red on this bug.
```

### 10.2 Selection Criteria

- Prefer concepts already present in the model's pretraining.
- Use them consistently in the name, description, and body.
- Create a new term only when its repeated benefit exceeds its definition cost.
- Look for repeated explanations that one strong word can replace.

### 10.3 Negation

Emphasizing only prohibited behavior can make that behavior more prominent in context. State the desired behavior positively when possible.

```text
Weak: Do not write verbose comments.
Strong: Write one-line comments that explain only non-obvious intent.
```

Negation is appropriate for genuine invariants and safety boundaries. Even then, state the desired alternative behavior as well.

## 11. Distinguish Hard Dependencies from Soft Dependencies

### 11.1 Hard Dependencies

Without one, the result is incorrect.

Examples:

- A skill that publishes an issue without knowing the issue tracker location
- A skill that applies tracker labels without a label mapping
- A skill that evaluates specification compliance without the required source artifact

Handling:

```text
Find the required information -> if it is missing, ask the user for the exact next action -> stop
```

### 11.2 Soft Dependencies

The skill still works without one, but the result is less precise.

Examples:

- A domain glossary
- A related ADR
- Optional historical context

Handling:

```text
Use it when available -> otherwise proceed reasonably from the current code and conversation
```

### 11.3 Do Not Duplicate Dependency Language

Do not cargo-cult a setup pointer into every skill. Include it only in skills where that dependency is load-bearing.

## 12. Compose Skills Safely

### 12.1 Distinguish Mentions from Invocation Instructions

Router prose for people:

```text
Use the test-first skill for a concrete behavior.
```

An operative instruction that must actually invoke another skill during execution:

```text
Invoke the "test-first-development" skill using the host's skill mechanism.
```

The second sentence expresses intent in a host-neutral way. In an actual file, use the invocation syntax or tool officially supported by the host.

### 12.2 Invoke Skills One at a Time

```markdown
Invoke two skills separately:

1. Invoke "decision-interview" to resolve the decision tree.
2. Invoke "terminology-management" to update the glossary and decision records.
```

### 12.3 User-Invoked Boundaries

A user-invoked skill cannot be invoked automatically by another skill.

```markdown
If required setup is missing, tell the user which user-invoked setup skill
to run, then stop.
```

### 12.4 Artifact Handoffs

Define artifact contracts so one skill's output becomes the next skill's input.

```text
research -> cited findings
specification synthesis -> one authoritative specification
work decomposition -> self-contained work items with dependency edges
implementation -> verified changes and review findings
```

If composition is unstable, inspect the artifact boundary before the skill names.

### 12.5 Router Skills

When there are too many user-invoked skills for people to remember, create a router. It recommends what to select and then stops. It does not attempt to execute a user-invoked skill on the user's behalf.

## 13. Separate Facts from Decisions

**Practical technique.** Discovering facts is the agent's job. Product decisions belong to the user.

The agent should find:

- Files and configuration
- Git state
- Existing commands
- Actual code behavior
- Official API behavior
- Primary sources

Ask the user about:

- Product behavior
- Scope boundaries
- Preferences among multiple valid alternatives
- Hard-to-reverse tradeoffs
- Significant changes affecting external systems

Make this distinction explicit in the skill body. It reduces questions the user should not need to answer and prevents the model from making product decisions for them.

## 14. Design Safety and Authority Boundaries

Do not duplicate generic safety language in every skill. Address the risks specific to the job.

Examples:

- Credentials: redact them from commands, output, and artifacts.
- Deletion: verify the exact target and prefer recoverable operations.
- Deployment: do not publish without user approval.
- Messaging: confirm the recipient and content before sending externally.
- Migration: require a confirmation gate before irreversible actions.
- Review: do not report criticism as a finding without evidence.

Recommended form:

```markdown
## Safety

- Keep credentials in environment variables.
- Replace sensitive values in shown commands and outputs with `<REDACTED>`.
- Confirm the exact target before an irreversible action.
- When the requested authority is missing, report the blocker and stop.
```

## 15. Pruning and a Single Source of Truth

### 15.1 Duplication

When the same meaning is copied into multiple files, behavior evolves inconsistently. Give each rule one owner.

### 15.2 The Environment as the Source of Truth

The following are easy to discover from the environment:

- `package.json` scripts
- Configuration files
- Directory layout
- CLI `--help`
- Current Git state

Rewriting these in documentation turns the documentation into a cache. When lookup is inexpensive, read the environment and keep only what the environment cannot tell you in the documentation:

- Why a choice was made
- Which convention to follow
- Which gotchas matter
- What constitutes a hard boundary

### 15.3 No-Ops

Restating behavior the model already performs by default only consumes context.

```text
Be careful.
Do a good job.
Think step by step.
Be thorough.
```

Use representative prompts to verify whether a sentence changes the actual execution trace. Remove it entirely if it does not.

### 15.4 Sediment

Skills are easy to add to and hard to trim, so stale instructions accumulate over time. When behavior changes, review all of the following:

- Description
- Body
- References and templates
- Human-facing documentation
- Router
- Trigger evaluation
- Distribution and package metadata

## 16. Trigger Evaluation and Execution Evaluation

### 16.1 Trigger Evaluation

**OpenAI standard.** Test the description against real prompts to verify correct trigger behavior.

Minimum fixture:

```yaml
skill: root-cause-analysis

should_trigger:
  - "This bug only happens intermittently. Find the cause."
  - "The API response has been three times slower since yesterday."
  - "The first fix did not work. Debug it systematically."

should_not_trigger:
  - "Implement a new payment feature."
  - "Review the current diff."
  - "Turn this idea into a prototype."
```

Verify:

- Explicit invocation
- Representative implicit invocation
- Wording without technical terminology
- Negative near-miss
- Sibling collision
- Description truncation

### 16.2 Execution Evaluation

| Test                    | What to verify                                              |
| ----------------------- | ----------------------------------------------------------- |
| Happy path              | Does it produce the expected artifact?                      |
| Missing input           | Does it discover facts or ask the minimum questions instead of guessing? |
| Hard dependency missing | Does it stop at exactly the right point?                     |
| Soft dependency missing | Does it degrade reasonably?                                 |
| Contradicting evidence  | Does it discard the hypothesis and return to investigation? |
| Partial success         | Does it avoid reporting success before meeting completion criteria? |
| Tool unavailable        | Does it use a fallback or report the blocker?                |

### 16.3 Composition Evaluation

- Does the parent skill actually invoke the required child skills?
- Does it avoid automatically invoking user-invoked skills?
- Is the previous step's artifact sufficient for the next step?
- Are shared rules defined consistently rather than differently across skills?
- Is there a fallback when a subagent or connector is unavailable?

### 16.4 Separate Structural Validation from Behavioral Evaluation

Structural validation:

- Parse YAML and frontmatter.
- Confirm that `name` and `description` exist.
- Confirm that invocation policies are synchronized.
- Confirm that the distribution manifest matches the skill list.
- Confirm that required references and assets exist.

Behavioral evaluation:

- Trigger precision and recall
- Workflow adherence
- Completion quality
- Artifact correctness
- Safety boundaries

Passing structural validation does not guarantee effective behavior.

## 17. Authoring Sequence

### Step 1. Observe Repeated Failures

Skills are strongest when they address repeatedly observed variance rather than a vague idea.

Record:

- Behavior users repeatedly correct
- Steps the model frequently skips
- Conventions that must be re-explained each time
- Identical procedures a person repeatedly performs manually
- Disciplines duplicated across several skills

### Step 2. Write the Job and Non-Job

```text
Job: <one outcome>
Non-job: <the most confusable adjacent task>
```

### Step 3. Perform One Successful Trace

Complete the work without the skill and record:

- Inputs actually required
- Important decision points
- Required tools and references
- Evidence of success
- Failure and recovery paths

### Step 4. Choose the Invocation Mode

- Should the model discover it automatically?
- Must another skill invoke it?
- Should a person start it explicitly?

### Step 5. Write the Description First

Briefly state the job, branch triggers, and sibling boundary. If you cannot write the description, the skill's boundaries are often not yet clear.

### Step 6. Write the Outcome and Completion Criteria

Define the final artifact and success criteria before writing the steps.

### Step 7. Fix Only the Necessary Process

Specify only the ordering that materially affects quality, safety, or artifact compatibility.

### Step 8. Organize the Information Hierarchy

- Always-needed content goes in the body.
- Branch-specific content goes in references.
- Deterministic work goes in scripts.
- Reusable shapes go in assets.

### Step 9. Create Positive and Negative Evaluations

Include at least one case that can be confused with a sibling skill.

### Step 10. Run the Skill, Then Prune It

Check every sentence:

- Does it change execution behavior?
- Is it relevant to the current job?
- Does it duplicate another source?
- Is it easy to discover from the environment?
- Can a stronger leading word replace it?

## 18. Recommended Directory Structure

```text
my-skill/
├── SKILL.md
├── references/
│   ├── branch-a.md
│   └── branch-b.md
├── scripts/
│   └── validate.sh
└── assets/
    └── template.md
```

Create only what you need. Do not add empty folders merely by convention. Add separate metadata only when the host requires it, and follow the host's official format.

## 19. Model-Invoked `SKILL.md` Template

```markdown
---
name: my-skill
description: >
  <What it does>. Use when <trigger branches>.
  Do not use when <confusable non-trigger>.
---

# <Skill name>

<State the job and defining constraint in one or two sentences.>

## Inputs

Resolve:

- <input 1>
- <input 2>

Find environmental facts yourself. Ask the user only for decisions or
information that cannot be discovered safely.

## Preconditions

- <hard dependency and stop behavior>
- <soft dependency and fallback>

## Process

### 1. <Imperative step>

<Actions to perform.>

**Done when:** <checkable and exhaustive completion condition>.

### 2. <Imperative step>

<Actions and branches.>

**Done when:** <completion condition>.

### 3. <Imperative step>

<Actions to perform.>

**Done when:** <completion condition>.

## Output

Produce:

- <artifact or response>
- <evidence>
- <verification result>
- <unresolved items>

Save it using <location resolution rule>.

## Failure handling

- If <recoverable condition>, <recovery>.
- If <hard blocker>, <report and stop behavior>.

## Safety

<Include only job-specific safety and authority boundaries.>
```

## 20. Changes for User-Invoked Skills

Write the job body of a user-invoked skill in the same way as a model-invoked skill, but use host-specific metadata to disable automatic discovery.

```yaml
---
name: my-user-skill
description: A short human-facing summary.
---
```

If the host supports a separate invocation policy, disable implicit invocation. Remove long model-facing trigger lists from a user-invoked description. Write a one-line summary that a person can read and remember in a picker.

Exact frontmatter and policy keys vary by host. Do not couple the core design in this guide to one host's syntax.

## 21. Optional Appendix: OpenAI Metadata Example

This section applies only when targeting OpenAI's ChatGPT and Codex. On another host, use that host's official metadata format. Place the following in `agents/openai.yaml` inside the skill directory.

```yaml
interface:
  display_name: "My Skill"
  short_description: "Short user-facing description"
  default_prompt: "Optional surrounding prompt"

# Add only for a user-invoked skill.
policy:
  allow_implicit_invocation: false

# Add only when a real tool dependency exists.
dependencies:
  tools:
    - type: "mcp"
      value: "exampleTool"
      description: "Why the skill needs this tool"
      transport: "streamable_http"
      url: "https://example.com/mcp"
```

UI descriptions and invocation descriptions serve different roles. `short_description` is display text for people, while the model-invoked description in `SKILL.md` is a pointer used by the model for selection.

## 22. Anti-Patterns

### God Skill

One skill owns discovery, planning, implementation, review, and deployment.

Fix:

- Separate orchestration from discipline.
- Define artifact boundaries.
- Split jobs that have independent triggers.

### Trigger Soup

The description lists excessive synonyms but has no sibling boundary.

Fix:

- Use one trigger per branch.
- Front-load the job.
- Add negative near-miss evaluations.

### Tutorial Body

The body reads like a lesson for people rather than an execution contract for the agent.

Fix:

- Reduce background material and rewrite it as imperative actions, decision rules, outputs, and completion criteria.

### Hidden Branch

Distinct execution paths are buried in a long paragraph.

Fix:

- Use a table or condition-action list.
- Extract branch-specific references.

### Fuzzy Done

The skill stops based on criteria such as "once it is understood well enough" or "when it looks good."

Fix:

- Use checkable, exhaustive, evidence-based criteria.

### Script Everything

Even work that requires judgment is locked into scripts.

Fix:

- Script only the deterministic core.
- Keep contextual judgment in instructions.

### Instruction Everything

Deterministic, repetitive work is reimplemented in natural language every time.

Fix:

- Extract repeated operations into scripts, assets, or templates.

### Cargo-Cult Dependency

Every skill requires setup, a glossary, or a tracker.

Fix:

- Distinguish hard dependencies from soft dependencies.

### Bare Skill Mention

A sentence contains `/skill-name` when an actual invocation is required.

Fix:

- In operative instructions, use the host's explicit invocation mechanism.

### Documentation Cache

Information immediately available from the environment is copied into documentation.

Fix:

- Use the environment as the source of truth.

### Untested Description

The body is detailed, but the skill is not invoked automatically for real requests or the wrong sibling is selected.

Fix:

- Add should-trigger, should-not-trigger, and sibling-collision evaluations.

## 23. Review Checklist

### Job

- [ ] The outcome can be described in one sentence.
- [ ] A defining constraint exists.
- [ ] The nearest non-job is clear.
- [ ] The work converges on one completion direction.

### Invocation

- [ ] Model invocation or user invocation was chosen intentionally.
- [ ] The job and core triggers appear near the beginning of the description.
- [ ] The description follows one trigger per branch.
- [ ] A sibling boundary exists.
- [ ] The target host's invocation policy matches the intended behavior.

### Body

- [ ] Inputs and preconditions are distinct.
- [ ] Steps use the imperative.
- [ ] Important branches are visible as condition-action rules.
- [ ] Important steps have completion criteria.
- [ ] Output and storage-location rules are clear.
- [ ] Failure and stop behavior are defined.
- [ ] Fact discovery is separated from user decisions.

### Information Architecture

- [ ] The body contains only information needed on every path.
- [ ] Branch-specific information is separated into references.
- [ ] Deterministic work is moved into scripts or assets.
- [ ] A concept's definition, rules, and exceptions are co-located.
- [ ] Environment lookups are not duplicated unnecessarily.
- [ ] The same rule is not duplicated across multiple skills.

### Composition

- [ ] Child-skill invocations are explicit operative instructions.
- [ ] User-invoked skills are not invoked automatically.
- [ ] Hard and soft dependencies are distinguished.
- [ ] The previous step's artifact is sufficient as input to the next.
- [ ] Behavior is defined when a tool or connector is unavailable.

### Evaluation

- [ ] Explicit invocation was tested.
- [ ] Representative implicit prompts were tested.
- [ ] Negative near-misses were tested.
- [ ] Sibling collisions were tested.
- [ ] Missing hard dependencies were tested.
- [ ] Partial success is not reported as completion.
- [ ] The actual artifact was verified.

### Maintenance

- [ ] The description was reviewed when behavior changed.
- [ ] References, assets, and scripts were reviewed.
- [ ] The router and human-facing documentation were reviewed.
- [ ] Trigger fixtures were updated.
- [ ] The distribution list and metadata were verified to be synchronized.

## 24. Adoption Sequence for an Organization or Project

When applying this guide to an existing skill set, the following order is effective:

1. Create `should_trigger` and `should_not_trigger` fixtures for each model-invoked skill.
2. Find pairs of skills with adjacent purposes and evaluate sibling collisions.
3. Standardize operative cross-skill calls on the target host's explicit invocation mechanism.
4. Move the job and triggers of long descriptions to the beginning.
5. Automatically verify synchronization among the skill list, distribution manifest, documentation, and invocation policy.
6. Confirm that human-facing documentation does not duplicate `SKILL.md` procedures.
7. Move experimental skills to the stable channel only after they pass structural validation and behavioral evaluation.

## 25. Final Principles

A good skill is not a document that gives the model a large amount of information. It is a small workflow module that is selected at the right moment, performs one job, reads only the information it needs, and leaves a verifiable result.

Remember this sequence when authoring a skill:

```text
observe variance
-> define one job
-> choose invocation
-> write the pointer
-> define outcome and done
-> constrain only the necessary process
-> disclose references by branch
-> automate deterministic work
-> test triggers and execution
-> prune everything that does not change behavior
```

The most important quality criterion is not the document's length or level of detail. It is whether the correct skill is selected for the same kinds of requests, the execution trace follows the intended discipline, and the artifact proving completion is produced consistently.
