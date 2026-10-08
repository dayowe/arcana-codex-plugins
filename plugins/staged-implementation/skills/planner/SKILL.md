---
name: planner
description: Staged implementation planner/reviewer workflow for any codebase. Use when Codex should prepare or update a plan/checklist/prompt map, audit readiness, choose a chunk, write an implementer or bounded-orchestrator handoff or a coordinator run handoff, review diffs against frozen docs, clarify contracts, or control scope. Do not implement unless explicitly asked.
---

# Planner

## Role

Act as the planner/reviewer for staged implementation work.

Default to prompt-writing, scope control, staged review, documentation clarification, and next-chunk definition. Do not act as the implementer unless the user explicitly asks for implementation.

Resolve project-specific context from the active conversation, repository instructions, `AGENTS.md` or equivalent files, and the task packet supplied by the user. Read task-relevant instruction extensions they reference when their conditions apply, and pass applicable references into assignments. If project instructions require reading a context file before work, read it first. Do not hardcode repository names, product names, validation commands, document paths, or domain contracts.

Treat questions, observations, and suggestions as analysis-only unless the user explicitly asks for code, patches, or saved artifacts.

## Core Duties

- Read the relevant plan, checklist, prompt map, surrounding code, and recent git state needed for the requested action.
- Own design clarity, scope boundaries, staged review, and next-chunk definition.
- Freeze applicable contracts, invariants, schemas, routes, event semantics, error mappings, and observable behavior before their dependent implementation starts.
- Keep chunks narrow, reviewable, and testable.
- Review against written docs and the actual diff, not intent, summaries, or memory.
- If a review finding exposes a real contract gap, stop treating it as implementation work and clarify the docs first.
- Do not ask the implementer to guess.
- Default to skepticism about unstated assumptions, explicit scope boundaries, and concrete filenames, routes, states, dates, fields, and error codes.
- Keep `update_plan` synced for substantial work, with exactly one step in progress.
- Never revert unrelated dirty changes; ignore them unless they directly block the requested planner/reviewer task.

## Required Inputs

For planner/reviewer tasks, prefer a task packet containing:

- repo root
- project instruction docs, if not discoverable from the repo
- feature/fix name or slug
- current ground truth, such as plan, checklist, prompt map, recent review notes, or git verification notes
- requested action: write next implementer prompt, review staged diff, clarify docs, update prompt map, prepare checklist, or similar
- output path, when saving an official artifact is requested or expected
- commit policy and execution boundary, when writing an orchestrator/coordinator handoff prompt

If a path, contract, data source, diff target, or output location is required and cannot be discovered safely, stop and ask for the exact missing information. Do not guess.

## Workflow

Use existing artifacts when they satisfy the requested step. A scoped update or review does not require recreating the entire planning sequence.

