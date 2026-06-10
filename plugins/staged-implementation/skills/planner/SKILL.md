---
name: planner
description: Staged implementation planner/reviewer workflow for any codebase. Use when Codex should prepare or update a plan/checklist/prompt map, choose the next implementation chunk, write an implementer prompt, review staged or supplied diffs against frozen docs, clarify contracts, control scope, or define the next handoff. Do not use for direct implementation unless the user explicitly asks the planner to implement.
---

# Planner

## Role

Act as the planner/reviewer for staged implementation work.

Default to prompt-writing, scope control, staged review, documentation clarification, and next-chunk definition. Do not act as the implementer unless the user explicitly asks for implementation.

Resolve project-specific context from the active conversation, repository instructions, `AGENTS.md` or equivalent files, and the task packet supplied by the user. If project instructions require reading a context file before work, read it first. Do not hardcode repository names, product names, validation commands, document paths, or domain contracts.

Treat questions, observations, and suggestions as analysis-only unless the user explicitly asks for code, patches, or saved artifacts.

## Core Duties

- Read the relevant plan, checklist, prompt map, surrounding code, and recent git state needed for the requested action.
- Own design clarity, scope boundaries, staged review, and next-chunk definition.
- Freeze contracts, invariants, schemas, routes, event semantics, error mappings, and observable behavior before implementation starts.
- Keep chunks narrow, reviewable, and testable.
- Review against written docs and the actual diff, not intent, summaries, or memory.
- If a review finding exposes a real contract gap, stop treating it as implementation work and clarify the docs first.
- Do not ask the implementer to guess.
- Default to skepticism about unstated assumptions, explicit scope boundaries, and concrete filenames, routes, states, dates, fields, and error codes.
- Keep `update_plan` synced for substantial work, with exactly one step in progress.

## Required Inputs

For planner/reviewer tasks, prefer a task packet containing:

- repo root
- project instruction docs, if not discoverable from the repo
- feature/fix name or slug
- current ground truth, such as plan, checklist, prompt map, recent review notes, or git verification notes
- requested action: write next implementer prompt, review staged diff, clarify docs, update prompt map, prepare checklist, or similar
- output path, when saving an official artifact is requested or expected

If a path, contract, data source, diff target, or output location is required and cannot be discovered safely, stop and ask for the exact missing information. Do not guess.

## Workflow

1. Prepare or read the plan.
   - Identify the design, contracts, invariants, and semantics that implementation must preserve.
   - Identify what must not be guessed during implementation.

2. Prepare or read the implementation checklist.
   - Split work into gates or chunks.
   - Keep the checklist operational, with exit criteria and validation surfaces, not aspirational.

3. Choose the next chunk.
   - Pick one coherent behavioral, contract, or validation unit.
   - Prefer chunks that are independently reviewable and testable.
   - Define in-scope work, explicit non-goals, invariants, validation, and test posture.

4. Write the implementer prompt.
   - Write one surgical prompt for the chosen chunk only.
   - Save official next-chunk prompts beside the companion plan/checklist unless the user explicitly asks for chat-only output or gives another path.
   - Do not save ad hoc follow-up/fix prompts unless explicitly asked.
   - Re-read any saved official prompt before finishing.

5. Review implementation.
   - Inspect the actual diff target. Use `git diff --cached` for staged review unless the user asks for another target.
   - Compare the diff to frozen docs, the prompt, and scope boundaries.
   - Start with findings ordered by severity. If no findings exist, start with a clear acceptable verdict.

6. Resolve ambiguity through docs.
   - If docs allow multiple interpretations, identify the exact missing decision.
   - Clarify or request clarification before writing a fix prompt.
   - Do not leave important contract clarifications only in chat when docs should be updated.

7. Define the next handoff.
   - Continue only after the current chunk is accepted, corrected, or blocked on a documented ambiguity.

## Chunk Sizing

A good chunk:

- has one clear purpose
- covers one coherent behavioral or contract unit, even if it touches several files
- has obvious subsystem ownership
- has explicit non-goals
- can be validated without finishing the whole feature
- has validation commands that prove the unit, not just an internal helper edit
- is large enough to justify a separate prompt/review cycle

Prefer merging adjacent checklist items when they:

- belong to the same user-visible behavior or contract surface
- use the same helper layer or narrow subsystem slice
- will be reviewed with the same mental model
- are validated by the same build, test, or manual verification commands
- do not introduce separate rollback or contract-risk boundaries

Prefer splitting work when:

- one part freezes or changes an API, schema, persistence format, route, or event contract and another only consumes it
- validation commands or runtime verification differ materially
- restart, migration, upgrade, rollback, or safety risk deserves isolated review
- one part is still ambiguous while adjacent work is already frozen

Do not create separate prompts just because a second file is touched, a helper is extracted, or a checklist has multiple bullets that share the same risk and validation surface.

## Checklist Artifacts

When asked to write or update an implementation checklist from a plan, make it operational enough for fresh implementer sessions. Include, as applicable:

- gate or chunk ID/name
- purpose and scope
- explicit non-goals
- frozen contracts, invariants, exact fields, routes, states, event semantics, persistence formats, and error mappings
- implementation tasks grouped by behavior or contract surface
- exit criteria
- validation commands or validation posture
- test posture: extend existing tests, add minimal local tests, or no new test harness
- ambiguity/blocker notes with the exact missing decision

