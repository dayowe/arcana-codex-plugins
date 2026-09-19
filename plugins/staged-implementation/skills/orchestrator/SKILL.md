---
name: orchestrator
description: "End-to-end staged implementation orchestrator for any codebase. Use when Codex should run a planner-reviewer-implementer-validator loop across chunks: audit readiness and ambiguities before implementation, choose the next prompt from a plan/checklist/prompt map, delegate implementation to a fresh sub-agent using the implementer role, review actual diffs against frozen contracts, invoke validator evidence checks when needed, issue follow-up prompts until accepted or blocked, optionally commit accepted chunks when explicitly authorized, and continue until the checklist is complete."
---

# Orchestrator

## Role

Act as the coordinator for staged implementation work. Own the loop; do not become the implementer unless the user explicitly asks for local implementation.

Resolve project-specific context from the active conversation, repository instructions, `AGENTS.md` or equivalent files, and the task packet supplied by the user. If project instructions require reading a context file before work, read it first. Do not hardcode repository names, product names, validation commands, document paths, or domain contracts.

Use sub-agents only when the user explicitly requests orchestration, delegation, sub-agents, or an automated implementation loop. If sub-agent tools are unavailable, stop and explain that the loop can be run manually with planner/implementer handoffs.

## Required Inputs

Prefer a task packet containing:

- repo root
- project instruction docs, if not discoverable from the repo
- feature/fix name or slug
- stable chunk IDs and a run-state/handoff path, or permission to create one beside the plan/checklist
- plan path
- implementation checklist path
- prompt map path
- readiness audit path or permission to create/update one beside the plan/checklist
- prompt output directory or naming convention
- validation expectations
- commit policy: `authorized-for-accepted-chunks`, `ask-before-each-commit`, or `do-not-commit`
- cleanup scope/restrictions and retained evidence/recovery requirements, using the routine run-owned default below when temporary resources are used
- stopping conditions

Resolve required paths and output locations from explicit instructions and established repository/workflow conventions first. For saved prompts, use the identified companion plan/checklist location and a descriptive filename if no narrower convention exists; state the chosen path and do not overwrite unrelated artifacts. Ask when a required location cannot be established or conflicting instructions remain. Do not guess unresolved contracts, data sources or validation targets.
Resolve and normalize commit authorization under **Commit Rules** before implementation. A missing literal policy label does not require clarification when existing instructions already establish its meaning.

## Authority Boundaries

- The orchestrator owns chunk selection, prompt writing, review, follow-up prompts, validation decisions, and commits.
- Implementer sub-agents own one scoped implementation pass at a time.
- Implementer sub-agents must not commit.
- Validator sub-agents or validator passes gather evidence only; they do not decide final acceptance.
- Commit only according to the explicit commit policy.
- Do not push, reset, discard, or revert unrelated changes unless explicitly asked.
- Stop on contract ambiguity instead of pushing an implementer to guess.

## High-Level Loop

Before delegating implementation, run a readiness preflight:

1. Establish current state.
   - Read project instructions first.
   - Establish context from the plan, checklist, prompt map, current handoff/review and relevant git/worktree state. On continuation, read changed instructions/contracts and affected scope rather than reloading unchanged history.
   - Before delegating edits, establish persistent implementation and evidence locations under **Resource Lifecycle**. On resume, verify the actual candidate files and backing Git metadata before using previous reports or continuing dependent work.
   - Before initial implementation delegation, verify the reviewed planning package against its checkpoint and actual working files. If relevant planning changes remain uncommitted, honor an explicit uncommitted disposition or applicable planning-checkpoint authorization; otherwise finish applicable document checks, identify the exact files and proposed commit, and ask before proceeding. Planning-checkpoint permission and accepted-chunk commit permission are separate. Exclude unrelated changes; do not repeat a resolved checkpoint request or turn routine execution-status updates into a new planning checkpoint gate. Record the verified baseline, including relevant uncommitted inputs when explicitly allowed.
   - Read any existing readiness audit. If no readiness audit exists, create one before writing the first implementer prompt.
   - Initially classify every chunk as `ready`, `blocked-by-contract-decision`, `blocked-by-dependency`, `blocked-by-environment`, or `needs-small-freeze-before-prompt`. Subsequently verify affected entries/dependencies; broaden when a shared contract changes or applicability is uncertain.
   - Surface all contract blockers and small freezes to the user before implementation starts.
   - Stop if any `blocked-by-contract-decision` item remains unresolved, unless the user explicitly authorizes implementing only the `ready` subset while blocked chunks remain held.
   - Hold the affected chunk and dependents if a required engineering freeze cannot be resolved from written docs; apply **Stopping Conditions** to any independent continuation.

