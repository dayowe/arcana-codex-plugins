# Staged Implementation

Staged Implementation is a Codex plugin that bundles four skills for chunked implementation work:

- `planner` plans scoped chunks, writes implementer prompts, reviews diffs, and clarifies frozen contracts.
- `implementer` executes one scoped implementation prompt without widening the task.
- `validator` verifies implemented behavior against the plan, prompt, checklist, review findings, or frozen contracts.
- `orchestrator` coordinates the planner, implementer, and validator loop across multiple chunks.

Use this plugin when a feature or fix is too large or contract-heavy to handle as one open-ended coding pass.

## Plugin Structure

```text
staged-implementation/
  .codex-plugin/
    plugin.json
  skills/
    planner/
      SKILL.md
    orchestrator/
      SKILL.md
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

Add the marketplace repository:

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
Use $orchestrator to run the staged implementation loop for this checklist.
```

The usual flow is:

1. Use `planner` to freeze scope, contracts, validation, and the next chunk.
2. Use `implementer` to execute only that chunk.
3. Use `validator` when runtime, UI, API, integration, or regression evidence is needed.
4. Use `orchestrator` when you want Codex to coordinate the loop across chunks.

## Notes

- The skills are intentionally separate. Keeping the roles separate makes the boundaries clearer and reduces accidental scope widening.
- `validator` reports evidence and risk. `planner` or `orchestrator` decides whether a chunk is accepted.
- `orchestrator` may commit accepted chunks only when the user explicitly authorizes commits.
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

After pushing marketplace changes, run `codex plugin marketplace upgrade arcana-codex-plugins`.

If this plugin changed, reinstall it with `codex plugin add staged-implementation@arcana-codex-plugins`, then start a new Codex session so updated skills are loaded.
