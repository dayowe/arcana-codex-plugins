# Staged Implementation

Staged Implementation bundles five skills with two execution entry points:

- `planner` plans scoped chunks, writes implementer prompts, reviews diffs, and clarifies frozen contracts.
- `coordinator` schedules a longer run and dispatches fresh bounded orchestrators, verifying completion and managing shared resources.
- `orchestrator` owns one assigned chunk/gate or named coherent group through actual-diff review, independent validation, corrections and authorized acceptance/commit.
- `implementer` executes one scoped implementation prompt without widening the task.
- `validator` verifies implemented behavior against the plan, prompt, checklist, review findings, or frozen contracts.

Use this plugin when a feature or fix is too large or contract-heavy to handle as one open-ended coding pass.

**Stable release: `0.2.1`.** The coordinated workflow is now the default on `master`. When upgrading from `0.1.5`, old whole-checklist launch prompts must explicitly select Coordinator; Orchestrator now owns bounded work. Release status and structural validators do not establish runtime qualification or measured savings for a particular setup. Qualify an unverified runtime using the [pilot](skills/coordinator/references/pilot-validation.md) before consequential coordinated execution, or reuse applicable recorded qualification. Keep all roles on the same selected skill bundle.

## Plugin Structure

```text
staged-implementation/
  .codex-plugin/
    plugin.json
  skills/
    planner/
      SKILL.md
    coordinator/
      SKILL.md
      references/
        execution-contract.md
        pilot-validation.md
    orchestrator/
      SKILL.md
      references/
        resource-lifecycle.md
    implementer/
      SKILL.md
    validator/
      SKILL.md
```

## Recommended Marketplace Layout

Codex installs plugins from a marketplace catalog. To publish this from Git, put the plugin inside a marketplace repository:

```text
arcana-codex-plugins/
  .agents/
    plugins/
      marketplace.json
  plugins/
    staged-implementation/
      .codex-plugin/
        plugin.json
      skills/
        planner/
        coordinator/
        orchestrator/
        implementer/
        validator/
```

Example `.agents/plugins/marketplace.json`:

```json
{
  "name": "arcana-codex-plugins",
  "interface": {
    "displayName": "Arcana Codex Plugins"
  },
  "plugins": [
    {
      "name": "staged-implementation",
      "source": {
        "source": "local",
        "path": "./plugins/staged-implementation"
      },
      "policy": {
        "installation": "AVAILABLE",
        "authentication": "ON_INSTALL"
      },
      "category": "Productivity"
    }
  ]
}
```

## Install From Git

These commands select the stable release on `master`. Verify the installed version before use; updating repository files does not replace the skills loaded in existing sessions.

Add the stable marketplace repository:

```bash
codex plugin marketplace add dayowe/arcana-codex-plugins --ref master
```

Install the plugin:

```bash
codex plugin add staged-implementation@arcana-codex-plugins
```

Start a new Codex session after installation so the bundled skills are available.

## Usage

Invoke a specific skill when you know the role you want:

```text
Use $planner to write the next implementer prompt for this feature.
Use $implementer with docs/prompts/chunk-03.md.
Use $validator to verify this chunk against the frozen API contract.
Use $orchestrator to execute chunk-03 and stop after its result.
Use $coordinator to execute the authorized plan through its remaining gates.
```

The usual flow is:

1. Use `planner` to freeze scope, contracts, validation, and the next chunk.
2. Use `implementer` to execute only that chunk.
3. Use `validator` when runtime, UI, API, integration, or regression evidence is needed.
4. Use `orchestrator` directly for bounded work, or `coordinator` to continue across a longer checklist with fresh scoped orchestrators.

