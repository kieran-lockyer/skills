# Reference — MBA Module → Vault

## Module note structure (extends the bare template)

The bare `Module Template.md` is minimal. Extend it with the sections below — see the **worked example** at the bottom of this file for tone, depth, and wikilink density to match:

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
- Author (Year) — short title — `<filename>.pdf`
- ...
(One bullet per PDF in the module folder. Use the PDF's actual title, then append the filename in backticks so the user can open the file directly.)
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
Examples link back to modules via a **"Related"** bullet list at the bottom of the body — one `- [[Module N - <Name>]]` line per linking module, in module-number order.

## Source citations on entity notes

Every concept, theorist, and example note must end with a `## Sources` section listing the documents inside the module folder where the entity is discussed. This is what lets the user open the originals and verify or extend a claim — without it, source-grounding is invisible.

Format — one bullet per source file, page numbers where known:

```markdown
## Sources
- `Bass-1985-leadership-performance.pdf` — pp. 12-14, 31
- `Burns-1978-leadership.pdf` — abstract; pp. 4
- `screencapture-bedifferent-cqu-edu-au-1.4.png` — sub-topic 1.4
```

Rules:
- **Filename only** (no path) — the file lives in the module folder.
- **Cite every source** that mentions the entity, not just the first one found. If a theorist appears in three PDFs, list all three.
- **Pages are a bonus, not mandatory.** Add them when you read a specific page; skip when the citation comes from a screenshot caption or only an abstract. Use `pp. N-M` for ranges, `p. N` for single pages, `abstract` / `intro` / `conclusion` for unpaginated front/back matter.
- **When backlinking an existing entity note from a new module, append** to the Sources section — don't replace prior citations. Group by module if it helps readability:
  ```markdown
  ## Sources
  - `Burns-1978.pdf` — pp. 4 *(Module 1)*
  - `Kotter-1990.pdf` — pp. 103-111 *(Module 4)*
  ```

## PDF extraction schema (Phase A subagent return value)

Each Phase A subagent reads exactly one PDF and returns this YAML extraction. Use the exact field names — the main agent merges by these keys, so a typo in a subagent's output silently breaks dedup.

```yaml
filename: <exact filename from the module folder, with extension>
title: <PDF's actual title from title page or first heading>
authors: [<Surname, Given>, ...]
year: <YYYY>
core_argument: <one sentence — the paper's central claim>
skim_depth: <abstract-only | abstract+intro | full first-pass | central deep-read>

named_theories:
  - name: <e.g., Transformational Leadership>
    pages: [<int>, ...]              # pages where this theory is named or defined
    note: <optional — one clause if the paper redefines or extends it>

named_theorists:
  - name: <e.g., Bernard Bass>
    role: <author | operationaliser | cited founder | critic | applied>
    pages: [<int>, ...]
    attribution: <optional — "founder of X (year)" line for the module note's Key theorists section>

named_examples:
  - organisation: <e.g., Qantas>
    pages: [<int>, ...]
    sector: <if stated>
    country: <if stated>
    one_line: <how this org illustrates the paper's theory>

quotable_findings:
  - quote: "<verbatim, ≤25 words>"
    page: <int>
    relevance: <one clause — why this is quotable for the module note>
```

**Empty arrays are valid** — not every PDF names a theorist or example. Never invent entries to fill an array. If `named_examples` is empty, the paper simply didn't name an example.

## Subagent prompt template (Phase A)

When spawning each Phase A subagent, use a `general-purpose` agent and this prompt — substitute `<MODULE_FOLDER>` and `<FILENAME>`:

> You are extracting structured metadata from a single academic PDF for an MBA module ingestion. Read **only** the file at `<MODULE_FOLDER>/<FILENAME>` — do not read other files in the folder, do not write any files, do not create vault notes. The main agent will synthesise across PDFs and handle all writes.
>
> **Reading strategy.** Start with `Read` and `pages: "1-3"` to grab title, abstract, and intro. Most papers reveal their core argument and named theory in the abstract alone. Only deepen (read discussion/conclusion) if the paper is clearly central to the module — multiple named theories, a foundational author, or the abstract promises a major case study.
>
> **Return ONLY the YAML extraction** in the schema below — no prose, no commentary, no markdown headings, no closing summary. The main agent parses your output as YAML.
>
> [paste the schema block above verbatim]
>
> Hard rules:
> - `filename` field: the bare filename, no path. The main agent knows the folder.
> - Page numbers: use the PDF's printed page numbers if present, else the logical PDF page index. Be consistent within your response.
> - No outside knowledge. If the PDF doesn't name a theorist or organisation, return an empty array — never fill from training data.
> - Quotes verbatim, ≤25 words. Trim with `…` rather than paraphrasing.
> - Set `skim_depth` honestly — it tells the main agent how much weight to give your extraction.

The main agent should fan out **all subagents in a single message** (multiple Agent tool calls in one response) so they execute in parallel.

## Reading PDFs efficiently

- A typical module has 15–25 PDFs of 10–30 pages each. Reading every page is wasteful.
- **First pass:** `Read` with `pages: "1-3"` to capture title, abstract, and intro. Most papers reveal their core argument and named theory in the abstract alone.
- **Second pass (only if central):** read the discussion/conclusion pages of the 3–5 papers most central to the module's theme.
- For each PDF, capture: (1) named theory/framework, (2) named theorist(s), (3) one quotable finding, (4) any organisation named as an example, (5) the **page number** where each lands. Page numbers feed the `## Sources` section of every entity note — record them as you read, not from memory afterwards.

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

## Worked example

A module note that hits the right tone and depth. Note the wikilink density (almost every named theory, theorist, and example is linked); the inline attribution after each theorist (`founder of X (year); operationalised Y`); and the rubric-aware framing in *Strategic takeaway*.

> **Convention drift:** this example predates the source-citation convention above. When you write a new module note, extend each *Source readings* line with the filename in backticks (e.g. `Bass (1985) — Leadership and Performance Beyond Expectations — \`bass-1985-leadership-performance.pdf\``), and ensure every entity note (concept/theorist/example) created from this module ends with a `## Sources` section.

````markdown
---
title: Module 1 - Why Leadership Matters
tags: [module, module/1]
module_number: 1
---

# Module 1 — Why Leadership Matters

## One-line summary
Establishes the foundational theoretical lens for the unit, positioning [[Transformational Leadership]] as the central paradigm and contrasting it against [[Transactional Leadership]].

## Sub-topics

- **1.1 Introduction** — why contemporary leadership matters in complex organisational landscapes
- **1.2 Leadership Perspectives** — the [[Leadership Styles Wheel]] and [[Path-Goal Theory]]
- **1.3 Transformational vs Transactional Leadership** — reciprocal nature of transformational leadership
- **1.4 Facets of Great Leadership** — trust, credibility, communication (Schmidt-Wilk, 2022)
- **1.5 Values and Behaviours**
- **1.6 Understanding Leadership — The Third Metric** (Huffington: physical + mental wellbeing + work-life balance)
- **1.7 Crucial Skills for Tomorrow's Leaders** — tension management, trust, whole-self development
- **1.8 Role of Tomorrow's Leaders** — [[Six Keys to Leading Positive Change]] (Kanter)
- **1.9 Leadership in the Age of Innovation** — embracing discomfort
- **1.10 Summary**

## Key concepts introduced

- [[Leadership Styles Wheel]]
- [[Transformational Leadership]]
- [[Four I's of Transformational Leadership]] (Bass)
- [[Transactional Leadership]]
- [[Path-Goal Theory]]
- [[Six Keys to Leading Positive Change]]
- [[Trait Theory]]
- [[Behaviour Theory]]
- [[Contingency Theory]]

## Key theorists introduced

- [[James MacGregor Burns]] — founder of transformational leadership (1978)
- [[Bernard Bass]] — operationalised Burns (1985); "4 I's"
- [[Robert House]] — Path-Goal theory
- [[Rosabeth Moss Kanter]] — Six Keys / Kanter's Law

## Australian / NFP examples

- [[Qantas - Alan Joyce]] — transactional/transformational hybrid through COVID
- [[FYA - Jan Owen]] — transformational leadership in NFP sector
- [[BHP]] — safety leadership as transactional/transformational mix
- [[Woolworths]] — [[Path-Goal Theory]] in store manager coaching
- [[The Star Entertainment Group]] — credibility collapse (Bell Inquiry 2022)
- [[Atlassian]] — innovation through discomfort (ShipIt)
- [[Sam Mostyn]] — Kanter's Six Keys embodied in an Australian leader

## Strategic takeaway for the presentation

Module 1 provides the **core theory vocabulary**. If the media article depicts a leadership failure or transformation, the slides should explicitly name the theory being applied or violated (e.g. *"a breakdown of idealised influence"*, *"transactional KPIs crowded out transformational vision"*). This directly supports Rubric Criterion 1 ([[Assessment - Presentation Rubric|Identification of task, 25%]]).

## Source readings (peer-reviewed, from module folder)

- Ahn et al. (2011) — From classical to contemporary leadership challenges
- Bass-era foundation: Burns (1978); Bass (1985)
- Dai et al. (2013) — Transformational vs transactional leadership
- Dartey-Baah (2015) — Resilient leadership: a transformational-transactional mix
- Deichmann & Stam (2015) — Leveraging transformational and transactional leadership
- Hamstra et al. (2014) — Leadership and followers' achievement goals
- Hussain et al. (2017) — Transactional leadership and creativity
- Afshari & Gibson (2016) — Increasing commitment through transactional leadership
- Nguyen et al. (2017) — Transformational-leadership style and management control
- Sy et al. (2018) — Charismatic leadership: eliciting and channeling emotions
- Levay (2010) — Charismatic leadership in resistance to change
- Spector (2014) — Problems and promise of contemporary leadership theories
- Schmidt-Wilk (2022) — Facets of Leadership
````
