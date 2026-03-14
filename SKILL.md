---
name: rag-tool
description: Manages text and markdown documents for semantic storage and retrieval. Use this skill to chunk, label, store, refresh, and search document collections within a reusable vector store, optionally utilizing metadata filters.
---

# RAG Tool

Follow this workflow exactly when storing or retrieving document content.

Do not add verification steps unless the user explicitly asks.

## Resolve Input

Always resolve and clean the input yourself before running `cvstore` or `cvquery`.

- Read plain text or markdown directly.
- For PDFs or other binary documents, use extracted text, not the binary file itself.
- Do not use scripts or programmatic pipelines to generate cleaned input text.
- Do not invent or infer content that is not present in the source.
- Rewrite only when needed to remove decorative boilerplate, repeated navigation text, or formatting noise that would hurt retrieval quality.
- Preserve the source meaning exactly. Do not add new facts.

## Tool Readiness

- Check for `cvstore` and `cvquery` with `command -v`.
- If either command is missing, read [references/chunkvec-install.md](references/chunkvec-install.md) and attempt installation.
- Check again after installation. If a required command is still missing, stop and report the failure.
- Request unrestricted network or escalated execution directly in the tool call for `cvstore` and `cvquery`.
  Do not run sandboxed probe calls first.
- Do not inspect shell profiles, environment files, or arbitrary filesystem files to discover API keys.
- If `cvstore` or `cvquery` reports an auth or config failure, report the error and ask the user to configure `DEEPINFRA_API_KEY` or `config.json`, then retry.

## Mode Selection

Map user intent into exactly one of these modes:

- `store`: the user wants to add or refresh content
- `search`: the user wants retrieval from stored content

Use `store` for requests such as:

- store this file
- ingest these notes
- add this document

Use `search` for requests such as:

- search my notes
- find what the source says about the new policy
- query the stored material for project guidelines

After choosing the mode, read only the matching mode reference:

- `store` -> [references/store-mode.md](references/store-mode.md)
- `search` -> [references/search-mode.md](references/search-mode.md)

Do not load both mode files unless the request truly spans both workflows.

## Stable Doc IDs

`doc` ids must stay deterministic across store and search runs.

When the user provides a source path, filename, or explicit document title,
derive `doc` from that same source identity before running either tool.

Derive a lowercase kebab-case `doc` from one stable source identity:

- prefer the source file stem, such as `chapter1.md` -> `chapter1`
- otherwise use an explicit logical title supplied by the user
- do not invent a fresh one-off name when later retrieval may depend on stability

Keep `doc` focused on document identity, not artifact type.

Examples:

- source text for `chapter1.md` -> `chapter1`
- derived notes for `chapter1.md` -> `chapter1`
- quiz for `chapter1.md` -> `chapter1`

If the user provides pasted text with no stable file or title and later
doc-specific retrieval may matter, ask for a short logical name before storing.

## Shared References

- For chunking and label examples during ingest, read [references/chunking-and-labels.md](references/chunking-and-labels.md).
