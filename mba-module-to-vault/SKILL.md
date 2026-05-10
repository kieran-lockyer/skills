---
name: mba-module-to-vault
description: Ingest a single MBA course module folder (PDFs + Moodle screenshots) and produce standardised Obsidian notes in the parallel study vault — one module note plus concept, theorist, and example notes derived strictly from the module's own documents. Use when the user says "ingest this module", "digest module N", "produce vault notes for module", or points to a folder under MBA/<Unit>/Module N and asks for documentation, summary, or Obsidian notes.
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

1. **Confirm scope.** State the module path you'll ingest and the vault unit folder you'll write into. Ask before proceeding if either is ambiguous.

2. **Survey the module folder.** Glob the folder. Separate `*.pdf` (source readings) from `screencapture-*.png` (Moodle module pages — these define the **module's structure, sub-topics, and narrative**).

3. **Read the Moodle screenshots first.** They are cheap and authoritative for: module title, learning outcomes, numbered sub-topics (e.g. *1.1 Introduction*), and which concepts/theorists/examples the module explicitly introduces. Read every screenshot.

4. **Read the PDFs strategically.** Most are 10+ pages — pass `pages: "1-3"` to grab title/abstract/intro, then expand only if a paper is central. Goal: identify each PDF's core argument, named theory, and named theorist(s). Capture findings/quotes that will power the concept and example notes.

5. **Reconcile against the existing vault.** Glob `02 Concepts/`, `03 Theorists/`, `04 Examples/`. For each concept/theorist/example you've identified, decide: **(a)** already exists → link by `[[wikilink]]` + add backlink; **(b)** new → create from template.

6. **Write the module note.** Copy `Vault/Templates/Module Template.md` to `01 Modules/Module N - <Name>.md`. Populate frontmatter and every section. Mirror the structure of the gold-standard reference: see [REFERENCE.md](REFERENCE.md) for the exact section list and conventions.

7. **Create new concept/theorist/example notes.** One file per entity, from the matching template. Frontmatter `module:` arrays must include `"[[Module N - <Name>]]"`. Ground every claim in a specific PDF or screenshot — no outside knowledge.

8. **Backlink existing notes.** For each concept/theorist/example that already existed, edit ONLY the frontmatter (`module:` array) and the "Referenced in" / "Source module(s)" list to add the new module link. Do not rewrite bodies.

9. **Update `Home.md`.** Under the **Modules** section, **add** a new entry `- [[Module N - <Title>]] — <one-line summary>` for this module (or **update** the matching line if a stub like `[[Module 4]]` already exists). If the section still contains the bootstrap placeholder line (`*Modules appear here as they are ingested…*`), replace that line with the new entry. Then add new concept/theorist/example links to their respective sections.

10. **Report.** List: module note created, N new concepts (named), M new theorists (named), K new examples (named), J existing notes backlinked. Flag any concept/theorist/example you couldn't ground in a specific document.

## Hard rules

- **Source-grounding.** Every claim in every note must trace to a specific PDF, screenshot, or already-cited note in this vault. If you can't cite it, don't write it.
- **No body edits to existing notes.** Only frontmatter `module:` and the "Referenced in" list may change. Bodies are user-curated.
- **Australian / NFP examples preferred** for the Examples folder when the source materials offer a choice (the unit assessment requires this).
- **Wikilink everything cross-referenceable** — concepts, theorists, examples, modules. Obsidian-style `[[Title]]`.
- **Don't invent sub-topic numbering.** The numbered sub-topics (e.g. *1.4 Facets of Great Leadership*) come from the Moodle screenshots only.

## Reference

See [REFERENCE.md](REFERENCE.md) for: the exact module-note section list (which extends the bare template), frontmatter conventions, the Module 1 gold-standard example, and PDF-reading heuristics.
