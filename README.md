# Arcana Codex Plugins

Codex plugin marketplace for reusable agent workflows.

## Included Plugins

- `staged-implementation`: Planner, coordinator, bounded orchestrator, implementer and validator skills for staged implementation workflows.

This branch contains the experimental coordinated-workflow preview (`0.2.0-alpha.1`). Stable `0.1.5` remains on `master`; the Git install commands below select that stable branch. Do not mistake branch-local edits for an installed upgrade. See the plugin README for the preview boundaries and qualification procedure.

## Install

Add this Git repository as a Codex marketplace:

```bash
codex plugin marketplace add dayowe/arcana-codex-plugins --ref master
```

Install the staged implementation plugin:

```bash
codex plugin add staged-implementation@arcana-codex-plugins
```

Start a new Codex session after installation so the bundled skills are available.

## Layout

```text
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

## Updating

After pushing changes, refresh the marketplace snapshot:

```bash
codex plugin marketplace upgrade arcana-codex-plugins
```

If `plugins/staged-implementation/` changed, reinstall the plugin:

```bash
codex plugin add staged-implementation@arcana-codex-plugins
```

Start a new Codex session after reinstalling so updated skills are loaded.
