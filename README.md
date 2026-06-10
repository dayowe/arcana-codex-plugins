# Arcana Codex Plugins

This repository is a Codex plugin marketplace for Arcana workflows.

## Included Plugins

- `staged-implementation`: Planner, implementer, validator, and orchestrator skills for staged implementation workflows.

## Install

Add this Git repository as a Codex marketplace:

```bash
codex plugin marketplace add ssh://git@your-git-server/path/arcana-codex-plugins.git --ref main
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

After changing a plugin, commit and push the repository, then refresh the marketplace:

```bash
codex plugin marketplace upgrade arcana-codex-plugins
codex plugin add staged-implementation@arcana-codex-plugins
```