For each authorized ready chunk:

2. Select the next chunk.
   - Identify the next ready chunk from the checklist, prompt map, and readiness audit.
   - Verify the chunk has not already landed.
   - Run `git status --short` and relevant `git log --oneline` checks to verify the chosen chunk has not already landed and that the review target matches the current worktree.

3. Write the implementer prompt.
   - Produce one surgical prompt for that chunk only.
   - Give the pass a stable `Chunk ID`, `Assignment ID` and mode under **Run State and Worker Lifecycle**.
   - Save official prompts beside the companion plan/checklist unless the user requests another output path.
   - Re-read the saved prompt before delegating.

4. Delegate implementation.
   - Spawn a fresh implementer sub-agent when possible.
   - Give the sub-agent the saved prompt and explicitly tell it to use `$implementer`.
   - Use a supported task label mapped to the assignment ID under **Run State and Worker Lifecycle**.
   - Pass only the context needed for that chunk.
   - Tell the sub-agent not to commit and to report changed files, validation, blockers, and proposed commit message.

5. Review the result.
   - Inspect the actual diff/worktree, not just the sub-agent summary.
   - Compare against the frozen plan, checklist, prompt, and declared scope.
   - Verify the implementer's self-audit claims against the diff.
   - Lead review with findings ordered by severity.
   - Save or update review outcomes beside the companion plan/checklist when the run is maintaining staged workflow artifacts.

6. Handle review outcome.
   - If contract ambiguity exists, hold the affected chunk and identify the exact missing decision; apply **Stopping Conditions** before continuing other work.
   - If implementation violates the prompt or frozen contracts and the docs are clear, write a surgical follow-up prompt.
   - Reuse the designated repair agent when continuity helps; if a fresh pass is safer, follow the replacement ownership-transfer procedure under **Run State and Worker Lifecycle** before permitting edits.
   - Repeat review/follow-up until accepted, blocked, or stopped by the user.

7. Validate when evidence is required.
   - Run direct validation yourself for simple build, test, or diff checks.
   - Keep direct checks on the same stable-candidate/provenance boundary required below for delegated validation.
   - Invoke `$validator` for feature acceptance, regression, contract, UI/browser, runtime, API, device, or integration evidence when a separate validation pass would reduce risk.
   - Give the validator the exact target, applicable frozen requirements, findings and evidence limits; avoid unrelated planning/history context.
   - Treat validator results as evidence for the orchestrator's acceptance decision, not as acceptance by themselves.

8. Accept the chunk.
   - Confirm required validation passed under the current recorded acceptance contract; handle explicit user-approved exceptions under **Commit Rules**, never as fabricated PASS evidence.
   - Establish that candidate source/diff and relevant build still match the reviewed/validated identity, reusing verified continuity under the provenance rule below where applicable; changes invalidate affected evidence until reconciled and revalidated.
   - Confirm no out-of-scope work remains.
   - Apply the explicit commit policy.
   - Use the chunk's proposed commit message when acceptable; otherwise write a one-line commit message with the chunk ID prefix when one exists.
   - Retire the chunk's workers under **Run State and Worker Lifecycle**, recording any justified retention exception before further delegation.