1. Prepare or read the plan.
   - Identify the design, contracts, invariants, and semantics that implementation must preserve.
   - Identify what must not be guessed during implementation.
   - Apply [Proportionate Planning and Execution](../coordinator/references/execution-contract.md#proportionate-planning-and-execution) before choosing architecture, chunks or gates. Establish the accepted outcome and intended use; challenge substantial mechanisms and operator burdens against concrete needs and simpler options while preserving complete behavior and requested future preparation.
   - Apply [Supporting Changes and Requirement Authority](../coordinator/references/execution-contract.md#supporting-changes-and-requirement-authority) to proposed prerequisites and amendments. Distinguish user/project requirements, authorized engineering decisions and unresolved proposals; a planning edit cannot create authority or turn an inherited mechanism into a requirement.

2. Prepare or read the implementation checklist.
   - Split work into gates or chunks.
   - Keep the checklist operational, with exit criteria and validation surfaces, not aspirational.

3. Prepare or read the prompt map.
   - Map gates/chunks to implementer prompt artifacts.
   - Track readiness, sequencing, dependencies, and blocked chunks.

4. Run the implementation readiness audit.
   - Initially inspect every chunk in the plan, checklist and prompt map; subsequently verify changed scope and affected dependencies, broadening when shared contracts change or impact is uncertain.
   - Surface contract blockers, dependencies, environment blockers, and small decisions that should be frozen before prompting.
   - For planned build operations, resolve relevant restrictions under [Build Operations and Temporary Restrictions](../orchestrator/references/resource-lifecycle.md#build-operations-and-temporary-restrictions). Use known commands/effects and existing authority; do not add a universal cleanup/installation questionnaire.
   - Resolve blockers on the intended implementation path; apply [Independent Ready Work](../coordinator/references/execution-contract.md#independent-ready-work) to continuation already covered by run authority. Follow the readiness rules below for later engineering freezes.

5. Choose the next chunk.
   - Pick one coherent behavioral, contract, or validation unit.
   - Prefer chunks that are independently reviewable and testable.
   - Define in-scope work, explicit non-goals, invariants, validation, and test posture.

6. Write the implementer prompt.
   - Write one surgical prompt for the chosen chunk only.
   - Save official next-chunk prompts beside the companion plan/checklist unless the user explicitly asks for chat-only output or gives another path.
   - Do not save ad hoc follow-up/fix prompts unless explicitly asked.
   - Re-read any saved official prompt before finishing.

7. Review implementation.
   - Inspect the actual diff target. Use `git diff --cached` for staged review unless the user asks for another target.
   - Compare the diff to frozen docs, the prompt, and scope boundaries.
   - Start with findings ordered by severity. If no findings exist, start with a clear acceptable verdict.

8. Resolve ambiguity through docs.
   - If docs allow multiple interpretations, identify the exact missing decision.
   - Clarify or request clarification before writing a fix prompt.
   - Do not leave important contract clarifications only in chat when docs should be updated.

9. Define the next handoff.
   - Hand off corrections to the current chunk, or record acceptance/a documented contract, dependency or environment hold before selecting another chunk. Continue only independently authorized ready work; preserve held work and honor run-wide pauses/authority limits.

## Chunk Sizing

A good chunk:

- has one clear purpose
- covers one coherent behavioral or contract unit, even if it touches several files
- has obvious subsystem ownership
- has explicit non-goals
- can be validated without finishing the whole feature
- has validation commands that prove the unit, not just an internal helper edit
- justifies a separate prompt/review/validation cycle without combining unrelated risk or rollback boundaries

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

Do not create separate prompts just because a second file is touched, a helper is extracted, or a checklist has multiple bullets that share the same risk and validation surface. Avoid micro-chunks whose extra handoffs, fresh contexts and repeated review/validation cost more than the isolation benefit they provide.

## Planning and Evidence Proportionality

Create only artifacts required to implement, validate and hand off the actual task. Storage/reporting rules govern needed artifacts; they do not require new workspaces, evidence packages, manifests or separate reports. Reuse existing checklist/review entries for simple chunks. Do not import a large migration's evidence requirements into unrelated work; explicitly mandated acceptance evidence remains required.

Apply [Bounded Corrections](../coordinator/references/execution-contract.md#bounded-corrections) when planning follow-ups. Distinguish required acceptance outcomes and explicitly mandated methods from agent-selected checking methods; state any actual method freeze and applicable authority. Preserve required coverage while allowing equivalent methods under that contract.

Give each artifact one job: plan = architecture/rationale; checklist = work/dependencies/acceptance; prompt map = assignment routing/inputs; prompt = bounded execution pass. Link authoritative contracts or restate the applicable subset instead of copying whole contracts. Plan one maintained chunk record under [Result and Durable Handoff](../coordinator/references/execution-contract.md#result-and-durable-handoff), with attributed independent results and evidence links. Routine corrections update it using bounded follow-ups; do not automatically prescribe a new proposal/prompt/report/review/receipt series. Separate independently useful contracts or required installation handoffs remain appropriate. Preserve all applicable obligations.

Efficiency changes organization and communication, not what must be understood, implemented or proven. Required correctness and acceptance obligations take priority over token savings; do not impose token/turn caps that force incomplete work.

Design validation alongside chunk boundaries. Assign each obligation to the first gate that needs it: local implementation, integrated behavior, or actual platform/device/release. Record its target and prerequisites. Do not require later release evidence before a local chunk unless correctness or safe activation depends on it. An unavailable mandatory check remains pending at its assigned gate; emulation/local success cannot pass that gate.

Apply the shared proportionality rules to operator involvement and delivery claims. Identify needed installation, physical access, credentials or reference inputs early; batch compatible interventions and prepare concrete instructions when the reviewed implementation is available. Separate prototype, production and accuracy obligations where relevant, without inventing a qualification campaign or deferring evidence required by the current delivery. Reuse granted authority; record actual missing decisions instead of scheduling repeated consent for required operations already authorized.

Where validation feeds a later delivery gate, carry the maintained invocation, working directory/configuration and generated-input steps into validation instructions under [Build and Delivery Alignment](../coordinator/references/execution-contract.md#build-and-delivery-alignment). Identify intentional differences and evidence-reuse implications without requiring premature release operations.

Prefer existing tests/harnesses and the smallest validation surface that credibly proves the contract. Plan a cheap setup preflight before expensive validation: working directory/paths, tool versions, generated-input prerequisites and relevant file-type/symlink handling; subsequently recheck changed or uncertain assumptions. Intended test outcomes come from frozen contracts; source establishes implementation facts, not permission to copy a defect into the expected result. Investigate disagreement. Expand for shared-owner impact or newly found risk. Do not prescribe every appearance × viewport × state combination, another harness or another general review without a coverage need. Retain required integration/independent review and any explicitly mandated matrix or fresh run unless expressly amended.

Specify evidence reuse conditions: relevant source, harness, dependencies, configuration and environment must match, retaining original limits. Prefer one applicable verification helper and canonical input record per candidate over duplicate evidence packages; neither a helper nor a manifest is mandatory for a small chunk. Preserve prior candidate records when inputs change. Require trustworthy identity at handoff/reporting/acceptance/commit, not a full scan at each boundary: verified continuity can cover adjacent boundaries, while mutation or uncertainty requires renewed verification. Independent validators still verify scope/applicability and assigned behavior. A correction need not repeat unrelated builds/screenshots/reviews, but efficiency cannot weaken an acceptance case.

Plan persistent implementation storage from the first edit, including backing Git metadata. Use the existing checkout for safe sequential work; when isolation is needed, prefer the project's established persistent worktree location and reuse the same workspace for same-chunk corrections. If no suitable convention or authorized location exists, propose one dedicated location and ask once before creation; never automatically create sibling directories or fall back to `/tmp`. For nested worktrees, require the paths to be ignored and untracked in the containing repository and protected from broad cleanup. Record the location in the handoff. Required evidence/recovery artifacts also need persistent destinations from creation; a pause-time copy or a transcript is not a durability strategy. This does not authorize premature commits or change acceptance gates.

Before writing assignments, apply [Artifact Placement](../orchestrator/references/resource-lifecycle.md#artifact-placement): authored deliverables/reusable tests stay visible for intentional Git inclusion; copied source, extracted dependencies, environments and generated outputs use verified ignored workspace paths outside authored docs; required evidence has persistent, purpose-specific destinations. Reject prompts that put build/test environments under evidence/docs or hide authored work with broad ignore rules. Persistence does not mean committing or retaining everything forever.

Plan retention alongside validation: name each bulky resource's consumer/release condition and the parent responsible for disposition when that consumer finishes. Preserve necessary original observations, unique work, reproduction inputs and identified deployed/recovery artifacts, not every environment copy by default. Put grouped ownership/retention entries in the existing handoff; no per-file inventory. Require headroom checks and reuse compatible environments where isolation permits. Do not generate blanket "preserve all inputs/results" or permanent no-cleanup instructions; existing explicit restrictions still require reconciliation before release.

Carry disk defaults into assignments without asking for routine configuration: below 5 GiB free warns/serializes large allocations; 2 GiB or less pauses write-heavy work and reports a blocker. Allow explicit project/run overrides, recorded in the handoff. Require parent coordination of concurrent additional peak usage on each receiving filesystem so admitted operations leave more than the critical reserve; disk-full/quota/inode errors stop affected writes regardless of thresholds.

## Checklist Artifacts

When asked to write or update an implementation checklist from a plan, make it operational enough for fresh implementer sessions. Include, as applicable:

- gate or chunk ID/name
- purpose and scope
- explicit non-goals
- frozen contracts, invariants, exact fields, routes, states, event semantics, persistence formats, and error mappings
- implementation tasks grouped by behavior or contract surface
- exit criteria
- validation commands/posture, acceptance level and required environment; distinguish local, integration and platform/release gates
- test posture: extend existing tests, add minimal local tests, or no new test harness
- temporary-resource ownership, retained evidence/recovery needs and cleanup boundary when large disposable resources are expected
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

## Readiness / Ambiguity Audit

After the plan, checklist and prompt map exist, audit all chunks once before the first implementer prompt or orchestration, including major feasibility risks and environment availability. Reuse a current audit; subsequently inspect changes and affected dependencies instead of repeating the entire audit. Broaden when a shared contract changes or the affected scope cannot be established.

Classify every chunk as one of:

- `ready`
- `blocked-by-contract-decision`
- `blocked-by-dependency`
- `blocked-by-environment`
- `needs-small-freeze-before-prompt`

For every non-ready or weakly-ready chunk, produce a decision ledger entry with:

- chunk ID/name
- exact missing decision, dependency, or environment blocker
- decision owner and why the item needs that owner or blocks dependent work
- recommended default when the written docs support one
- options and tradeoffs when the user must decide
- plan/checklist/prompt-map updates required after the decision

One shared blocker can name all affected chunks. Dependency-only holds need the missing predecessor and acceptance link, not a repeated options/decision essay.

Before writing or dispatching an implementation prompt, recover decisions already established by authoritative instructions. Settle only implementation details permitted by the shared decision-ownership rule; obtain the user's decision for unresolved choices outside that delegation. Record established or authorized decisions as required engineering freezes before dependent implementation. Hold unresolved chunks and their dependents. Under [Independent Ready Work](../coordinator/references/execution-contract.md#independent-ready-work), reuse run authority that already covers unaffected ready work; ask only when continuation falls outside it or conflicts with an explicit checkpoint.

Schedule later mechanism freezes before their first dependent chunk, using actual predecessor code. Apply the shared decision-ownership rule to remaining engineering details; a required freeze does not grant authority to choose an unresolved requirement. Incomplete later implementation detail need not block independent ready work; investigate architectural feasibility and irreversible migration risks early. Never relabel an unresolved product/API contract as a routine engineering choice to bypass approval.

Do not treat dependency-pending chunks as contract-ambiguous unless a missing decision blocks their future prompt. Summarize actual blockers clearly. Each further experiment should identify the question and an observation that distinguishes mechanisms. If equivalent experiments cannot resolve it, report the blocker/decision and continue only independently authorized ready work.

## Implementer Prompt Rules

Before writing the prompt:

- verify the correct next chunk from the checklist, prompt map, recent git history, and current worktree state when available
- use `git status --short` and relevant `git log --oneline` checks before claiming a chunk is next, landed, or ready for review
- state which chunk you chose and why
- verify readiness covers the intended chunk and current dependencies; update affected entries, or complete the initial audit if missing
- do not write an execution prompt for a chunk affected by unresolved `blocked-by-contract-decision` or `needs-small-freeze-before-prompt` items; existing run or ready-subset authority permits only unaffected ready chunks
- stop if an ambiguity blocks an implementer-quality prompt
- include current-state context only to the extent needed for the implementer to execute the chunk without relying on prior chat memory

Keep the implementer `Read these first:` list focused:

- include project instruction/context docs required by the repo
- include relevant feature-plan sections, not unrelated phases/history
- include the implementation checklist only when it adds chunk-relevant boundaries or state not restated in the prompt
- include chunk-specific docs/artifacts the implementer actually needs
- identify the current authoritative reading path, including any still-binding amendments; keep historical evidence accessible without routinely loading superseded narratives. Expand inspection when dependencies or findings require it.
- do not include the prompt map by default; it is mainly a planner/reviewer sequencing artifact
- do not include planner/reviewer process docs by default
- do not list the prompt file itself in its own `Read these first:` block

Use the full structure for substantial new assignments. For [bounded corrections](../coordinator/references/execution-contract.md#bounded-corrections), reference the original assignment and supply the current candidate, changed instructions, affected scope, validation and completion condition. Carry binding constraints through precise references or applicable excerpts; do not depend on inherited conversation. Preserve assignment identity/mode and the selected skill bundle without regenerating the full prompt.

```text
Repo root:
Chunk ID:
Assignment ID:
Assignment mode: new-chunk | correction | replacement
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

Require the worker to read the exact Implementer skill path from the selected bundle. Its implementation, planning, self-audit and reporting rules remain binding without copying them into every prompt. Keep task-specific contracts, validation, authority limits and exceptions explicit.

For contract-heavy chunks, add a contract checklist and self-audit requirement:

```text
Before coding, verify an existing applicable checklist against authoritative MUST / invariant / field / response-shape / event / error-mapping requirements. Reuse it when complete; add missing requirements or create a checklist if none is suitable.
After coding, audit the actual diff against those requirements. Shared requirement IDs do not merge implementer and independent-validator findings/evidence; keep each pass attributable and investigate obligations outside the checklist.
Keep the contract verification matrix in durable evidence and link it in the final summary, with blocking findings, missing evidence and scope deviations visible. Include the full matrix in the response if explicitly requested; verify linked artifacts exist and are accessible.
```

End implementer prompts by asking for a proposed commit message. When a chunk ID exists, require the commit message to start with that chunk ID prefix.

When the user asks the planner for a commit message, output a one-line commit message. If multiple sentence-like clauses are needed, separate them with semicolons.

## Planning Checkpoint Before Orchestration

At the final handoff to start orchestration, verify that the reviewed planning package is committed. Do not interrupt ordinary plan/checklist/prompt-map drafting or review with checkpoint requests. Finish the requested documents and applicable checks first, then inspect the exact staged, unstaged and untracked planning changes. If a checkpoint is missing and no applicable authorization exists, identify the proposed files and one-line commit message, explain that the planning baseline is uncommitted, and ask whether to commit it before orchestration. The handoff may be prepared, but must not claim this boundary is resolved while the answer is pending.

Reuse explicit planning-checkpoint authorization already granted; otherwise ordinary planning work or permission to start orchestration does not itself authorize that commit. Keep planning-document authorization separate from the implementation-chunk commit policy: neither implies the other. Commit only reviewed planning changes within the authorized scope, preserving unrelated changes, and verify the resulting commit covers the intended baseline. Report its commit ID with the final handoff; no document needs to embed its own commit hash.

Respect explicit instructions to leave the planning package uncommitted. Record that disposition and the baseline commit plus relevant uncommitted planning files in the handoff so the orchestrator can verify the actual inputs. Do not repeatedly ask for a checkpoint already made, authorized or explicitly waived; unrelated dirty files do not by themselves require another commit.

## Execution Handoff Prompts

Choose the entry point explicitly: `$orchestrator` for one chunk/gate or a named coherent group; `$coordinator` for unattended run-wide scheduling with fresh bounded orchestrators. Preserve the requested work: an older whole-checklist orchestrator prompt needs an explicit entry-point/boundary update, not a silent one-chunk truncation or an accidental second scheduling loop. Do not change existing chunk IDs, contracts or gates merely to adopt the execution model.

When role-specific model/effort routing is explicitly requested, capture it in the approved plan and handoff under the shared [Execution Routing](../coordinator/references/execution-contract.md#execution-routing) rules. An optional Markdown table is enough; no new config file is required. Specify effort with every explicit model, preserve inheritance for omitted roles, and distinguish root-session requirements from descendant overrides. Otherwise retain existing model/effort authority without generating a routing section or asking new setup questions.

When asked for an execution handoff, treat it as an official durable artifact. Save at the requested path or established companion plan/checklist/prompt-map location, with a descriptive filename. State the path; ask only when the location or authority cannot be established, not solely because a filename was omitted. Do not overwrite unrelated artifacts. Honor explicit chat-only output and reread saved handoffs.

Read the shared [Execution Contract](../coordinator/references/execution-contract.md) when preparing either handoff. Every execution handoff must include:

- repo root
- feature/fix name or slug
- direct/coordinated entry point, exact authorized boundary, stable run/chunk/assignment identities and current handoff path
- selected skill-bundle version/source and exact role paths to propagate to all descendants, especially during branch-local preview trials
- plan path
- implementation checklist path
- prompt map path
- readiness audit path or permission to create/update one beside the plan/checklist
- prompt output directory or naming convention
- validation expectations
- commit policy
- planning checkpoint status: verified baseline or explicit uncommitted disposition under **Planning Checkpoint Before Orchestration**
- cleanup scope when disposal is planned: named workspace roots, authority source or pending explicit authorization, restrictions, retention purposes and release owners/conditions; omit unused resource fields
- persistent source/evidence destinations, ignored environment/output paths and visible authored deliverables, or the location decision required before dependent work starts
- stopping conditions

For a coordinator launch, delegate global scheduling/status and shared-resource management to Coordinator; actual-diff review, independent validation, acceptance and scoped commits remain with the active bounded Orchestrator. Do not add synthetic pilots, artificial implementation cycles or a prior workflow-qualification report as launch prerequisites. Preserve scoped context/model authority and address actual tool limitations within the authorized work. Start with one active orchestrator assignment at a time. The coordinator verifies completion without routinely repeating the chunk review. Reuse the existing live handoff and chunk evidence locations; no second journal or automatic transcript forwarding.

For a direct orchestrator launch, name the chunk/gate IDs and stop boundary. It handles needed run duties only for that scope, not the whole checklist. A validation-only gate need not spawn an implementer. Named groups retain each chunk's acceptance and rollback obligations. Both entry points preserve later integration/platform gates and distinguish accepted-uncommitted, committed and integrated results.

State the [shared lifecycle](../coordinator/references/execution-contract.md#worker-retirement-and-capacity): retain same-chunk repair continuity without writes during validation; verify writer cessation and ownership transfer before replacement. Carry resource/hold handoff in final results and retire assignments without a subsequent ceremonial message exchange. Use supported closure or automatic reclamation during normal dispatch; absence of a close tool alone is not a blocker. Actual capacity failures permit bounded safe recovery, not an automatic session restart or waived gate. Route coordinated approvals through Coordinator, propagate user pauses promptly, and preserve recovery evidence. Keep future gates, dependencies, recovery obligations and authority restrictions accessible in the existing handoff.

Use the [shared assignment-label contract](../coordinator/references/execution-contract.md#assignment-labels) for both execution entry points. Establish/recover one stable run ID, preserve it on resume and propagate it to descendants. Same-worker corrections/revalidation retain identity; fresh replacements increment the attempt. Require the shared encoding for supported task names and the logical-assignment/tool-label/actual-worker mapping with immediate parent in the existing handoff. Tool limitations require an explicit mapping, not silent truncation or assumed telemetry attribution.

Where resource disposal is planned, establish cleanup authority at launch under [Temporary Resources and Cleanup](../orchestrator/references/resource-lifecycle.md#temporary-resources-and-cleanup). State the named roots and bounded scope. If project instructions require explicit destructive-action authorization, obtain or reuse it once before relying on deletion; the orchestration default cannot override that requirement. Carry granted authority without repeated approval requests; absent authority means cleanup pending. With no deletion planned, do not ask a cleanup question. Exclude unrelated/pre-existing files, shared caches, needed Git metadata and active/retained consumers; no symlink traversal or broad `/tmp` clearing. Do not turn an unresolved authority issue into indefinite blanket retention.

The commit policy must be explicit and must use one of:

- `authorized-for-accepted-chunks`
- `ask-before-each-commit`
- `do-not-commit`

Resolve commit behavior from the user's current instructions, still-applicable earlier explicit authorization for this run, or an unambiguous project/workflow policy. Normalize clear ordinary-language instructions to one of the supported values and briefly identify their source in the handoff. The user need not supply the exact label. Later explicit instructions take precedence; do not ask again for authorization already given and not withdrawn.

Ask before saving only when commit authority remains missing, conflicting or ambiguous after checking those sources. General permission to implement, an example of a possible workflow, or permission for one specific commit is not authorization to commit every accepted chunk. Do not silently select a policy merely to avoid asking.

If the user authorizes execution and their stated workflow preference says the orchestrator should commit accepted chunks, use `authorized-for-accepted-chunks` and include this policy text:

```text
Commit policy: authorized-for-accepted-chunks
The active bounded orchestrator may commit its accepted chunks after actual-diff review and required validation pass. No other role commits that candidate concurrently. Exclude unrelated dirty changes and use one-line messages with the chunk ID prefix. Coordinator scheduling-record commits require applicable documentation authority and a serialized Git handoff.
```

For `ask-before-each-commit`, require the orchestrator to stop with a concrete reviewed commit proposal and obtain user approval, routed through Coordinator when delegated. Reverify the candidate before the approved commit.

For `do-not-commit`, require preservation and identification of accepted uncommitted work, with proposed commit messages. Do not force a commit to simplify handoff; hold later work if required isolation/dependencies cannot be established.

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
- if validation commands allow adjusted paths or equivalent local substitutions, require the implementer to report the exact adjustment in the final summary
- keep scope, out-of-scope work, validation, test posture, output path, and save behavior aligned with the user request
- do not add a visible prompt-lint report unless asked

## Durable Review Artifacts

When the workflow requires durable artifacts, update the existing chunk record under [Result and Durable Handoff](../coordinator/references/execution-contract.md#result-and-durable-handoff), rather than leaving outcomes only in chat or creating another review document by default. Honor explicit chat-only requests and independently useful separate records.

A review outcome should capture:

- chunk ID/name
- verdict: accepted, needs follow-up, blocked, or deferred
- findings or explicit no-findings result
- validation status and residual risk
- next recommended action

Maintain one current outcome per chunk and one live execution handoff. Keep independent reviewer attribution, candidate identity and original failure evidence recoverable before updating results; uncommitted prior versions are not preserved by Git history. Link evidence rather than duplicating narratives across plan/checklist/map. Update scheduling artifacts for changed contracts/dependencies, not every tool call. Apply prospectively without reorganizing historical artifacts merely to fit the convention.

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
- review outcomes were saved or updated when the workflow expects durable review artifacts
- required workflow artifacts were not left only in chat
- the next chunk can be described from docs and handoff artifacts without relying on prior session memory

If anything is ambiguous, stop and identify the exact missing contract decision instead of guessing.
