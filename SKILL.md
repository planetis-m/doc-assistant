---
name: doc-assistant
description: Prepare source and derived documents for marked-up storage and query using concrete document ids plus the `doc`/`kind`/`position`/`label` metadata contract. Use when a task involves structuring text or markdown into chunked document inputs or writing filtered query inputs.
---

# Doc Assistant

Follow this workflow to prepare document content for marked-up ingest and filtered search.

## Resolve Input

This skill is text-first.

- If the source is plain text or markdown, read it directly.
- If the source is a PDF or another binary document, work from a text or markdown transcription rather than the binary file itself.
- Do not invent content that is not present in the provided source.

## Choose Metadata

Every stored chunk must carry exactly these fields:

- `doc`: stable logical document id for one stored artifact
- `kind`: one of `source`, `derived`
- `position`: integer locator within that `doc`
- `label`: optional short topic or title string

Apply these rules:

- Use lowercase kebab-case for `doc`, such as `ml-unit-3`, `ml-unit-3-notes`, or `calc-book-ch-4`.
- Use a separate `doc` id for each stored artifact, such as a source chapter, lecture rewrite, notes file, or quiz bundle.
- Use `kind=source` for original material or faithful transcription.
- Use `kind=derived` for generated or rewritten material, including notes, lectures, quizzes, flashcards, essays, and summaries.
- `position` is required even when the source has no native numbering. In that case, assign sequential positions.
- Multiple chunks may share the same `position`. the store keeps chunk order separately through `ordinal`.
- For quizzes, keep the question and its answer in the same chunk so retrieval returns a complete item without extra linking metadata.

## Produce Ingest Input

Write the material as repeated `<chunk ...>` markers plus non-empty chunk bodies.

Example:

```text
<chunk doc="ml-unit-3" kind=source position=12 label="Regularization">
Dropout disables random activations during training.

<chunk doc="ml-unit-3-notes" kind=derived position=12 label="Regularization">
Regularization reduces overfitting by constraining model behavior.
```

Rules:

- Do not emit legacy `<page ...>` markers.
- Keep only the 4 supported metadata fields. Do not invent extra attributes.
- Trim decorative boilerplate that hurts retrieval quality.
- Keep chunk bodies non-empty and semantically coherent.
- Prefer chunks that stand on their own during retrieval.

## Produce Query Input

When constrained retrieval is needed, write a query file with an optional leading `<search ...>` header, then a blank line, then the semantic query text.

Example:

```text
<search doc="ml-unit-3-notes" kind=derived position=12 label="regularization">

Why does dropout reduce overfitting?
```

Query filter behavior:

- `doc` is exact match
- `kind` is exact match
- `position` is exact integer match
- `label` is normalized substring match

Use only the filters the user actually needs.