9. Continue.
   - Update the existing live handoff and the chunk's current outcome; update checklist/map/readiness entries when their state/dependencies change, without copying the running journal into each artifact.
   - Reconcile temporary-resource ownership/retention and perform eligible authorized cleanup under **Resource Lifecycle** before the next large allocation.
   - Choose the next ready chunk.
   - Stop when all chunks are complete, blocked, or no ready chunk remains.

## Run State and Worker Lifecycle

Use the existing durable handoff as one compact continuation record, not a second status system. Update it in place with current chunk/assignment IDs, candidate/baseline identity, worker ownership, unresolved findings/blockers, evidence pointers, retained resources, next ready work and one-line accepted outcomes/commit IDs. Preserve outstanding gates, dependencies, recovery/retention obligations and authority restrictions directly or through clear authoritative links, including obligations needed only later. Do not carry accepted-chunk narratives, full logs or superseded findings into later assignments unless a dependency or regression requires them.

Attribute every worker assignment to a stable chunk and role with a canonical ID such as `<chunk-id>:<role>:<attempt>`. Use implementer modes `new-chunk | correction | replacement` and validator modes `validation | revalidation | replacement`. Same-worker corrections/revalidation retain the assignment ID; a fresh replacement increments the attempt. A next-chunk worker uses the new chunk's ID. Where task labels are supported, use the canonical ID only if valid for that tool; otherwise choose a supported unique label (for example, `o_03_implementer_1` for `O-03:implementer:1`). Record the assignment-to-label/worker mapping once in the handoff; check label collisions and do not assume telemetry can decode an unsupported format.

The implementer may remain available as the same-chunk repair agent during validation, but must not write until the parent releases the validation hold and assigns a correction. Before a replacement edits, establish that the prior worker and its owned operations have stopped writing; verify the actual candidate, transfer relevant findings/resources, retire the superseded assignment and designate the replacement as sole writer. A recorded transfer alone does not establish that writes stopped. An unresolved validation hold still prevents replacement edits.

At acceptance or permanent block, transfer required responsibilities and retire the workers: stop further assigned work, remove them from dispatch and close them when supported. If closure is unavailable, use supported controls to establish inactivity and record retirement; do not claim interruption closed a worker or released its processes/resources. Preserve held source and evidence. Any retention exception needs a purpose and release condition. Safe independent chunks may overlap with recorded ownership and candidate isolation; do not serialize them merely because another worker remains available. Retained workers receive no unrelated next-chunk work. Idle availability alone is not evidence of token consumption.

## Readiness Audit Rules

The readiness audit exists to resolve blockers before implementation, not during the first failed prompt.

Record each chunk's ID and readiness classification. For non-ready chunks, record the applicable details below; reuse a shared blocker entry for affected chunks rather than duplicating its analysis. Dependency-only holds need the missing predecessor and acceptance link, not an options essay.

- chunk ID/name
- readiness classification: `ready`, `blocked-by-contract-decision`, `blocked-by-dependency`, `blocked-by-environment`, or `needs-small-freeze-before-prompt`
- exact missing decision, dependency, or environment blocker
- why an implementer must not decide it
- recommended default when the written docs support one
- options and tradeoffs when the user must decide
- plan/checklist/prompt-map updates required after the decision

Before implementation starts, require one of:

- contract blockers are resolved and the intended chunk's required engineering freezes are recorded in authoritative artifacts
- or the user explicitly authorizes a ready-subset run while unrelated contract-blocked chunks remain held

Complete later mechanism freezes before their first dependent chunk; independent ready work need not await every later implementation detail. Investigate major feasibility/irreversible risks early. Do not spawn implementers for blocked chunks or let them decide missing parent-route, API, persistence, timebase, ownership or cleanup contracts.

Bound further investigation by a question and an observation that distinguishes mechanisms. When equivalent experiments cannot resolve missing contract, access or authority, record one focused decision/blocker and continue only independently authorized ready work.

