---
name: mba-unit-to-vault
description: Bootstrap a new MBA unit in the Obsidian study vault — creates the unit's vault folder, Home.md (MOC), and 00 Unit notes (Unit Overview, Assessment Overview, Assessment Rubric) from the unit's overview screenshots and rubric PDF. Use when the user adds a new unit folder under MBA/, says "set up this unit", "bootstrap unit", "create the home for this unit", "init unit vault", or asks to scaffold/initialise a unit's Obsidian notes from its overview and rubric.
---

# MBA Unit → Obsidian Vault (bootstrap)

Scaffolds a new MBA unit in the parallel study vault. Creates the unit's vault folder, `Home.md` (Map of Content), and the three `00 Unit/` notes — all grounded **only** in the unit's overview screenshots and rubric PDF.

This skill is the *first* step for a new unit. After it runs, `mba-module-to-vault` ingests each module and is responsible for populating the **Modules** section of `Home.md` as it goes.

## Directory layout (assumed)

```
<root>/MBA/<Unit Folder Name>/                     ← input
    <Unit Name> overview/                          ← unit overview screenshots (Moodle)
        screencapture-*.png
    Assessment <X> - Overview.png|.pdf             ← assessment brief
    Assessment <X> - Rubric.pdf                    ← rubric
    Module 1/, Module 2/, …                        ← (ignored by this skill)

<root>/Vault/                                      ← output
    README.md                                      ← vault conventions (read for context)
    Templates/                                     ← source templates (Home, Unit Overview, Assessment Overview, Assessment Rubric)
    <Unit Folder Name>/                            ← created
        Home.md
        00 Unit/
            Unit Overview.md
            Assessment - <X> Overview.md
            Assessment - <X> Rubric.md
```

**Vault folder naming.** Mirror the MBA folder name verbatim. E.g. `MBA/BUSN20027 — Contemporary Leadership/` → `Vault/BUSN20027 — Contemporary Leadership/`.

**Unit code.** Parse it from the MBA folder name when present (e.g. the `BUSN20027` prefix before ` - ` or ` — `). If the folder name has no code prefix, check the overview screenshots; if still not found, leave the `unit_code` frontmatter blank and flag it in the report.

## Workflow

1. **Confirm scope.** State the MBA unit folder you'll bootstrap and the vault folder name you'll create. Ask before proceeding if either is ambiguous.

2. **Read vault conventions.** Read `Vault/README.md` so you match the documented folder layout, tag scheme, and note structure.

3. **Read the unit overview screenshots.** Glob `<MBA>/<Unit>/<Unit> overview/screencapture-*.png` and read every one. Extract: **verbatim unit summary**, **learning outcomes**, the **theoretical perspectives** (or equivalent foundations), **named theories/frameworks** the unit highlights, and the **assessment structure table** (each assessment + weighting). Also extract the **unit code** if it appears and wasn't already in the folder name.

4. **Read the assessment brief.** Read the `Assessment <X> - Overview.png` (or `.pdf`) at the unit root. Extract: assessment **name**, **weighting**, **length/format**, **task description**, the specific **questions to answer**, **required structure**, **task conditions**, and **submission instructions**.

5. **Read the rubric PDF.** Read `Assessment <X> - Rubric.pdf`. Extract every **criterion**, its **weight**, and the **HD descriptor** verbatim where possible. Note any strategic implications (e.g. which criteria dominate the mark).

6. **Create the vault unit folder.** Name it `<MBA folder name>/` (mirror exactly). Create `00 Unit/`, `01 Modules/`, `02 Concepts/`, `03 Theorists/`, `04 Examples/`, `05 Readings/` as empty subfolders.

7. **Write `00 Unit/Unit Overview.md`** from `Vault/Templates/Unit Overview Template.md`. Replace `{{unit_code}}` and `{{unit_name}}`. Fill the verbatim CQU summary blockquote, theoretical perspectives, named theories (as `[[wikilinks]]`), learning outcomes, assessment table, and strategic note. Ground every line in the screenshots.

8. **Write `00 Unit/Assessment - <X> Overview.md`** from `Vault/Templates/Assessment Overview Template.md`. Replace `{{assessment_name}}`, `{{assessment_type}}`, `{{weighting}}`, `{{length}}`, `{{genai_level}}`. Fill task description, the questions/requirements, required structure, conditions, submission steps, and resources. Adapt section headings if the assessment type warrants it (e.g. an essay won't have "Required presentation structure").

9. **Write `00 Unit/Assessment - <X> Rubric.md`** from `Vault/Templates/Assessment Rubric Template.md`. Replace `{{assessment_name}}` and `{{n}}` (criterion count). Duplicate the criterion section per criterion in the rubric — fill `{{criterion_name}}`, `{{weight}}`, `{{criterion_description}}`, and the HD descriptor (verbatim from the PDF). Fill **Strategic implications** with which criteria carry the most weight and where execution vs theory sit.

10. **Write `Home.md` from the template.** Copy `Vault/Templates/Home Template.md`. Replace `{{unit_code}}`, `{{unit_name}}`, `{{assessment_name}}`, `{{assessment_weight}}`. **Strip the template's `Module 1` … `Module 7` stub list** — leave just the `### Modules` heading with a single italicised line: `*Modules appear here as they are ingested via `mba-module-to-vault`.*` Leave Theories / Frameworks / Theorists / Examples sections as the template's empty stubs (`- [[]]`) — they fill in as modules are ingested. Set the **Status** section to `Bootstrapped from unit overview + rubric. Modules awaiting ingestion.`

11. **Report.** List: vault folder path created, the four notes written, unit code + name (or note if code wasn't found), assessment name + weight. Flag anything you couldn't extract from the source documents.

## Hard rules

- **Source-grounding.** Every fact in the four notes must trace to a specific screenshot or the rubric PDF. No outside knowledge of the unit.
- **Verbatim where possible.** The CQU unit summary and HD rubric descriptors are quoted as-is — they are the authoritative wording the marker uses.
- **Wikilink everything cross-referenceable** — theories, theorists, the other unit notes. Obsidian-style `[[Title]]`. Concept/theorist links will be stubs until modules are ingested; that is expected.
- **Don't ingest module contents.** This skill stops at the unit level. Module ingestion (and Home.md module-list population) is `mba-module-to-vault`'s job.
- **Don't overwrite an existing unit.** If `Vault/<Unit Folder Name>/` already exists, stop and ask before doing anything.
