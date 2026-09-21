# Bounded Execution Contract

Shared by Coordinator and Orchestrator. Use existing prompt, handoff and chunk-evidence locations; these fields are obligations, not a requirement for a new database, JSON schema or duplicate report. Applicable project/user instructions prevail. A scoped packet must carry all binding constraints and authoritative references, not rely on inherited conversation.

## Assignment

- **Identity and mode:** run ID, assigned chunk/gate ID(s), orchestrator assignment ID/attempt, direct or coordinated invocation, parent identity if coordinated. Keep stable logical IDs and map them to supported unique tool labels and actual worker IDs. Same-worker corrections retain the assignment ID; replacements increment attempt. Group membership is explicit, never inferred from the whole checklist.
- **Boundary:** purpose, allowed scope, exclusions, ordered dependencies and stop point; acceptance level (local, integration, platform/release) for each assigned unit. No unassigned next-chunk selection. A validation-only gate may need no implementer.
- **Authority:** source of user authorization, ready-subset limits, commit policy, allowed runtime targets/operations, model/effort and fallback restrictions, pause state, cleanup scope and retention restrictions. Delegation cannot expand any of these. Unresolved authority is a blocker.
- **Skill bundle:** selected plugin version/source identity and exact role skill paths. Every descendant reads its role from that same bundle; bare skill names are insufficient when installed and branch-local versions differ. Verify paths and relevant instruction changes before reuse; do not mix stable and preview roles or treat an unchanged version string as proof of unchanged files.
- **Inputs:** authoritative plan/checklist/prompt-map sections, applicable amendments/shared invariants, current readiness and prerequisite acceptance links, original failures/open findings and relevant source locations. Supply a reading path and verify applicability, not a transcript dump. The orchestrator must discover relevant callers/dependencies and missing obligations rather than trusting the packet as exhaustive.
- **Candidate:** repository/workspace/integration target, baseline and relevant staged/unstaged/untracked inputs, protected pre-existing work, durable evidence location, candidate/build provenance where available. Identify allowed record paths and who writes each. No generic clean/reset permission.
- **Resources:** source/index writer, target/process ownership, assigned allocation on each filesystem, shared-resource restrictions and retained consumers. The coordinator owns run-wide allocations; the orchestrator subdivides its allocation among workers and asks before exceeding it. Reconcile actual remaining bytes/consumers at handoff without double-counting the same reservation.
- **Validation and return:** full applicable acceptance obligations and required independent/fresh checks, permitted evidence reuse/limits, acceptance-record destination and return fields below. Missing tests or platform access remain explicit at their assigned gate.

In coordinated mode, the orchestrator owns its chunk prompt, review, evidence and acceptance record; the coordinator owns global scheduling/readiness/status records. The orchestrator returns proposed global updates rather than editing them concurrently. If a shared contract must change, hold affected work and route it for authoritative reconciliation. Direct mode folds run duties into the orchestrator only for the explicitly bounded assignment.

### Assignment labels

Establish one non-secret run ID in the existing handoff before dispatch. Preserve it across session restarts/resume and pass it unchanged to descendants; distinct runs use distinct IDs. The logical assignment is run ID + scope (`chunk` or `group`) + exact chunk/gate or named-group ID + role + attempt. A group names its members explicitly; its workers use their actual assigned chunk or group, not an inferred allocation. Same-worker correction/revalidation retains identity; a fresh replacement increments the attempt for that run/scope/unit/role, including after resume. Do not reuse an assignment ID for a different worker.

For supported spawn task names, encode this identity as:

`si1_<hex run ID>_<c|g>_<hex unit ID>_<role>_<attempt>`

Use lowercase hexadecimal of the exact UTF-8 run/unit IDs (no slugification), `c` for one chunk or gate, `g` for a named group, the lowercase role name, and a positive decimal attempt without leading zeros. IDs must be nonempty, at most 96 UTF-8 bytes each and contain no characters below U+0020; keep the encoded label within 512 characters and the tool's actual limits. For example, run `run-a`, chunk `O-03`, role `implementer`, attempt `1` becomes `si1_72756e2d61_c_4f2d3033_implementer_1`. Hex is reversible, not anonymization; never put credentials or private content in identifiers.

Record logical assignment → submitted tool label → returned worker ID and immediate parent in the existing handoff/assignment record once. Check uniqueness in the tool's naming scope. If the tool cannot accept the encoding or has no label field, retain the logical identity and explicitly map the supported unique label (or absence of one) to the actual worker; never silently truncate, rename canonical IDs or assume an audit can infer the mapping. Labels identify assignments, not acceptance, completion or resource release. No extra status messages or telemetry journal are required.

