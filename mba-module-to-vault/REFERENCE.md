# Reference — MBA Module → Vault

## Module note structure (extends the bare template)

The bare `Module Template.md` is minimal. The gold-standard module notes already in this vault (e.g. `Module 1 - Why Leadership Matters.md`, `Module 3.md`) extend it with these sections — match them:

```markdown
---
title: Module N - <Name>
tags: [module, module/N]
module_number: N
---

# Module N — <Name>

## One-line summary
<one sentence positioning the module's central argument, with [[wikilinks]] to the dominant theories>

## Sub-topics
- **N.1 Introduction** — <short gloss>
- **N.2 <topic>** — <gloss; wikilink any concept/theorist named here>
- ...
(Numbering and titles come from the Moodle screencapture pages — do not invent.)

## Key concepts introduced
- [[Concept A]]
- [[Concept B]]

## Key theorists introduced
- [[Theorist Name]] — <one-line attribution: founder of X (year); operationalised Y>

## Australian / NFP examples
- [[Example Org]] — <one-line of how the module's theory shows up here>

## Strategic takeaway for the presentation
<2–3 sentence paragraph. The unit's assessment is a presentation; this section names the rubric-relevant vocabulary the module supplies.>

## Source readings
- Author (Year) — short title
- ...
(One bullet per PDF in the module folder. Use the PDF's actual title, not the filename.)
```

## Frontmatter conventions for entity notes

**Concept** (`02 Concepts/<Title>.md`):
```yaml
---
title: <Title>
tags: [theory]                    # or [framework] for practical models
module: ["[[Module N - <Name>]]"] # array; append on backlink
theorist: ["[[Theorist A]]", "[[Theorist B]]"]
related: ["[[Related Concept]]"]
---
```

**Theorist** (`03 Theorists/<Name>.md`):
```yaml
---
title: <Name>
tags: [theorist]
era: <years if known, else blank>
nationality: <if known>
associated_theories: ["[[Theory]]"]
---
```
The Theorist template has no `module:` field — instead, append the new module to the **"Referenced in"** bullet list at the bottom of the body.

**Example** (`04 Examples/<Title>.md`):
```yaml
---
title: <Title>
tags: [example, example/australian]   # add example/nfp if NFP
sector: <sector>
country: Australia                      # default
leaders: [<Name>]
theories_illustrated: ["[[Theory]]"]
---
```
Examples link back to modules via a **"Related"** bullet list at the bottom of the body (see `Qantas - Alan Joyce.md`).

## Reading PDFs efficiently

- A typical module has 15–25 PDFs of 10–30 pages each. Reading every page is wasteful.
- **First pass:** `Read` with `pages: "1-3"` to capture title, abstract, and intro. Most papers reveal their core argument and named theory in the abstract alone.
- **Second pass (only if central):** read the discussion/conclusion pages of the 3–5 papers most central to the module's theme.
- For each PDF, capture: (1) named theory/framework, (2) named theorist(s), (3) one quotable finding, (4) any organisation named as an example.

## Reading Moodle screenshots

- The screencapture-*.png files are screenshots of the module's pages on the LMS (`bedifferent-cqu-edu-au`).
- Their filenames are timestamps, not topics — the only way to know what's on each is to read them.
- They contain: module title, learning outcomes, numbered sub-topics (1.1, 1.2, ...), inline definitions of theories, and named theorists/examples.
- **The Sub-topics section of the module note is built from these screenshots** — not from the PDFs.

## Identifying examples

The unit prefers **Australian** and **NFP** examples. Sources of examples:
1. Named organisations inside PDFs (case studies, vignettes).
2. Organisations named on the Moodle screenshots.
3. The screencaptures occasionally include external pages (e.g. `screencapture-startsmartcee-org-...`) — these are also valid sources.

If the module's documents only mention international/non-NFP examples, document those — don't invent Australian examples to satisfy the preference.

## Backlinking existing notes (the only edits you may make to them)

When a concept/theorist/example already exists in the vault:

**Concepts and Examples** — edit the YAML frontmatter to append to the `module:` (concept) or add a new "Related" bullet pointing at the new module note (example).

**Theorists** — edit ONLY the "Referenced in" bullet list at the bottom of the file. Append `- [[Module N - <Name>]]` if not already present.

Never modify the body of an existing note. If the new module materially changes the picture (e.g., a paper contradicts an existing claim), surface it in the report at step 10 and let the user decide.

## Home.md updates

`Vault/<Unit>/Home.md` is a manually-curated index. Targeted edits only:

- **Modules section** — if the module's existing entry is a stub like `[[Module 4]]`, replace it with the new full title `[[Module N - <Name>]] — <one-line gloss>`.
- **Core Leadership Theories / Major Frameworks** — append new concept wikilinks to the appropriate sub-list. Use the existing classification (theory vs framework) as a guide.
- **Theorists** — append new theorists with a short `— <attribution>` gloss matching the existing style.
- **Australian & NFP Examples** — append under Corporate / Public-Crisis / NFP & Social Enterprise / Individuals / International, matching the existing classification.

Don't reorder existing entries. Don't touch the Tag Index or Status sections.

## Gold-standard reference

`Vault/Contemporary Leadership/01 Modules/Module 1 - Why Leadership Matters.md` is the canonical example. Read it before writing a new module note — match its tone, level of detail, and section structure.
