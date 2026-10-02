---
name: viverse-docs-improver
description: Audit and improve VIVERSE technical documentation for information architecture, task completion, style-guide compliance, technical accuracy, readability, discoverability, and non-AI-sounding prose. Use when auditing or improving VIVERSE GitBook docs. This skill decides what should change and why. For approved Improve-mode file edits, load the sibling write-docs skill for GitBook syntax; do not copy GitBook block recipes into this skill.
---

# VIVERSE docs improver

Use this skill to audit or improve documentation in the VIVERSE docs repository.

## Source labels

Every rule in this skill is marked with one source. Do not present Proposed or no-ai-slop rules as official VIVERSE policy.

- **Official VIVERSE rule** — from the VIVERSE Technical Documentation Style Guide PowerPoint
- **No-AI-slop rule** — from Peter Yang's [no-ai-slop](https://github.com/petergyang/no-ai-slop) skill
- **Proposed automation rule** — IA, technical-safety, stop-condition, and review workflow added for agent use

When a heading or bullet has no inline label, the nearest section label applies.

## Priority order

**Proposed automation rule**, with Official constraints.

When rules conflict, use this order:

1. Technical accuracy and factual preservation (**Proposed automation rule**)
2. Official VIVERSE style guide, including document types, title formulas, terminology, accessibility, and SEO
3. GitBook `write-docs` for how an approved change is represented (syntax, blocks, `SUMMARY.md`, Git Sync)
4. Proposed information-architecture and journey findings — recommend; do not silently rewrite navigation or invent pages
5. No-AI-slop language cleanup, only where it does not fight Official structure or terminology

Never sacrifice technical accuracy, established terminology, API semantics, warnings, version-specific behavior, or required caveats for stylistic cleanliness.

Official VIVERSE document-type structure and official product terms outrank no-ai-slop heuristics. Do not apply no-ai-slop rules that ban inanimate subjects, required lists or numbered steps, required H2 intro sentences, or repeated official terms.

## Modes

**Proposed automation rule.**

Default the first run on a section to **Audit**. Do not rewrite production pages unless the user explicitly asks for Improve mode.

### Audit

Use when asked to review a page, folder, section, or the whole documentation set without rewriting.

Return:

- executive summary
- highest-impact findings
- information architecture findings
- page-structure findings
- style-guide violations
- discoverability/SEO findings
- accessibility findings
- technical-risk findings
- recommended actions, ordered by impact
- pages or claims requiring human review
- missing pages that a reader journey needs

Load [eval.md](eval.md) and apply the **Audit-applicable** sections page by page. Return a compact scorecard per page: failures, useful passes, and N/A that would change a decision. Do not assign numeric scores. Do not dump every checkbox. Group recommendations by page with impact and effort. Hidden pages get the same rubric plus the visibility finding; do not unhide without owner approval. A failed Audit check is a finding, never an automatic edit. Improve-only sections are N/A.

Do not rewrite entire pages unless asked. Do not invent a page to complete a journey.

### Improve

Use only when asked to rewrite or improve documentation.

For each page:

1. identify audience and reader goal
2. classify the document type
3. preserve facts, code, limits, warnings, versions, and terminology
4. fix structure before sentence-level prose
5. apply Official VIVERSE style rules
6. apply allowed no-ai-slop cleanup
7. before writing files, read [`.cursor/skills/write-docs/SKILL.md`](../write-docs/SKILL.md); load write-docs `references/` on demand
8. run the complete evaluation checklist in [eval.md](eval.md) and resolve applicable failures before returning the draft unless a human decision is required
9. return the revised page plus a concise change report

### Structure

Use when asked to evaluate navigation, hierarchy, or documentation architecture.

Review:

- top-level navigation labels
- folder/page grouping
- whether labels make sense without internal VIVERSE knowledge
- whether SDKs, engines, publishing, monetization, optimization, examples, and troubleshooting are discoverable
- task-based developer journeys
- orphaned, hidden, or buried pages
- duplicate or overlapping categories
- missing landing pages
- cross-linking
- naming consistency
- likely search terms vs internal terminology

