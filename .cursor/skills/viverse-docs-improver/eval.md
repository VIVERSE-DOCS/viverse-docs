# VIVERSE docs improver evaluation

One checklist, two uses. Do not split this file.

**Audit:** Score **Audit-applicable** sections only. Use pass / fail / N/A. A fail is a finding or recommendation. Do not edit the page. Do not assign numeric scores. Do not dump every checkbox into the report. Report all failures, useful passes, and N/A that would change a decision. Group recommendations by page with impact and effort.

**Improve:** After approved edits, run the **entire** list. Fix applicable failures before returning the draft unless the failure requires a human decision.

Applicability:

- **Audit + Improve:** §§2–10
- **Improve only (N/A in Audit):** §1, §11, and the Improve-only items in §12
- **§12 Audit-visible:** only what the source already shows (see that section). Do not treat “stepper vs numbered list” as an Audit defect.

Source labels:

- **Official** — VIVERSE Technical Documentation Style Guide
- **no-ai-slop** — Peter Yang skill, only the heuristics this project adopted
- **Proposed** — automation, accuracy, and review workflow

## 1. Technical preservation

**Proposed.** **Improve only** (N/A in Audit).

- [ ] No API names changed without explicit reason.
- [ ] No method/event/parameter names changed.
- [ ] No code semantics changed.
- [ ] No version numbers changed.
- [ ] No supported-platform claims were invented.
- [ ] No prerequisites were invented.
- [ ] No warnings or limitations were removed.
- [ ] No product terminology was replaced by stylistic synonyms.
- [ ] No missing page or API was invented to complete a journey.
- [ ] Any suspected contradiction is flagged instead of guessed.

## 2. Reader goal

**Official** for a clear opening. **Proposed** for navigation and next-step checks. **Audit + Improve.**

- [ ] The page has one clear primary audience.
- [ ] The reader's goal or the concept being explained is clear in the opening paragraph.
- [ ] The first useful action or explanation appears early.
- [ ] Internal VIVERSE organization is not required knowledge unless unavoidable.
- [ ] The next step is clear when another page is required.
- [ ] If the needed next page does not exist, the gap is reported instead of filled with invented content.

## 3. Document type

**Official.** **Audit + Improve.**

- [ ] The page is classified as Informative, Setup, Tutorial, or Examples.
- [ ] The page structure matches that type.
- [ ] The title matches the type formulas, or a named overview title is used only on an Informative page.
- [ ] Informative overview titles such as "VIVERSE SDK Overview" are allowed when the page is conceptual. They are not treated as weak.
- [ ] Informative pages are not padded with unnecessary numbered procedures.
- [ ] Setup pages use numbered steps and end when the thing works.
- [ ] Tutorial pages use numbered steps and teach a skill rather than only completing setup.
- [ ] Tutorial titles use "How to…" or walkthrough framing, not Setup framing.
- [ ] Example pages use numbered steps and provide a copyable pattern.

## 4. Heading structure

**Official.** **Audit + Improve.**

- [ ] One H1 comes from the page title.
- [ ] No manual duplicate H1 in the body.
- [ ] H2s define major sections.
- [ ] H3s are used only for real subsections or step-level grouping.
- [ ] No H4+.
- [ ] Headings use sentence case.
- [ ] H2s have 1-2 context sentences before lists, steps, or tables.
- [ ] Headings describe reader intent, outcome, or a named concept. Bare "Overview" without a feature is avoided; a named overview on an Informative page is acceptable.

## 5. Voice and language

**Official.** **Audit + Improve.**

- [ ] Active voice is the default; passive voice is used only when the actor is unknown or irrelevant.
- [ ] Inanimate-subject sentences such as "The SDK returns a token" are allowed.
- [ ] Second person is used where direct address helps.
- [ ] Present tense is the default.
- [ ] Plain language is used when technically accurate. Prefer "start" and "use" over "initiate" and "utilize".
- [ ] American English is used.
- [ ] Oxford comma is used.
- [ ] Marketing words such as "innovative," "robust," and "seamless" are removed unless they are a required product name.
- [ ] One term is used consistently for each concept. "World," "space," and "environment" are not rotated for the same thing.

## 6. Code and UI

**Official**, except the last item. **Audit + Improve**, except the last item (**Improve only**).

- [ ] Code fences include language identifiers.
- [ ] Filenames, commands, parameters, values, and code identifiers use inline code (the official "single apostrophe" rule, implemented as backticks).
- [ ] UI elements are bold when readers must interact with them.
- [ ] Shortcuts use bold or code style and stay platform-neutral when relevant. Key names are Ctrl, not CTRL.
- [ ] Screenshots/diagrams have useful alt text. Alt text is not "image of..." or "Screenshot of dashboard."
- [ ] Documentation images are web-friendly and no more than 1080 px on the longest side.
- [ ] Code was not changed merely for prose consistency. (**Proposed.** **Improve only.**)