## Execution and Acceptance

The orchestrator reads the actual candidate and contracts, writes/reuses the scoped implementer prompt, reviews the actual diff and affected behavior, obtains independent validation where required, reconciles findings and decides chunk acceptance. No implementer self-acceptance or validator acceptance authority. Independence requires a separate validator assignment with authoritative requirements and candidate evidence, not adoption of the implementer's verdict. Required fresh challenges remain mandatory.

Fixable in-scope review/test failures keep acceptance held while the same assignment performs correction and revalidation. They do not automatically become a blocked return or trigger fresh orchestrator setup. Return a block when missing authority, contract, dependency or environment prevents safe progress; preserve the candidate and findings rather than retrying equivalent failed attempts.

Keep communication event-driven within existing authority. The bounded orchestrator handles ordinary investigation, in-scope corrections and revalidation without routine progress messages or acknowledgement requests to Coordinator. Promptly report completion, blockers requiring coordination, authority requests and material findings affecting the wider run; preserve other findings and failures in durable evidence and the final return. Required user-facing updates do not create a requirement to poll descendants or relay periodic status through each level. Use known state and state uncertainty honestly; higher-priority update instructions, agreed checkpoints, urgent reporting and pause handling remain binding.

Keep the candidate and relevant harness/configuration/build stable during validation. Same-chunk repairs may reuse workers, but writes resume only after the validation hold is released. Replacement requires verified cessation of prior writes, candidate verification and ownership transfer. No additional review layer is mandated by the existence of a coordinator.

The active orchestrator alone stages/commits its accepted scope under its commit policy, after inspecting the exact staged diff and verifying accepted candidate identity. Coordinate any integration target exclusively. Commit permission is not merge/deploy/push permission. If integration changes the accepted source or relevant inputs, re-establish review/evidence applicability before integration acceptance. A valid isolated-worktree commit does not by itself prove the target branch is integrated.

For `ask-before-each-commit`, return the concrete proposed commit and pause before Git mutation pending approval; for `do-not-commit`, preserve and identify accepted uncommitted work. Acceptance and Git completion are separate facts. User pauses and later authority restrictions override the assignment.

## User-Requested Progress Snapshots

Treat ordinary progress questions as lightweight snapshots by default, recognizing intent rather than exact wording. Examples such as "status?", "progress update" and "how's it going?" are illustrative, not commands the user must memorize.

For each ordinary progress request, Coordinator requests one bounded snapshot from the active Orchestrator. Reuse an already-pending snapshot request for that assignment instead of sending another. If no Orchestrator assignment is active, report the latest known outcome. The Orchestrator answers from existing knowledge without cascading status requests to workers, inspecting changing files, reconstructing evidence or running checks solely for the snapshot. In direct mode, Orchestrator answers the user from its own known state. Do not spawn a worker or wake a retired assignment for a snapshot.

Return a short paragraph covering the current chunk/phase, meaningful progress, known blockers and what remains before acceptance/commit. Distinguish reported progress from verified acceptance; qualify older information as "last reported" and missing blocker information as "no blocker reported". Do not invent percentages, ETAs or fresh verification. A snapshot neither performs nor replaces acceptance.

During an active run or direct Orchestrator assignment, deliver user-facing snapshots as intermediate commentary, not a final response that ends the execution turn. A progress question does not replace the objective or request a pause. After replying, continue authorized coordination/work or supported waiting. When a bounded Orchestrator completes, Coordinator processes its result through normal completion verification and scheduling; that completion does not itself end the run. Existing stopping conditions still apply, and bounded Orchestrators still return and stop at their own assignment boundary.

If a fresh reply is not promptly available, provide the last known state and its limitation without interrupting active work or repeatedly prompting. Use supported notifications/waits for the requested reply. Do not add acknowledgement exchanges, progress documents or periodic reporting solely because an update was requested; continue the authorized work or waiting afterward, preserving any user pause.

An explicit request for deeper investigation, fresh verification or a detailed review overrides the lightweight default to the extent requested and within existing authority. It does not waive safety, ownership, pause handling or acceptance requirements. Material findings and urgent blockers still receive their required treatment.

## Result and Durable Handoff

Return concise fields with accessible evidence links; omit empty optional sections:

- Run/chunk/assignment identity; outcome `accepted`, `blocked`, `incomplete` or `awaiting-approval` at the stated acceptance level.
- Actual baseline/candidate/build identity and changed scope; approved deviations or new contract questions.
- Acceptance record and attributable implementer/orchestrator/independent-validator results; fresh/reused evidence, reuse limits, failure resolution and missing obligations. Preserve original failures.
- Git disposition: actual commit(s) and integration target/state, accepted uncommitted scope, or proposed commit awaiting approval. Include staged/unstaged/untracked state needed to distinguish held or unrelated work.
- Remaining gates/dependencies, risks and requested scheduling/contract updates; no implied release PASS from local success.
- Descendant worker IDs/status, processes/validation holds, retained/disposable resources, allocations and ownership transfer. Verify inactivity separately from resource release; identify any justified retention exception and release condition.

Write the acceptance basis and candidate/evidence pointers durably before a commit, then record the resulting commit identity immediately afterward. These are recoverable stages, not an atomic transaction: on restart inspect Git and evidence to determine which stage completed. Do not replay a commit or infer acceptance solely from its presence. The coordinator verifies completion and updates scheduling; it does not repeat routine substantive code review or fabricate missing acceptance.

An accepted boundary ends the assigned unit, not every future obligation. A held assignment may return after safely transferring ownership; it does not need to remain alive indefinitely. Preserve enough durable state for a replacement to verify the candidate and continue without recreating unrelated history. Resume only after checking changed/uncertain inputs and retiring prior writers under the lifecycle rules below.

## Worker Retirement and Capacity

Distinguish assignment retirement (no further assigned work), stopped turns/writes, process/resource ownership transfer, and runtime slot reclamation. None proves the others. Collect the handoff in the worker's final result, including owned processes, validation holds, retained resources and unresolved obligations. Verify it before reassigning ownership. A complete final result needs no acknowledgement or retirement message; if essential information is missing, request a concrete bounded follow-up. Never send ceremonial "thanks", "you're retired" or "no more work" messages to completed workers. Safety interventions and necessary coordination remain mandatory.

Use the runtime's supported lifecycle. Where explicit closure exists, close finished workers after handoff and needed same-chunk continuity ends. In Multi-Agent V2 with demand-driven residency reclamation, leave finished workers idle and spawn the next authorized worker normally. Eligible terminal workers can be unloaded on demand; visible `completed`, `errored` or `interrupted` status alone does not prove eligibility because active turns or pending mailbox items can prevent it. Queue-only `send_message` can leave a completed worker with pending mail; use turn-triggering `followup_task` for actual follow-up work. `interrupt_agent` stops executing work, not an explicit thread closure. Neither a resident listing nor absence of a close tool alone blocks dispatch. Do not add a capacity probe before every assignment or infer process cleanup from a worker disappearing from a listing.

On an actual capacity failure, hold dependent dispatch and use bounded diagnosis/recovery within existing authority:

1. Inspect available agent states and recent relevant message history; distinguish active work from specifically suspected mailbox pinning. Coordinate through one recovery owner, using verified canonical worker paths. If legitimate work holds capacity, wait for its completion using notifications or supported waits. Do not interrupt it, resume a retired implementation assignment or wake every completed agent merely to gain capacity.
2. If a known inactive worker has evidence of queued post-completion messages, first establish that its writes/owned operations are stopped or safely handed over and recovery will not conflict with an active owner. Where supported, give it one turn-triggering recovery-only follow-up that explicitly supersedes earlier execution instructions: consume pending messages without acting on old assignments; no edits, commands, delegation or messages to other agents; return a short final response and stop. Preserve pauses, model/effort, validation holds and operational restrictions. Unknown ownership or unsafe recovery remains blocked.
3. Wait for that turn to complete, send no acknowledgement, and retry the blocked spawn once after the concrete state change. If still blocked, pursue only a distinct evidenced cause within a bounded recovery scope; no repeated identical retries or automatic restart/configuration/model changes. Record exact results and limitations in existing evidence. Success proves capacity at the tested boundary, not the original cause or full runtime qualification.

Hold coordinated execution if required capacity remains unavailable after safe supported recovery, or recovery is unavailable/unsafe. Report the specific limitation and an explicit recovery/direct/manual alternative; a fresh session is an option, not a presumed requirement or guaranteed fix. Never waive independent validation, acceptance gates, candidate protection or ownership checks to fit the available slots.
