# Arcana Codex Plugins

Codex plugin marketplace for reusable agent workflows.

## Included Plugins

- `staged-implementation`: Planner, implementer, validator, and orchestrator skills for staged implementation workflows.

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