Recommend structural changes. Do not apply a navigation migration unless a human owner asks for it.

If the user asks to apply navigation changes, and those changes touch `SUMMARY.md`, `.gitbook.yaml`, or nav files, read write-docs and load `references/configuration.md` before editing.

## GitBook execution

**Proposed automation rule.**

This skill is policy and judgment. It decides *what* should change and *why*. It does not teach GitBook syntax.

The sibling skill [`.cursor/skills/write-docs/SKILL.md`](../write-docs/SKILL.md) is platform execution. It decides *how* an approved change is represented in GitBook. Do not copy GitBook block syntax into this file. Load write-docs instead.

### When to load write-docs

- **Audit:** this skill only. Report GitBook formatting gaps at policy level (callouts should be hints; sequential procedures exist, and a stepper may be appropriate). Do not rewrite. Do not treat a valid numbered list as a defect.
- **Improve:** after the change is approved, read write-docs before editing files. Load write-docs `references/` on demand (`blocks.md`, `frontmatter.md`, `configuration.md`).
- **Structure:** if `SUMMARY.md`, `.gitbook.yaml`, or nav files change, also load write-docs `references/configuration.md`.

If VIVERSE policy and GitBook syntax conflict, keep the VIVERSE requirement and let write-docs plus existing page/space convention choose the representation. Official sequential numbered steps is the requirement. A GitBook stepper is one valid representation, not a mandatory rewrite of every numbered procedure.

### Policy to platform map

Hand these VIVERSE requirements to write-docs. Do not embed tag recipes here.

- Callouts that are not decorative → hints (`info` / `warning` / `danger`)
- Sequential numbered steps on Setup / Tutorial / Examples → use a GitBook stepper when the page/space convention and write-docs guidance make it appropriate; otherwise preserve a valid numbered list
- Engine or platform alternatives → tabs
- Page description / SEO meta → frontmatter `description:`
- Nav / new pages → `SUMMARY.md` plus matching files
- Reusable repeated warnings → include, only if the space already uses includes

Point at write-docs for syntax, closing tags, YAML quoting, and Git Sync pitfalls.

### Existing space conventions

Live Developer Tools pages already use frontmatter `description`, an H1 in the file, optional `***` after the description, hints, and sometimes steppers. Improve mode should match that page's existing convention, not invent a second template or convert every numbered list into a stepper. Keep Official no decorative callouts, no H4+, and 1–2 sentences after each H2. When a stepper is used, step titles stay H3.

## Reader-first writing

**Official VIVERSE rule** for audience-first openings and task-oriented wording. **Proposed automation rule** for using that idea in navigation audits.

Write for what the reader is trying to accomplish, not for what VIVERSE wants to describe.

Use the opening paragraph and, when the page is procedural, the title to state the reader goal.

Do **not** treat conceptual overview titles as weak. Informative pages may use titles such as "VIVERSE SDK Overview" or "Polygon Streaming intro" when the page explains what something is. The official style guide lists **VIVERSE SDK Overview** as a valid Informative example.

Prefer task titles on Setup, Tutorial, and Examples pages:

- "Install the VIVERSE Unity SDK"
- "Publish a Unity WebGL build"
- "Add VIVERSE authentication"

Keep conceptual titles on Informative pages:

- "VIVERSE SDK Overview"
- "What is Polygon Streaming?"

## VIVERSE document types

**Official VIVERSE rule.**

Classify every page as one of these before editing.

### Informative

Use for concepts, references, and overviews.

Goal: build understanding.

Title formulas: "What is [Feature]?", "Troubleshooting [Feature]". A specific overview title is valid when the topic is named, for example "VIVERSE SDK Overview". Avoid a bare "Overview" with no feature or goal.

Use:

- explanatory prose
- tables for catalogs/comparisons; always include a header row; do not use tables for layout only
- callouts for limitations or clarifications; do not use callouts as decorative emphasis

Do not force numbered procedures into an informative page. If the page becomes procedural, reclassify it as Setup or Tutorial.

### Setup

Use for installation, configuration, and getting something working.

Goal: get something working. Setup ends when the thing works.

