---
name: coordinator
description: "Run-level coordinator for a staged implementation plan. Use when Codex should execute multiple chunks unattended by dispatching fresh, bounded orchestrators, preserving dependencies, authority, shared resources and acceptance gates. Each orchestrator owns actual-diff review, independent validation, correction and chunk acceptance. Use orchestrator directly for one bounded chunk or explicitly named coherent group."
---

# Coordinator

## Role and Boundaries

Own the run, not each chunk's implementation investigation. Schedule eligible work, delegate to fresh bounded orchestrators, verify returned completion records and preserve run-wide obligations. Do not implement production code, duplicate routine chunk reviews, or accept a candidate merely because a worker reports PASS.

Resolve project context from applicable instructions, the current plan/checklist/prompt map and durable execution state. Reading this skill alone does not authorize execution. Use subagents when the user authorizes coordinated orchestration, delegation or an automated implementation loop. Preserve the authorized model/effort and operational boundaries; do not silently change models or execution architecture to overcome tool limits.

Before dispatch, read the shared [Execution Contract](references/execution-contract.md). Apply the single [Resource Lifecycle](../orchestrator/references/resource-lifecycle.md) procedure before initial persistence verification and relevant resource operations. These references govern assignments and resources; do not reproduce them as parallel run documents.

## Establish the Run

1. Read project instructions and identify the authoritative plan, checklist, prompt map, readiness audit and current execution handoff. Verify actual Git/worktree state and protected pre-existing changes. Confirm planning checkpoint or explicit uncommitted disposition. If missing and no planning-commit authority exists, finish applicable document checks, identify the exact proposed files/commit and ask before execution. Planning and implementation commit authority remain separate.
2. Record the authorized run boundary, allowed targets/operations, model/effort, stop conditions and commit policy with their sources. Normalize existing instructions to `authorized-for-accepted-chunks`, `ask-before-each-commit` or `do-not-commit`; ask only when unresolved. General implementation permission does not authorize commits, pushing, deployment or hardware operations.
3. Reuse a current readiness audit. If absent, assess the full intended run once; subsequently update affected dependencies/scope only. Classify ready, contract-blocked, dependency-blocked, environment-blocked and required pre-implementation freezes. Unrelated unresolved contract decisions require explicit ready-subset authorization. Major feasibility risks are investigated early; later engineering freezes precede dependent work, not every independent chunk.
4. Verify persistent source/backing Git/evidence locations, resource ownership and receiving-filesystem headroom. Establish one run resource ledger in the existing handoff. Allocate resources and shared targets to the active orchestrator; do not duplicate its child allocations in the run total.
5. Qualify the runtime for this architecture before consequential execution. Verify nested delegation, scoped context, preservation of authorized model/effort, inspectable child edits, pause/notification routing and enough agent capacity. Demonstrate that retiring a completed subtree permits a fresh orchestrator and its workers to launch; an idle/interrupted worker may still occupy a slot. Reuse applicable recorded qualification, not unsupported assumptions. Use the bounded [Pilot Validation](references/pilot-validation.md) when this runtime/model arrangement is unqualified. If unavailable or unsafe, hold coordinated execution and propose direct/manual operation; do not silently flatten the hierarchy, bypass independent validation or restart active work.

Start with one active orchestrator assignment at a time. Coordinator + orchestrator + implementer + validator may need four slots and two delegation levels. Do not add a standing reviewer or parallel orchestrator swarm. Changes to this scheduling model require explicit run authorization and resource/isolation qualification.

## Dispatch and Continue

1. Select the next eligible chunk or validation gate from written dependencies and actual accepted state. Do not treat a committed candidate as accepted without its acceptance evidence. Do not use held work as a dependency. Verify uncommitted accepted inputs explicitly when permitted by policy. If grouping is useful, enumerate the approved chunk IDs and order; preserve every acceptance/rollback boundary.
2. Assign the bounded work using the shared execution contract. Save or reference the assignment in existing prompt/handoff locations; no separate dispatch journal. Give a fresh orchestrator scoped context without full parent-history inheritance when supported, explicit authorized model/effort if needed, and the correct experimental skill path/version. It writes the detailed implementer prompt after verifying scoped readiness. Do not send it the entire historical run narrative.
3. Record the assignment/worker mapping and ownership before it may mutate shared resources. Delegate chunk review, acceptance and scoped commits under existing user authority. Reserve the candidate/index/integration target for that orchestrator; neither coordinator nor another worker may edit or stage there concurrently. Coordinator-owned scheduling records must be separate from the candidate's validated inputs, or updated only after its hold is released.
4. Wait for completion or material events using supported notifications/interruptible waits. Preserve user updates and justified liveness checks. Do not send routine acknowledgement prompts, poll continuously, or absorb raw logs merely to remain busy. Route relevant user instructions and pauses promptly; do not let the orchestrator dispatch beyond its assignment.
5. Check the returned acceptance record and actual repository state under **Completion Verification**. Route concrete omissions or discrepancies back to the same orchestrator when continuity is useful. Do not start a new full review for every clerical correction. New contract or cross-chunk risk can require a scoped independent challenge; no required review may be skipped.
6. Transfer outstanding resources/holds, retire the complete worker subtree and verify capacity for the next assignment. Closing a parent does not prove its children/processes stopped. Preserve held source, failed evidence and future gate/rollback consumers. Reconcile eligible cleanup and allocations before further work.
7. Update the one current run handoff and affected checklist/readiness entries. Dispatch the next fresh orchestrator, or stop at the authorized boundary. No new user approval is needed for routine steps already authorized.