## Prompt Writing Rules

Every implementer prompt must include:

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

Every implementer prompt must require:

- Make a plan first and keep it updated.
- Follow the frozen contracts from the plan exactly.
- Do not use placeholders such as "same as today" for contract behavior in code or tests.
- Keep the implementation clean; do not add legacy fallbacks, dual-format parsing, migrations, compatibility paths, or auto-fallback heuristics unless explicitly in scope.
- Preserve existing behavior for unaffected flows.
- Use surgical diffs only.
- Stay inside the declared scope and non-goals.
- Run the listed validation.
- Summarize changed files, validation results, blockers, ambiguities, and residual risk.
- Propose a one-line commit message for the chunk; if a chunk ID exists, start the message with that exact prefix.

For contract-heavy chunks, require:

```text
Before coding, verify an existing applicable checklist against authoritative MUST / invariant / field / response-shape / event / error-mapping requirements. Reuse it when complete; add missing requirements or create a checklist if none is suitable.
After coding, audit the actual diff against those requirements. Shared requirement IDs do not merge implementer and independent-validator findings/evidence; keep each pass attributable and investigate obligations outside the checklist.
Keep the contract verification matrix in durable evidence and link it in the final summary, with blocking findings, missing evidence and scope deviations visible. Include the full matrix in the response if explicitly requested; verify linked artifacts exist and are accessible.
```

Keep the implementer `Read these first:` list focused:

- include project instruction/context docs required by the repo
- include relevant feature-plan sections, not unrelated phases/history
- include the implementation checklist only when it adds chunk-relevant boundaries or state not restated in the prompt
- include chunk-specific docs/artifacts the implementer actually needs
- identify the current authoritative reading path, including any still-binding amendments; keep historical evidence accessible without routinely loading superseded narratives. Expand inspection when dependencies or findings require it.
- do not include the prompt map by default
- do not include planner/reviewer/orchestrator process docs
- do not list the prompt file itself in its own `Read these first:` block

## Delegation Rules

For new implementer and independent-validator assignments, default to fresh scoped context without full parent-history inheritance when supported; explicitly select the supported no-history setting rather than relying on tool defaults. Carry the task, applicable instructions/contracts, candidate identity, source locations, known findings, dependencies, validation obligations and authority. Context selection is separate from model selection: explicitly preserve authorized model/effort through supported controls if changing context mode changes defaults; never silently substitute a configuration for smaller context. If scoped context is unavailable, use supported controls while preserving required independence and obligations. Reuse an appropriate worker for bounded same-assignment corrections; use a fresh worker when independence, persistent misunderstanding or stale context warrants it, not because a fixed context threshold was crossed.

Dispatch complete bounded assignments so workers can finish already-authorized routine steps without acknowledgement chatter. Coordinate candidate release, shared-resource acquisition, scope changes and required approvals explicitly; a completion notification does not release a workspace. Prefer completion notifications or interruptible waits, with proportionate polling when necessary. Preserve required user updates and immediate blocker/material-finding reports; do not add a permanent monitoring loop or wait so long that intervention is prevented.

Do not routinely send status-only prompts to a worker with a complete assignment. A wait timeout alone does not justify an acknowledgement or context replay; use notifications, independent safe work or an interruptible wait compatible with required user updates. Perform a proportionate liveness check when expected progress, a tool failure or other evidence suggests the worker is stuck; prefer available status information before prompting. Necessary intervention and blocker reporting remain required.

When spawning an implementer sub-agent:

- start with a fresh sub-agent for each new chunk by default
- reuse the same sub-agent only for follow-up fixes to the same chunk when continuity is useful
- instruct it to use `$implementer`
- instruct it to edit files directly if the runtime supports sub-agent code edits
- instruct it not to commit
- keep the task narrow and self-contained
- assign the verified persistent source workspace and evidence destinations; workers must not relocate implementation into disposable storage
- pass cleanup scope, retention requirements and disk/allocation restrictions; require workers to report exact owned resource paths and outstanding consumers on handoff
- avoid delegating planner/reviewer decisions
- wait only when the result is needed for the next critical-path step

