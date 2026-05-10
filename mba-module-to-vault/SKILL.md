---
name: mba-module-to-vault
description: Ingest a single MBA course module folder (PDFs + Moodle screenshots) and produce standardised Obsidian notes in the parallel study vault — one module note plus concept, theorist, and example notes derived strictly from the module's own documents. Use when the user says "ingest this module", "digest module N", "produce vault notes for module", or points to a folder under MBA/{Unit}/Module N and asks for documentation, summary, or Obsidian notes.
---

# MBA Module → Obsidian Vault

Turns one course module into a standardised set of cross-linked Obsidian notes. Notes are grounded **only** in the documents inside the module folder.

## Directory layout (assumed)

```
<root>/MBA/<Unit>/Module N/        ← input: PDFs + Moodle screencapture PNGs
<root>/Vault/<Unit>/               ← output: parallel unit folder
    01 Modules/                    ← one module note
    02 Concepts/                   ← one note per concept
    03 Theorists/                  ← one note per theorist
    04 Examples/                   ← one note per example
    05 Readings/
    Home.md                        ← unit index — update when new entities added
<root>/Vault/Templates/            ← Module/Concept/Theorist/Example templates
```

The vault always lives **alongside** `MBA/`, not inside it. From any module path, the unit name is the parent folder; the vault unit folder is `<root>/Vault/<UnitName>/`.

## Workflow

The flow is **map-reduce**: parallel subagents extract structured data from PDFs (Phase A, read-only), then the main agent synthesises and writes everything to the vault single-threaded (Phase B). This keeps the main agent's context small and avoids write races on shared entity notes — multiple PDFs frequently introduce the same concept (e.g., "Transformational Leadership" across five papers), and parallel writers would clobber each other.

### Phase A — Survey & parallel extraction (read-only)

1. **Confirm scope.** State the module path you'll ingest and the vault unit folder you'll write into. Ask before proceeding if either is ambiguous.

2. **Survey the module folder.** Glob the folder. Separate `*.pdf` (source readings) from `screencapture-*.png` (Moodle module pages — these define the **module's structure, sub-topics, and narrative**).

3. **Read every Moodle screenshot in the main agent.** Do not delegate. The screenshots define the module's structure (title, learning outcomes, numbered sub-topics, and which entities the module explicitly introduces) — the main agent needs this context for synthesis in Phase B. Read every screenshot.

4. **Fan out: one subagent per PDF, in parallel.** Spawn one `general-purpose` subagent per PDF in a **single message with multiple Agent tool calls** (so they run concurrently). Each subagent reads its PDF using the strategic page-range approach and returns the YAML extraction defined in [REFERENCE.md](REFERENCE.md) — `## PDF extraction schema`. Use the prompt template in [REFERENCE.md](REFERENCE.md) — `## Subagent prompt template (Phase A)`. Subagents are **read-only** and do not write to the vault.

### Phase B — Synthesis & write (main agent, single-threaded)

5. **Merge extractions and deduplicate.** Collect all subagent YAML results. Group by entity name (concept, theorist, example) — the same theorist often appears across many PDFs with slight phrasing variations. Consolidate into one canonical entity with the union of pages and source filenames.

6. **Reconcile against the existing vault.** Glob `02 Concepts/`, `03 Theorists/`, `04 Examples/`. For each merged entity, decide: **(a)** already exists → link by `[[wikilink]]` + add backlink; **(b)** new → create from template.

7. **Write the module note.** Copy `Vault/Templates/Module Template.md` to `01 Modules/Module N - <Name>.md`. Populate frontmatter and every section per [REFERENCE.md](REFERENCE.md).

8. **Create new concept/theorist/example notes.** One file per entity, from the matching template. Frontmatter `module:` arrays must include `"[[Module N - <Name>]]"`. Ground every claim in a specific PDF or screenshot — no outside knowledge. **End each note with a `## Sources` section** listing the filenames (with page numbers where known) — see [REFERENCE.md](REFERENCE.md) for the format.

9. **Backlink existing notes.** For each concept/theorist/example that already existed, edit ONLY: (a) the frontmatter `module:` array, (b) the "Referenced in" / "Source module(s)" / "Related" bullet list, and (c) the `## Sources` section — append this module's filenames + pages, don't rewrite prior citations. Do not touch any other part of the body.

10. **Update `Home.md`.** Under the **Modules** section, **add** a new entry `- [[Module N - <Title>]] — <one-line summary>` for this module (or **update** the matching line if a stub like `[[Module 4]]` already exists). If the section still contains the bootstrap placeholder line (`*Modules appear here as they are ingested…*`), replace that line with the new entry. Then add new concept/theorist/example links to their respective sections.

11. **Report.** List: module note created, N new concepts (named), M new theorists (named), K new examples (named), J existing notes backlinked. Flag any entity a Phase A subagent surfaced but you couldn't ground in a specific page or screenshot.

## Hard rules

- **Source-grounding.** Every claim in every note must trace to a specific PDF, screenshot, or already-cited note in this vault. If you can't cite it, don't write it. Surface those citations in each entity note's `## Sources` section so the user can open the originals and scan for context.
- **Single-writer for Obsidian.** All vault writes happen in Phase B in the main agent. Phase A subagents are strictly read-only — never delegate writing of module notes, entity notes, backlinks, or `Home.md` to a subagent. This is what prevents two PDFs that both introduce "Transformational Leadership" from racing on the same file.
- **No body edits to existing notes.** Only frontmatter `module:` and the "Referenced in" list may change. Bodies are user-curated.
- **Australian / NFP examples preferred** for the Examples folder when the source materials offer a choice (the unit assessment requires this).
- **Wikilink everything cross-referenceable** — concepts, theorists, examples, modules. Obsidian-style `[[Title]]`.
- **Don't invent sub-topic numbering.** The numbered sub-topics (e.g. *1.4 Facets of Great Leadership*) come from the Moodle screenshots only.

## Reference

See [REFERENCE.md](REFERENCE.md) for: the exact module-note section list (which extends the bare template), frontmatter conventions, the Module 1 gold-standard example, and PDF-reading heuristics.
