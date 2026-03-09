# Doc Assistant Skill

`doc-assistant` is an installable Agent Skill for preparing semantically coherent
`chunkvec` ingest and query workflows.

## Features

It provides two modes:
- `store`: chunk text, assign stable topic labels, and store it with `cvstore`
- `search`: search the stored database with `cvquery`, inferring filters from
  natural-language intent when the cue is clear

This skill is designed for source material that needs to be stored and searched
through `chunkvec` while preserving document ids, source/derived kind,
positions, and reusable topic labels.

## Requirements

- **`cvstore` and `cvquery`**: Required to ingest and query `chunkvec` data.
- **DeepInfra API Key**: Required by `cvstore` and `cvquery`.
  - Set it via `DEEPINFRA_API_KEY` (recommended).
  - Or provide it via `config.json` next to the real `cvstore` and `cvquery`
    executables.

## Default Database

Unless the user explicitly asks for another location, the skill uses one fixed
workspace-local database path:

```text
./.doc-assistant/chunkvec.sqlite
```

This path stays the same across executions in the same workspace.

## Installation

### Using Codex
Codex recommends installing non-built-in skills using the `$skill-installer`.
Prompt Codex with:

```text
$skill-installer install the skill from repo planetis-m/doc-assistant with path .
```

### Manual Install
Clone directly into your agent's scanned skills path:

```bash
git clone https://github.com/planetis-m/doc-assistant.git ~/.agents/skills/doc-assistant
```

## Modes

### Store
Use `store` when the user wants to add material to the default workspace
database.

```text
Use $doc-assistant in store mode on notes.md.
```

```text
Use $doc-assistant to store chapter1.md in the default workspace database.
```

### Search
Use `search` when the user wants to retrieve from the default workspace
database.

```text
Use $doc-assistant in search mode for: how do embeddings help search?
```

```text
Use $doc-assistant in search mode for my derived notes about regularization.
```

```text
Use $doc-assistant in search mode for the original source chunks on vector search.
```

The skill should infer filters only when the intent is clear. For example,
`derived notes about regularization` should map naturally to `kind=derived`
plus a `label` filter for `Regularization`.

## Advanced

If the user explicitly names a database path, that path overrides the default
workspace-local database.

```text
Use $doc-assistant in search mode for chain rule, but query ./tmp/custom.sqlite instead.
```