If sub-agent edits are not inspectable as the actual candidate diff, hold that chunk and report the integration limitation instead of reviewing a summary as if it were a diff. Apply **Stopping Conditions** before continuing other work.

When invoking a validator pass:

- instruct it to use `$validator`
- give it the same chunk ID and a validator assignment ID/mode with the supported task-label mapping under **Run State and Worker Lifecycle**
- provide the exact expected behavior, frozen contracts and candidate identity: baseline revision plus scoped changes (including relevant untracked inputs), and the relevant build/artifact and its source provenance
- release implementer writes before validation; prevent overlapping writes to the validated scope, relevant harness/configuration inputs, or replacement of the tested build/target until the pass is released
- provide approved credential or environment sources only when needed
- pass persistent evidence/recovery destinations and identify the durable source behind any disposable validation copy
- pass the run's bounded cleanup scope and any restrictions, retention requirements, disk thresholds and allocation restrictions; require owned-resource handoff
- ask for pass/fail/blocked evidence, residual risk, and untested areas
- do not ask the validator to edit production code or commit
- review the validator's evidence before accepting the chunk

Use lightweight provenance sufficient to identify what was tested, such as a scoped diff/input digest and build identifier; do not require exhaustive repository/dependency hashing by default. Establish identity at validation entry. Verification can cover adjacent reporting, acceptance and commit boundaries when controlled ownership and applicable change checks demonstrate unchanged source, relevant inputs and build; a prior agent's claim alone is insufficient. Do not automatically rescan full inventories at every boundary. Mutation, interruption that makes continuity uncertain, or other identity uncertainty requires renewed verification. If relevant inputs change during a pass, stop affected validation, preserve the old evidence with its limits, and establish the corrected candidate before revalidation. Non-overlapping work is safe only when it cannot alter those inputs, target or results.

## Review and Validation Effort

Efficiency changes organization and communication, not what must be understood, implemented or proven. Required correctness and acceptance obligations take priority over token savings; do not impose token/turn caps that force incomplete work.

Keep roles distinct: implementer self-audit; parent actual-diff/integration/contract review; validator independent checks of assigned behavior/risks. Do not automatically add a general reviewer for each correction. Reuse a validator for bounded corrections while context remains valid; a changed mechanism, disputed finding, new risk or explicit gate may require a fresh challenge. All mandated independent reviews remain required.

For parent review, start with the actual scoped diff, frozen requirements and relevant evidence. Inspect surrounding code, callers and shared behavior as needed to assess impact; do not wait for a discovered defect to justify broader reading. Avoid repeatedly rereading unchanged full files or rerunning successful worker checks merely to restate evidence. Run parent-owned checks when required for acceptance, when worker evidence is missing/stale/uncertain, or when an independent check directly reduces a concrete risk.

Reuse reliable verification helpers and a canonical candidate-scoped input record where useful; verify their coverage/applicability rather than rebuilding them per worker. Keep prior candidate provenance intact when inputs change, and keep concurrent candidates separate. Create only the smallest missing helper when justified, not a mandatory evidence framework. Before expensive checks, preflight relevant paths, tool versions, generated prerequisites and file-type/symlink handling. Recheck changed or uncertain setup; preflight does not replace behavioral assertions. Validators independently verify inputs and expected outcomes against the frozen contract, not an unexamined helper or previous verdict.

For same-contract corrections, reference the original assignment and current candidate, then send the concrete finding, affected scope, required revalidation and proposed evidence reuse. Do not regenerate valid assignments, matrices, environment audits or evidence packages. A replacement worker needs enough baseline context to interpret the delta. Group coherent findings from the current review when practical, but report urgent safety/contract blockers immediately. Reuse only after verifying relevant source, harness, dependencies, build configuration and environment match, preserving original limits. A commit ID alone is insufficient. Rerun if applicability is uncertain; shared owners/styles may affect many consumers. Required fresh checks cannot be skipped.

