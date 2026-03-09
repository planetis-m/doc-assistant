# Doc Assistant Skill

`doc-assistant` is an installable Agent Skill for preparing semantically coherent
`chunkvec` ingest and query workflows.

## Features

It provides two modes:
- `store`: chunk text, assign stable topic labels, and store it with `cvstore`
- `search`: search the stored database with `cvquery`, using filters only when
  the user explicitly asks to narrow scope

This skill is designed for source material that needs to be stored and searched
through `chunkvec` while preserving document ids, source/derived kind,
positions, reusable topic labels, and provenance paths when available.

`doc` names the document identity, such as `chapter1`. Use `kind` for `source`
versus `derived`. Subtypes such as notes, quiz, or flashcards are not
first-class exact query filters in the current CLI.

## Requirements

- **`cvstore` and `cvquery`**: Required to ingest and query `chunkvec` data.
- **DeepInfra API Key**: Required by `cvstore` and `cvquery`.
  - Set it via `DEEPINFRA_API_KEY` (recommended).
  - Or provide it via `config.json` next to the real `cvstore` and `cvquery`
    executables.

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
Use `store` when the user wants to add material to the search database.

```text
Use $doc-assistant in store mode on notes.md.
```

```text
Use $doc-assistant to store chapter1.md.
```

```text
Use $doc-assistant to store derived notes for chapter1.md.
```

### Search
Use `search` when the user wants to retrieve from the stored material.

```text
Use $doc-assistant in search mode for: how do embeddings help search?
```

```text
Use $doc-assistant in search mode and search only in my chapter1 notes for regularization.
```

```text
Use $doc-assistant in search mode and search within the chapter1 source for vector search.
```
