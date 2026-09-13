- Before doing anything get general project context by reading README.md and plugins/staged-implementation/README.md

- Always make a Plan before implementing. 

  For example:

  • Updated Plan
    └ Created an implementation plan for WsLite integration and wiring in Bootstrapper.
      □ Add WsLite module in brain-core
      □ Implement broadcast binding (guarded)
      □ Register WsLite in Bootstrapper
      □ Set ui_state callback (g_ota_bs)
      □ Remove stub and adjust flags
      □ Sanity compile Bootstrapper target
      □ Update docs on flags/endpoint

- Always favor a clean implementation or fix over a quick fix or bandaid solution

When asked for a commit message always provide a oneline commit message and separate sentences with `;`

When asked to write a markdown file the specified path might contain a date followed by `-${name}` .. e.g. 28.12.2025-${name}.md .. replace ${name} with a suitable name for the document.

You do not ever run any destructive commands unless you have explicitely been asked to do so.

## Change Discipline

- If the prompt is a question and verifying in-repo first would improve answer quality, do so.
- Treat questions, observations, and suggestions as analysis-only; only implement changes when the user explicitly says to implement/apply a patch.
- No freeform edits: use apply_patch with ≥3 context lines; keep diffs surgical; do not mix unrelated changes.
- Use spaces (no tabs) for indentation; keep indentation consistent with surrounding code and avoid whitespace-only churn in diffs
- Plan discipline: keep update_plan synced; exactly one step in_progress; revise when scope shifts.
- Ambiguity rule: if any symbol/endpoint/data source is unclear, stop and ask — do not guess.
- Preserve contracts: do not change public API schemas, event semantics, or observable behavior unless explicitly requested.
- Minimal glue: add only imports/exports/helpers strictly required for the change; no opportunistic refactors.
- Validate changes: build/typecheck affected targets and run quick verifications relevant to the edit.
- No legacy/backward‑compat migrations or auto‑fallback heuristics unless explicitly requested.
- Do not change route paths, auth checks, or CORS policy unless explicitly in scope.
- Clean cutover only by default; never implement legacy/dual-format parsing or fallback behavior unless explicitly requested.
