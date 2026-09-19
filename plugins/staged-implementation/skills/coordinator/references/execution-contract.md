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

## Execution and Acceptance

The orchestrator reads the actual candidate and contracts, writes/reuses the scoped implementer prompt, reviews the actual diff and affected behavior, obtains independent validation where required, reconciles findings and decides chunk acceptance. No implementer self-acceptance or validator acceptance authority. Independence requires a separate validator assignment with authoritative requirements and candidate evidence, not adoption of the implementer's verdict. Required fresh challenges remain mandatory.

Fixable in-scope review/test failures keep acceptance held while the same assignment performs correction and revalidation. They do not automatically become a blocked return or trigger fresh orchestrator setup. Return a block when missing authority, contract, dependency or environment prevents safe progress; preserve the candidate and findings rather than retrying equivalent failed attempts.

Keep the candidate and relevant harness/configuration/build stable during validation. Same-chunk repairs may reuse workers, but writes resume only after the validation hold is released. Replacement requires verified cessation of prior writes, candidate verification and ownership transfer. No additional review layer is mandated by the existence of a coordinator.

The active orchestrator alone stages/commits its accepted scope under its commit policy, after inspecting the exact staged diff and verifying accepted candidate identity. Coordinate any integration target exclusively. Commit permission is not merge/deploy/push permission. If integration changes the accepted source or relevant inputs, re-establish review/evidence applicability before integration acceptance. A valid isolated-worktree commit does not by itself prove the target branch is integrated.

For `ask-before-each-commit`, return the concrete proposed commit and pause before Git mutation pending approval; for `do-not-commit`, preserve and identify accepted uncommitted work. Acceptance and Git completion are separate facts. User pauses and later authority restrictions override the assignment.

## Result and Durable Handoff

Return concise fields with accessible evidence links; omit empty optional sections:

- Run/chunk/assignment identity; outcome `accepted`, `blocked`, `incomplete` or `awaiting-approval` at the stated acceptance level.
- Actual baseline/candidate/build identity and changed scope; approved deviations or new contract questions.
- Acceptance record and attributable implementer/orchestrator/independent-validator results; fresh/reused evidence, reuse limits, failure resolution and missing obligations. Preserve original failures.
- Git disposition: actual commit(s) and integration target/state, accepted uncommitted scope, or proposed commit awaiting approval. Include staged/unstaged/untracked state needed to distinguish held or unrelated work.
- Remaining gates/dependencies, risks and requested scheduling/contract updates; no implied release PASS from local success.
- Descendant worker IDs/status, processes/validation holds, retained/disposable resources, allocations and ownership transfer. Verify inactivity separately from resource release; identify any justified retention exception and release condition.

Write the acceptance basis and candidate/evidence pointers durably before a commit, then record the resulting commit identity immediately afterward. These are recoverable stages, not an atomic transaction: on restart inspect Git and evidence to determine which stage completed. Do not replay a commit or infer acceptance solely from its presence. The coordinator verifies completion and updates scheduling; it does not repeat routine substantive code review or fabricate missing acceptance.

An accepted boundary ends the assigned unit, not every future obligation. A held assignment may return after safely transferring ownership; it does not need to remain alive indefinitely. Preserve enough durable state for a replacement to verify the candidate and continue without recreating unrelated history. Resume only after checking changed/uncertain inputs and retiring prior writers. Runtime capacity must be available for subsequent assignments; unsupported closure is not proof that slots were released.