Ordinary progress questions, such as "progress update" or "how's it going?", default to a brief [progress snapshot](skills/coordinator/references/execution-contract.md#user-requested-progress-snapshots). Each question requests one snapshot from the active orchestrator, reusing an already-pending request for that assignment; if none is active, the coordinator reports the latest known outcome. The orchestrator answers from existing knowledge without cascading worker queries or extra checks. Explicitly ask for investigation, fresh verification or a detailed review to override that default within existing authority; requesting an update does not start periodic reporting.

## Optional Model and Reasoning Policy

By default, preserve the already-authorized model and reasoning effort. The workflow does not automatically choose cheaper models. To request different configurations, include an optional `Execution Routing` section in the approved plan or launch instruction. For example:

| Role | Model | Reasoning |
| --- | --- | --- |
| Coordinator | current session | high |
| Orchestrator | gpt-6-astra | high |
| Implementer | gpt-5.6-luna | medium |
| Validator | gpt-6-astra | high |

This is an illustrative policy, not a model recommendation or availability guarantee. Use identifiers and efforts supported by your runtime. Every explicit model needs an explicit effort. An effort-only entry inherits the parent's model; an omitted role inherits both settings. A root entry describes the required existing session settings and does not change them. The same root rule applies when invoking Orchestrator directly.

The [routing contract](skills/coordinator/references/execution-contract.md#execution-routing) requires runtime evidence of effective settings for opted-in routing; a worker's self-report is insufficient. Unsupported routes or known mismatches hold affected work. Unobservable settings remain unverified and require explicit user authorization to proceed with that limitation. No new verification gate is imposed on default runs. Required validation and acceptance remain unchanged; choosing a cheaper model does not guarantee equivalent results or lower total effort through acceptance.

## Execution Ownership

| Responsibility | Owner |
| --- | --- |
| Run authority, dependency scheduling, global status and shared allocations | Coordinator; direct Orchestrator only within its bounded assignment |
| Chunk prompt, actual-diff/integration review, correction loop and substantive acceptance | Bounded Orchestrator |
| Implementation and self-checks | Implementer |
| Independent candidate/behavior verification | Validator |
| Accepted chunk staging/commit under explicit policy | Active Orchestrator only |
| Returned evidence/identity/completion checks and next dispatch | Coordinator |

The [shared execution contract](skills/coordinator/references/execution-contract.md) defines assignment/results and recovery boundaries. Start with one active Orchestrator assignment at a time; groups name their IDs and preserve individual gates. Coordinator does not repeat routine source review, tests or screenshots. It verifies actual repository results and evidence completeness/applicability, escalating discrepancies. Only Coordinator writes run-wide scheduling records; only Orchestrator writes chunk acceptance records. Coordinate shared Git mutations explicitly.

The orchestrator stays through same-chunk corrections. At a bounded result, it hands over ownership/resources in final results and retires descendant assignments. Coordinator verifies the handoff, then dispatches normally using supported explicit closure or automatic runtime reclamation. No post-completion retirement messages or per-assignment capacity probes are required. Nested delegation, scoped context and successive complete worker groups must be qualified. Actual capacity failures use the shared [bounded recovery procedure](skills/coordinator/references/execution-contract.md#worker-retirement-and-capacity); missing `close_agent` alone is not a blocker. Unresolved runtime failures hold the coordinated loop; direct/manual execution requires an explicit alternative, not silently weakened validation.

The existing plan/checklist/prompt map and one live handoff remain authoritative. No new run database, duplicated transcript archive, standing reviewer, automatic model downgrade or automatic external runner is introduced. User pauses, commit policies and later device/integration/release gates remain binding. A result can be accepted uncommitted, committed but not integrated, or locally accepted with later gates pending; those states are not interchangeable.

## Lifecycle and Context Efficiency

The workflow keeps implementation and validation rigor while avoiding avoidable context churn:

- Each worker carries stable run/unit/assignment identity using the [shared label convention](skills/coordinator/references/execution-contract.md#assignment-labels), with a recorded mapping to the tool label, actual worker and immediate parent. Resume preserves the run ID; same-worker corrections retain assignment identity; replacement workers increment the attempt.
- An implementer may remain available for same-chunk repairs but cannot write during validation. Replacements acquire write ownership only after prior writes stop and the candidate, findings and resources are verified and transferred.
- Accepted/blocked/end-of-assignment workers retire after verified handoff: stop assigned work and leave completed workers idle, using supported closure or automatic reclamation. Assignment retirement, stopped turns, resource transfer and runtime slot release are distinct. Default coordinated scheduling is one active assignment at a time; broader concurrency requires explicit qualified authority. Idle availability alone does not demonstrate token expense.
- Workers return one compact packet and stop until a concrete follow-up. Avoid acknowledgement chatter; retain justified liveness checks, intervention and blocker reporting.
- The existing durable handoff stays compact while preserving pending gates, dependencies, recovery obligations and authority restrictions directly or through authoritative links.
- Parent review starts from the actual diff, requirements and evidence, inspecting surrounding code, callers and shared behavior as needed without waiting for a discovered defect.
- Read the resource reference for initial persistence verification before edits and before substantial allocations (including large builds in the main checkout), worktree/resource management or cleanup. Reuse applicable instructions and verified setup rather than reloading them for routine steps.

These are efficiency rules, not acceptance shortcuts. Required independent validation, fresh gates, contract checks and evidence remain mandatory where the task requires them.

## Notes

- The skills are intentionally separate. Keeping the roles separate makes the boundaries clearer and reduces accidental scope widening.
- `validator` reports evidence and risk. `planner` or `orchestrator` decides whether a chunk is accepted.
- `orchestrator` may commit accepted chunks only when the user explicitly authorizes commits.
- Coordinator forwards existing commit authority without expanding it; global-record commits need applicable documentation authority and exclusive Git access after candidate release.
- Unfinished source, backing Git metadata and required evidence live on persistent storage from creation. Prefer the project's established worktree location; ask once if isolation needs a new location. `/tmp` is for reproducible scratch, never the only copy of unfinished work. Nested worktree paths must be ignored, untracked and protected from broad cleanup.
- Authorizing orchestration includes routine cleanup of its tracked, disposable temporary resources after ownership, retention and consumer-release checks pass. The handoff states this default; explicit retention/no-deletion instructions override it. Shared caches, unrelated files and resources still needed remain protected.
- If the same skill names also exist as standalone local skills, Codex may show duplicates. After the plugin is installed and verified, remove or disable the standalone copies if you want only the plugin version.

## Development

Validate the plugin manifest:

```bash
python3 /path/to/plugin-creator/scripts/validate_plugin.py /path/to/staged-implementation
```

Validate an individual skill:

```bash
python3 /path/to/skill-creator/scripts/quick_validate.py /path/to/staged-implementation/skills/planner
```

Validate all five skills and the plugin; inspect relative references and cross-role authority consistency. Use the pilot procedure for mechanics, recovery and real-work evidence. Passing file validators is not a behavioral or efficiency verdict.

After an explicitly selected release/ref is published, run `codex plugin marketplace upgrade arcana-codex-plugins`.

If this plugin changed, reinstall it with `codex plugin add staged-implementation@arcana-codex-plugins`, then start a new Codex session so updated skills are loaded.