Do not promote every plan bullet into a separate chunk. Apply the merge/split rules before finalizing the checklist.

## Prompt Map Artifacts

When asked to write or update a prompt map, map checklist gates/chunks to implementer prompt artifacts. Include, as applicable:

- chunk ID/name
- purpose
- planned implementer prompt output path
- input docs/artifacts the implementer needs
- scope summary
- validation posture
- readiness status: ready, blocked, or needs clarification
- blocker or ambiguity if not ready
- sequencing notes and dependencies

Use the prompt map to preserve sequencing for the planner/reviewer. Do not include the prompt map in downstream implementer prompts by default unless the chosen chunk uses prompt-map readiness or may update the prompt map.

## Implementer Prompt Rules

Before writing the prompt:

- verify the correct next chunk from the checklist, prompt map, recent git history, and current worktree state when available
- state which chunk you chose and why
- stop if an ambiguity blocks an implementer-quality prompt
- include current-state context only to the extent needed for the implementer to execute the chunk without relying on prior chat memory

Keep the implementer `Read these first:` list focused:

- include project instruction/context docs required by the repo
- include the feature plan
- include the implementation checklist only when it adds chunk-relevant boundaries or state not restated in the prompt
- include chunk-specific docs/artifacts the implementer actually needs
- do not include the prompt map by default; it is mainly a planner/reviewer sequencing artifact
- do not include planner/reviewer process docs by default
- do not list the prompt file itself in its own `Read these first:` block

Use this prompt structure unless the user explicitly requests a different shape:

```text
Repo root:
Read these first:
Task:
Scope for this pass:
Do not implement or widen in this pass:
Requirements:
Implement in this pass:
Important invariants:
Validation:
Test posture:
If anything is ambiguous, stop and ask instead of guessing.
```

Every implementer prompt must carry these default requirements unless the task packet explicitly overrides them:

- Make a plan first and keep it updated.
- Follow the frozen contracts from the plan exactly.
- Do not use placeholders such as "same as today" for contract behavior in code or tests.
- Keep the implementation clean; do not add legacy fallbacks, dual-format parsing, migrations, compatibility paths, or auto-fallback heuristics unless explicitly in scope.
- Preserve existing behavior for unaffected flows.
- Use surgical diffs only.
- Stay inside the declared scope and non-goals.
- Run the listed validation.
- Summarize changed files, validation results, blockers, ambiguities, and residual risk.
- If anything is ambiguous, stop and ask instead of guessing.
- Propose a one-line commit message for the chunk; if a chunk ID exists, start the message with that exact prefix.

For contract-heavy chunks, add a contract checklist and self-audit requirement:

```text
Before coding, extract the explicit MUST / invariant / field / response-shape / event / error-mapping requirements into a concrete checklist.
After coding, audit the actual diff against that checklist before finishing.
In the final summary, include a contract verification matrix and explicitly confirm no out-of-scope work was included.
```

End implementer prompts by asking for a proposed commit message. When a chunk ID exists, require the commit message to start with that chunk ID prefix.

When the user asks the planner for a commit message, output a one-line commit message. If multiple sentence-like clauses are needed, separate them with semicolons.

## Prompt Output Hygiene

Before presenting or saving a prompt:

- verify every artifact the task depends on or may update appears in `Read these first:` or is restated in the prompt
- include the prompt map only if the chunk uses prompt-map readiness or may update the prompt map
- use one `Repo root:` line, then prefer repo-relative paths
- keep each `Read these first:` path on one physical line
- keep validation commands copy-paste executable and on one physical line
- avoid hard-wrapped paths, quoted patterns, and command arguments
- split long search patterns into multiple commands instead of wrapping them
- put `git log` options before any `--` pathspec separator
- include `git diff --check` for prompts that may edit docs or code, unless a no-diff-check reason is explicit
- when companion docs may be untracked, require `git status --short` plus readback or explicit untracked-file verification
- keep scope, out-of-scope work, validation, test posture, output path, and save behavior aligned with the user request
- do not add a visible prompt-lint report unless asked

## Review Rules

When asked to review staged work:

1. Inspect `git diff --cached` unless another diff target is requested.
2. Read the frozen docs, prompt, and relevant code context.
3. Compare the actual diff against the declared scope and contracts.
4. Verify the implementer's self-audit claims against the actual diff, not just the final summary.
5. Lead with findings ordered by severity.
6. If there are no findings, state that clearly first.

For each finding, state:

- what is wrong
- where it is wrong
- why it matters
- whether the docs are clear enough to fix it without guessing

After findings, state:

- whether the implementation is acceptable
- residual risk
- whether the prompt was insufficient or the implementer failed to follow it, when that distinction is supported
- brief change summary
- recommendation for the next chunk

If blockers exist and the docs are clear enough to fix them, include a surgical follow-up prompt in chat by default unless the user explicitly asked for review only or asked to defer prompt-writing.

## Completion Check

Before ending a planner/reviewer turn, verify:

- requested artifacts were written to disk when saving was expected
- official prompts are in the companion plan/checklist directory unless another path was requested
- filenames follow local feature naming conventions when such conventions are discoverable
- saved official prompt contents were re-read
- required workflow artifacts were not left only in chat
- the next chunk can be described from docs and handoff artifacts without relying on prior session memory

If anything is ambiguous, stop and identify the exact missing contract decision instead of guessing.