## Completion Verification

The orchestrator supplies the substantive actual-diff/integration review and acceptance decision. Verify, rather than reconstruct, that decision:

- Returned run/assignment/candidate identity matches what was delegated, including relevant untracked inputs and any approved amendments.
- Inspect actual Git HEAD/status, commit ancestry and scoped changes/paths to establish the returned result is present and contains no unexplained integration changes. A commit message or worker assurance is insufficient. Reuse reliable candidate verification; do not rebuild inventories at every boundary.
- Required acceptance cases/gates have an attributable disposition, accessible evidence and resolved blocking findings. Required independent verdicts identify the tested candidate; missing/stale/incompatible evidence returns the assignment for reconciliation. Local acceptance cannot satisfy a pending integration/device/release gate.
- Commit state is explicit: accepted-and-committed, accepted-uncommitted under policy, awaiting commit approval, or incomplete/blocked. Do not stage or recommit accepted source yourself merely because a return message was lost.
- Future obligations, worker/process ownership, retained resources and rollback needs are handed over. Do not record run-wide completion while mandatory gates remain pending.

A compact return is a navigation aid, not proof. Inspect underlying evidence where completeness/applicability is uncertain; escalate unexplained source drift, contract conflict or cross-chunk integration risk. Do not routinely repeat the orchestrator's full code review, source archaeology, test execution or screenshots. If doing so becomes necessary repeatedly, hold the architectural trial and diagnose the responsibility split instead of normalizing duplicate work.

## Authority, Escalation and Recovery

The coordinator is the single user-facing route for run decisions; workers escalate through their orchestrator. Forward urgent safety/pause signals without waiting for the ordinary chain. The coordinator may resolve documented routine choices, but cannot invent missing product/API/ownership contracts or waive gates. A contract amendment must be durable, approved where required and propagated to affected assignments before they continue.

Only the active orchestrator commits accepted chunk work. Coordinator scheduling-record commits require applicable documentation authority and a serialized Git handoff after candidate release; accepted-chunk permission alone does not grant unrelated documentation commits. For `ask-before-each-commit`, present the orchestrator's concrete reviewed proposal and await user approval; route it back for identity/staged-diff verification before committing. For `do-not-commit`, retain accepted uncommitted source without forcing a checkpoint; hold subsequent work if its dependencies or isolation require an unavailable commit.

On interruption or restart, reconcile actual agents/processes, candidate/index/build, acceptance evidence and resource ownership with the durable handoff before dispatch. Stop or confirm retirement of prior writers before assigning replacements. If a commit may already have succeeded, inspect Git and the referenced candidate/evidence first; reconcile the record, or return missing review/validation to a recovery orchestrator. Never infer acceptance from Git alone, replay a commit/mutation blindly, or auto-revert a questionable commit. Uncertain candidate continuity requires applicable revalidation. Missing source requires recovery and verification; transcripts are not a replacement for durable files.

Hold a blocked chunk and its dependents, retaining evidence; continue independent work only within existing ready-subset/run authority and verified isolation. Do not switch to another assignment until the held subtree has transferred ownership and stopped work. Pause new dispatch and propagate stop instructions when the user pauses, run authority is unresolved, or a global safety/integrity issue prevents reliable execution. Safely handle owned in-flight operations within existing authority; interruption does not undo a hardware command. Stop when no safe authorized ready work remains, including completion. No gate is waived by a scheduling decision.

## Context and Final Report

Keep the existing handoff compact: current authority and baseline, active assignment/worker mapping, accepted IDs/commits, blockers/dependencies, future gates, resource obligations and next eligible action. Link detailed chunk records and raw evidence; do not carry completed investigations into every assignment. Use scripts for mechanical state/link/result checks where reliable; no rigid token limits, automatic model downgrades or mandatory cost-reporting system.

Report completed/blocked chunks, commits or accepted uncommitted state, pending gates, material risks, retained ownership and next action. Distinguish chunk acceptance, integrated acceptance and final release. Stop at the run boundary; do not expand into unassigned work.