Title formulas: "Quickstart: [Goal]", "Getting Started with [Feature]". Avoid vague titles like "Overview" with no specific feature or goal.

Require:

- sequential numbered steps
- short directive step titles
- enough detail that the reader cannot get lost
- screenshots when UI steps need visual support; keep screenshots web-friendly and no more than 1080 px on the longest side
- callouts before version-specific behavior, limitations, or prerequisites
- tables for configuration options, supported file types, or setting comparisons when a list is harder to scan; include a header row; do not use tables for layout only
- a clear end state

**Proposed automation rule:** include a prerequisites section when prerequisites exist.

### Tutorial

Use for guided, skill-building journeys.

Goal: build a skill. Tutorial ends when the reader has learned a skill, not only when something is running.

Title formulas: "How to [do the thing]", "[Scenario] walkthrough". Do not use Setup title framing.

Require:

- sequential numbered steps
- short directive steps plus enough context that the reader understands what they are doing and why
- screenshots or diagrams at meaningful decision points; keep them web-friendly and no more than 1080 px on the longest side
- scoped examples
- callouts for prerequisites, learning checkpoints, and common mistakes before the reader encounters them
- technical references, engine examples, and code snippets kept tightly scoped to the tutorial goal; prefer lists when they scan better
- a clear learning outcome

**Proposed automation rule:** include a prerequisites section when prerequisites exist.

### Examples

Use when the main value is a working pattern, sample, template, or demo the reader can copy and adapt.

Goal: provide a copyable pattern.

Title formulas: "Quickstart: [Goal]", "Getting Started with [Feature]". Avoid a bare "Overview".

Require:

- sequential numbered steps
- short directive steps with enough detail that the reader cannot get lost
- screenshots for UI-driven steps; keep them web-friendly and no more than 1080 px on the longest side
- callouts for limitations, prerequisites, or version-specific behavior before the reader encounters them
- tables for configuration options, supported file types, or setting comparisons when a list is harder to scan; include a header row; do not use tables for layout only

Official good examples of this type include Artefact Hunt and Pet Rescue Template Project.

**Proposed automation rule:** also state what the sample demonstrates, point to the code or project, and list limitations or version notes when those facts already exist. Do not invent them.

## Required page structure

**Official VIVERSE rule.**

Every page should have:

1. A single H1 supplied by the page title, not repeated manually in the body.
2. A concise opening description explaining what the reader will learn, do, or understand. This is also the natural SEO entry point.
3. A horizontal rule after the page description and before the main body.
4. H2 sections for major content areas.
5. H3 only for subsections, step titles, or deeper grouping within an H2.
6. No H4 or deeper headings. Use bold text only when another visual sub-level is genuinely needed.
7. One or two context sentences immediately after each H2 before steps, tables, or lists.

Use sentence case for headings. Write "Upload your first world," not "Upload Your First World."

Headings should state a clear intent. "How players purchase content" beats "Purchasing." Vague one-word headings are weak. A named overview title on an Informative page is not vague.

## Voice and tone

**Official VIVERSE rule.**

Use:

- helpful, clear, confident language
- active voice by default; use passive voice only when the actor is unknown or irrelevant
- second person when direct reference helps
- present tense by default: write "The SDK returns a token," not "The SDK will return a token"
- plain language: "start" over "initiate"; "use" over "utilize"
- American English: "color" not "colour"; "customize" not "customise"
- Oxford comma: "publish, share, and monetize"

Avoid:

- stuffy phrasing
- over-casual phrasing
- marketing language in instructional docs, including "innovative," "robust," and "seamless"
- jargon when a simpler word is accurate
- internal terminology that readers would not know
- "the user" or "the player" when "you" is appropriate

Official good active-voice example: "The SDK returns an authentication token after you call the login method."

## Terminology

**Official VIVERSE rule.**

Use one term per concept.

Do not rotate synonyms for style. Do not alternate "world," "space," and "environment" for the same thing.

Repeat the correct product or API term when precision requires it.

Never replace an official technical term with a friendlier synonym if doing so changes meaning or makes search harder.

## Technical safety rules

**Proposed automation rule.**

Never invent or silently change:

- API behavior
- method names
- parameter names
- event names
- code semantics
- package names
- SDK names
- supported engines/platforms
- version numbers
- prerequisites
- authentication behavior
- networking behavior
- limits
- requirements
- security guidance
- warnings
- compatibility statements

If content appears contradictory, incomplete, outdated, or technically suspicious, flag it for human review instead of guessing.

Treat code as source material, not stylistic prose.

## Code and UI formatting

**Official VIVERSE rule.**

- Use fenced code blocks with a language identifier.
- In GitBook/Markdown, the style-guide "single apostrophe" / inline-code rule means wrap filenames, commands, parameters, values, method names, and code identifiers in inline code (backticks), not a literal apostrophe.
- Bold UI controls and menu items the reader must interact with: "Select **Upload**, then choose **Publish**."
- Keyboard shortcuts may use bold or code style. Write them platform-neutral, for example `Ctrl+S` (Windows) / `Cmd+S` (Mac). Spell key names normally: Ctrl, not CTRL.
- Require descriptive alt text for every screenshot and diagram. Describe what the reader needs to understand. Do not write "Screenshot of dashboard," "image of...," or equally empty text.

Do not alter working code merely to match prose style. (**Proposed automation rule.**)

## Links and references

**Official VIVERSE rule.**

- Never use vague anchor text such as "click here" or "read more."
- Make the destination or action clear from the link text.
- Prefer canonical, stable, or versioned URLs.
- Do not deep-link to UI states that may change between releases.
- Cross-link related documentation when it helps the reader continue a task.
- Do not over-link; each link creates a possible exit from the current path.
- All external links require editor approval before publication. Flag every new or changed external link for editor review.

## SEO and discoverability

**Official VIVERSE rule**, except the last bullet.

Check:

- page title contains the likely reader/search phrase, using the document-type title formulas above
- opening paragraph naturally states the page purpose and primary topic
- page description is written intentionally as the meta description and naturally includes the primary keyword
- GitBook shows at most 200 characters of `description:`. Count the folded text (line breaks become single spaces). Stay at or under 200 characters and end on a complete sentence. A longer description is cut off on the page. Put extra detail in the body. (**Proposed automation rule**)
- H2s use terms readers are likely to search for, not only internal terminology
- duplicate explanations are consolidated and cross-linked
- titles are specific before publication because they influence GitBook URLs; renaming later can disrupt rank or force a redirect
- internal links form useful content clusters
- GitBook indexes page titles and H2s heavily
- docs not reviewed in 6+ months are flagged for content audit
- broken internal links are reviewed periodically, especially after GitBook page renames
- search-performance data from Google Search Console, Semrush, or a similar source is used when available to align headings with reader language

**Proposed automation rule:** surface buried but important content from landing pages or nearby task-oriented pages. Do not keyword-stuff.

## Source standards for edge cases

**Official VIVERSE rule** that these three sources may be consulted. The consultation order is a **Proposed automation rule**.

When the VIVERSE guide does not resolve an edge case, consult these standards in this order while preserving VIVERSE-specific rules:

- [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide) for voice, word choice, UI terminology, accessibility, and global writing guidance
- [Google Developer Documentation Style Guide](https://developers.google.com/style) for API docs, code samples, heading hierarchy, second-person style, and link text
- [WCAG 2.1](https://w3.org/WAI/WCAG21/quickref) for accessibility, alt text, heading structure, link text, and color usage

Do not use an external standard to override an explicit VIVERSE rule. (**Proposed automation rule.**)

## Accessibility

**Official VIVERSE rule.**

Require:

- descriptive alt text that is not "image of..."
- sequential heading levels; never skip H1 to H3
- header rows in tables
- no tables used only for layout
- no color-only meaning
- self-describing link text
- Grade 8–10 Flesch-Kincaid as a directional target

Technical terms such as "WebGL," "SDK," and "iframe" will mechanically lower the score. Treat Flesch-Kincaid as a directional signal, not a hard pass/fail.

Do not simplify technical terms merely to improve a readability score. That exception is stated in the official guide.

## No-AI-slop pass

**No-AI-slop rule**, applied only after structure, accuracy, and Official VIVERSE style are correct.

Remove or rewrite:

- binary "not X, but Y" constructions used for drama
- negative listing ("Not a X. Not a Y. A Z.")
- throat-clearing openers
- faux-insight framing
- dramatic colon reveals
- superficial "-ing" analysis
- importance puffery
- interpretive metadiscourse ("The key point is," "As you can see," "This distinction matters")
- vague or weasel attribution ("experts agree," "studies show")
- fake-strong verbs that hide the action ("serves as a centralized hub")
- unnecessary synonym cycling
- dramatic fragments
- robotic rhythm: identical paragraph shapes and stacked punchy fragments
- rhetorical question-answer setups
- fake-profound endings
- repetitive summary endings
- decorative formatting
- em-dash overuse
- often-empty adverbs and phrases when they add nothing: "it's worth noting," "let's dive in," "in order to," "at its core"
- banned filler words unless they are a required product term or a quoted example: delve, foster, leverage, utilize, facilitate, empower, streamline, robust, cutting-edge, paradigm shift, game changer, tapestry, realm, beacon, multifaceted, meticulous, intricate, paramount, transformative, elevate, embark, supercharge, harness, ever-evolving
- generic filler that fails the portability test: if the sentence could move unchanged to another product, cut or replace it

Prefer:

- the concrete point
- direct verbs
- specific facts
- stable terminology
- minimum effective edits

Do not copy another writer's personal voice. For VIVERSE documentation, Official VIVERSE style and technical clarity come first.

### Do not apply these no-ai-slop heuristics

They conflict with Official VIVERSE technical documentation:

- Do not rewrite "The SDK returns…" or similar inanimate-subject sentences. The official guide uses that pattern as a good example.
- Do not collapse required numbered steps, tables, or lists into prose.
- Do not remove the required one or two sentences after an H2.
- Do not replace repeated official terms such as "VIVERSE Unity SDK" with synonyms.
- Do not preserve humor, profanity, fragments, or a personal authorial voice.

## Information architecture audit

**Proposed automation rule.**

When reviewing a section or the full site, test navigation from realistic starting points.

Use developer journeys such as:

- Unity developer wants to add VIVERSE multiplayer
- Unity developer wants to add authentication
- web developer wants to publish a Three.js experience
- creator wants to publish an existing WebGL build
- developer wants to add leaderboards
- developer wants to optimize memory/performance
- developer wants to find a sample project

For each journey, record:

- starting page
- target page, or that the target page is missing
- clicks/decisions required
- labels the reader must interpret
- whether internal VIVERSE terminology is required
- missing cross-links
- dead ends
- ambiguous category choices
- recommended fix

Flag category names that describe internal organization rather than recognizable reader goals or technologies. "Developer Tools" is an example of a label that may be opaque because the documentation set itself is a developer tool.

Do not invent a Unity, engine, or feature page to complete a journey. Report the gap.

Known live-site shape to verify rather than assume:

- The public homepage is Introduction to Creator Tools, not an SDK or engine router.
- Unity publishing, Developer Tools SDKs, the VIVERSE Unity SDK artifacts page, and a hidden Creator Tools Unity tree are separate paths.
- Matchmaking and Networking documentation is JavaScript/PlayCanvas-oriented.

## Change report

**Proposed automation rule.**

For Improve mode, include:

### What changed

- structural changes
- clarity/style changes
- discoverability/SEO changes
- accessibility changes
- links/cross-links changed

### Technical changes

State one of:

- None. Technical meaning preserved.
- Potential technical change: [explain and require review]

### Human review required

List anything that needs confirmation from engineering, product, legal, SEO, or docs owners. Include every new or changed external link.

## Stop conditions

**Proposed automation rule.**

Do not auto-rewrite when:

- the source appears technically contradictory
- a required technical fact is missing
- multiple structures would imply different product strategy
- a navigation change affects large portions of the docs and requires owner approval
- an external link or claim requires verification
- code behavior is uncertain
- a reader journey needs a page that does not exist

In those cases, return a recommendation and the exact decision needed from a human.