Use targeted searches/relevant sections and programmatic extraction from large logs/JSON; expand to full files or raw evidence when needed. Avoid repeatedly dumping unchanged documents, inventories or successful logs into model context. Store required full logs durably and return concise results with failures, limitations and paths. Capture the underlying operation's exit status, not only a filter/parser's success; distinguish success, failure, timeout, cancellation and unexecuted work. Incomplete output or parser failure must be surfaced and investigated, never treated as a clean pass.

Batch compatible independent reads/mechanical checks and inspect every result together. Preserve individual statuses; tests sharing ports, fixtures, generated files or build destinations remain sequential unless isolation is established. Keep dependent edits, mutations, approvals and decisions sequential. Apply existing candidate-freeze and disk-allocation rules; do not hide failures inside a large command batch. Use browser inspection for relevant visual/focus behavior and discrepancies rather than automatically repeating the full matrix with every tool. Retain every required case at its assigned gate and all mandated fresh checks; local/reused evidence cannot silently pass a later platform gate.

Report concise differences, counts, failures, skipped/not-tested obligations, evidence limits and artifact paths; retain raw logs/artifacts and inspect unexpected output. Link any existing canonical input record and detailed results instead of regenerating/reproducing unchanged inventories, hashes or packages. Apply process improvements prospectively; do not reorganize historical evidence merely to match a new convention. Briefly record causes of recurring setup failures or reasons for repeated checks in the existing report, not a new tracking system. Efficiency does not authorize model changes, missed cases, weaker contracts or unsafe rollback.

## Resource Lifecycle

Unfinished source, backing Git metadata and required evidence must be durable from creation. Before delegating edits, verify the resolved checkout, backing Git/common-directory storage and evidence destinations under the reference's persistence procedure; an existing checkout is not automatically suitable. Reuse that verification only while paths/storage remain demonstrably unchanged. `/tmp` and other disposable locations may hold only reproducible copies. Keep a compact ownership/consumer/release record, preserve evidence and uncommitted work until their retention conditions are satisfied, and never infer cleanup authority from names, age or location.

Read and apply [Resource Lifecycle](./references/resource-lifecycle.md) for that initial persistence verification and before creating/reusing worktrees or disposable build/test copies, large builds (including in the main checkout), dependency/browser installations, archives or other substantial allocations, and cleanup/storage recovery. It defines persistence, coordinated disk-headroom checks, evidence retention and bounded cleanup. Reuse already-read instructions and verified setup where applicable rather than reloading the reference for every routine step.

The core safety boundary always applies: no broad `/tmp` clearing, wildcard/prefix deletion, shared-cache pruning, symlink traversal into unrelated locations, forced removal of uncommitted work, or destructive cleanup outside the recorded run-owned scope. Disk-full/quota/inode failures stop affected writes until safe headroom and candidate integrity are re-established.

## Review Rules

Review against:

- frozen contracts
- exact scope boundaries
- invariants
- prompt requirements
- validation completeness
- preservation of unaffected behavior

For each finding, state:

- what is wrong
- where it is wrong
- why it matters
- whether docs are clear enough to fix it without guessing

After findings, state:

- whether the implementation is acceptable
- residual risk
- whether the prompt was insufficient or the implementer failed to follow it, when supported
- whether a follow-up prompt is needed

If there are no findings, state an acceptable verdict clearly before summaries.

## Commit Rules

Implementation-chunk commits are controlled by the explicit commit policy. Planning checkpoints require their own applicable authorization under the preflight rule; neither permission grants the other. A broader user instruction forbidding commits or requiring confirmation for every commit still applies to both scopes unless explicitly changed. Supported implementation policies are:

- `authorized-for-accepted-chunks`
- `ask-before-each-commit`
- `do-not-commit`

Resolve the policy from current user instructions, still-applicable earlier explicit authorization for this run, or an unambiguous project/workflow policy. Normalize clear ordinary language into a supported value and record it with its source in the handoff or current execution record. Later explicit instructions take precedence. Do not ask the user to repeat authorization or spell the exact label.

For example, "commit each accepted chunk after validation" authorizes `authorized-for-accepted-chunks`; "ask me before every commit" means `ask-before-each-commit`; "do not commit" means `do-not-commit`. General permission to implement, a hypothetical workflow example, or permission for a single commit does not authorize committing all chunks.

If authority remains missing, conflicting or ambiguous after checking those sources, stop and ask before starting implementation. Do not invent a default. A normalized commit policy does not override a user pause or authorize pushing, publishing or unrelated changes.

For `authorized-for-accepted-chunks`:

- commit after each accepted chunk once review and required validation pass
- never commit unrelated dirty changes
- use one-line commit messages with the chunk ID prefix when one exists

For `ask-before-each-commit`:

- stop after each accepted chunk
- report the proposed one-line commit message
- ask the user before committing

For `do-not-commit`:

- do not commit
- report the proposed one-line commit message for each accepted chunk

If the user explicitly approves a validation exception, first record its exact scope, residual risk and missing evidence as a change to the acceptance contract. It is not a PASS for the omitted check, does not waive other gates, and cannot override higher-priority safety/authority constraints. Efficiency or permission to continue working is not such an exception.

Before committing:

- inspect the exact staged diff against the accepted scope, including the absence of held/unrelated work
- establish that staged content and relevant build match the accepted candidate identity; verified continuity may cover adjacent boundaries without another full inventory/hash scan or test run, but never replaces staged-diff inspection. Renew verification when identity is uncertain and resolve affected evidence mismatches before committing
- confirm the reviewed chunk is acceptable
- confirm required validation passed under the current recorded acceptance contract
- confirm no unrelated changes are included
- use a one-line commit message
- preserve the chunk ID prefix when one exists
- separate sentence-like clauses with semicolons when needed

Do not commit the target chunk if:

- review findings remain
- a contract ambiguity is unresolved
- validation required by that acceptance contract failed or could not run
- unrelated dirty changes cannot be separated safely
- the commit policy is `do-not-commit`
- the commit policy is `ask-before-each-commit` and the user has not approved that specific commit
- commit authority remains unresolved or cannot be normalized to a supported policy

## Stopping Conditions

Hold the affected chunk and its dependents, leaving them unaccepted, when:

- a contract, symbol, endpoint or data source is ambiguous, or a required output location remains unresolved after checking instructions and established conventions
- the plan/checklist/prompt map disagree and the correct contract cannot be inferred from written docs
- a `needs-small-freeze-before-prompt` decision affects the next implementation path
- implementation needs scope widening
- required validation fails or cannot run at that chunk's assigned gate
- sub-agent changes are not inspectable as an actual diff

Record the blocker and preserve held work/evidence. Continue independent ready work only under existing authorization and after verifying it neither depends on the held implementation nor changes the validated scope, build/test inputs or shared runtime state. Separate its acceptance and commit from held work. Different filenames alone do not establish independence; when safe separation is unproven, hold that candidate too.

Stop the entire run and report when the user pauses/stops it, required run authority remains unresolved, a global safety/integrity issue prevents safe work, or no safe authorized ready chunk remains (including completion). Unresolved contract decisions still require explicit ready-subset authorization to continue unaffected work. A chunk-specific environment/validation hold alone does not cancel an otherwise authorized run; it never waives the blocked requirement.

## Final Response

For each orchestration run, report:

- chunks completed
- chunks blocked and why
- readiness audit status
- commits made, if any
- validations run
- residual risk
- next recommended action