## 7. Links

**Official.** **Audit + Improve.** The last item is **Improve only** when no link was added or changed.

- [ ] No "click here" or "read more."
- [ ] Link text describes the destination/action.
- [ ] Stable/canonical/versioned URLs are preferred.
- [ ] Links do not deep-link volatile UI states.
- [ ] Cross-links help the current task.
- [ ] Links are not excessive.
- [ ] Every new or changed external link is flagged for mandatory editor approval before publication. (**Improve only** unless the audit is proposing a new external link.)

## 8. SEO and discoverability

**Official**, except the last two items. **Audit + Improve.**

- [ ] Page title contains the likely target phrase and follows the document-type title formula.
- [ ] Opening description includes the topic naturally.
- [ ] Page description/meta description intentionally includes the primary keyword.
- [ ] Page description is 200 characters or fewer after folding line breaks to spaces, and it ends on a complete sentence. (**Proposed**)
- [ ] H2s use likely reader/search language where appropriate.
- [ ] Duplicate explanations are consolidated or cross-linked.
- [ ] Docs not reviewed in 6+ months are flagged for audit.
- [ ] Broken internal links are checked, especially after page renames.
- [ ] Search data is considered when available to align headings with reader terminology.
- [ ] Important pages are not buried behind unclear category names. (**Proposed**)
- [ ] Navigation supports likely developer journeys, or the gap is reported. (**Proposed**)

## 9. Accessibility

**Official.** **Audit + Improve.**

- [ ] Heading levels are sequential. H1 is not skipped to H3.
- [ ] Tables have header rows.
- [ ] Tables are not used only for layout.
- [ ] Alt text describes what the reader needs to understand.
- [ ] Color is not the only carrier of meaning.
- [ ] Link text works without surrounding context.
- [ ] Flesch-Kincaid Grade 8-10 is treated as a directional target. Terms such as WebGL, SDK, and iframe are expected exceptions.
- [ ] Reading level is broadly accessible without sacrificing technical accuracy.

## 10. AI-slop check

**no-ai-slop**, limited to the adopted heuristics. **Audit + Improve.** The “were not collapsed” item is **Improve only**.

- [ ] No generic throat-clearing.
- [ ] No dramatic "not X, but Y" framing unless technically necessary.
- [ ] No negative listing used for drama.
- [ ] No faux-insight framing.
- [ ] No dramatic colon reveals.
- [ ] No importance puffery.
- [ ] No interpretive metadiscourse.
- [ ] No vague attribution.
- [ ] No fake-strong verbs that hide the action.
- [ ] No synonym cycling. Official terms may repeat.
- [ ] No dramatic fragments, robotic rhythm, or fake-profound ending.
- [ ] No redundant conclusion that merely repeats the page.
- [ ] No decorative formatting that competes with the content.
- [ ] No banned filler words unless they are a required product term or a quoted example.
- [ ] Em dashes are rare.
- [ ] Portable generic filler was cut or made specific.
- [ ] Required numbered steps, tables, lists, and H2 intro sentences were not collapsed to satisfy slop heuristics. (**Improve only.**)
- [ ] Technical terms remain precise even if repetition is necessary.

## 11. Output quality

**Proposed.** **Improve only** (N/A in Audit).

- [ ] Changes are the minimum effective edits.
- [ ] The page is easier to scan.
- [ ] The page is easier to act on, or a conceptual page is easier to understand.
- [ ] The change report distinguishes editorial vs technical changes.
- [ ] Anything uncertain is explicitly listed under Human review required.
- [ ] New or changed external links are listed for editor approval.

## 12. GitBook execution

**Proposed.** Load write-docs for syntax in Improve mode. These checks are policy plus “did you load write-docs,” not a second syntax manual.

**Audit-visible** (score only what the source already shows):

- [ ] No decorative hints. Official callouts that should be hints are flagged; do not rewrite in Audit.
- [ ] Setup / Tutorial / Examples have sequential numbered steps.
- [ ] Frontmatter `description` is present and YAML-safe.
- [ ] Custom blocks are closed; no invented GitBook tags.
- [ ] Internal links stay same-space relative paths.

Do not treat “stepper vs numbered list” as an Audit defect.

**Improve only** (N/A in Audit):

- [ ] write-docs was read before Improve-mode file edits.
- [ ] Official callouts became hints, and no decorative hints were added.
- [ ] A stepper was used only when the page/space convention and write-docs made it appropriate.
- [ ] A valid numbered list was not rewritten into a stepper by default.
- [ ] Engine alternatives used tabs when the page already uses that pattern.
- [ ] `SUMMARY.md` stays in sync if files were added, moved, or renamed.
