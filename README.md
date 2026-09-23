# Arcana Codex Plugins

Codex plugin marketplace for reusable agent workflows.

## Included Plugins

- `staged-implementation`: Planner, coordinator, bounded orchestrator, implementer and validator skills for staged implementation workflows.

The current stable release is `0.2.1` on `master`. Use Coordinator for a full staged run and Orchestrator for bounded work. See the plugin README for upgrade guidance and runtime qualification requirements. Repository updates do not update installed skills automatically.

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
