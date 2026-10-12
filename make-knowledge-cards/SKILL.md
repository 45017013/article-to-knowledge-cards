---
name: make-knowledge-cards
description: Turn one article into 5-8 knowledge cards, each covering a single knowledge point with a title, a core knowledge statement, a plain-language explanation, and an example or self-test question. Use when the user asks to make knowledge cards, study cards, review cards, summary cards, or non-Anki flashcards out of an article, blog post, product or API documentation, textbook passage, meeting notes, or a local Markdown/TXT file, or asks to extract only the most important knowledge points, remove duplicated content, or condense a long text into something memorizable. Handles pasted text and local .md/.markdown/.txt files only, and does not fetch web pages, read PDFs, export Anki decks, or render a graphical interface.
---

# Make Knowledge Cards

## Output contract

One source in, 5-8 Markdown knowledge cards out. Each card covers exactly one knowledge point and has four fields: Title, Core knowledge, Explanation, Example or self-test.

Write everything in the same language as the source, including the document title line, the source line, the card headings, and the field labels.

## Rules in priority order

When two rules seem to conflict, the earlier one wins:

1. **One knowledge point per card.**
2. **Fidelity**: invent nothing; preserve the source's terms, scope, and hedging.
3. **Cover the source's most important points.**
4. **Land in the 5-8 card range.**

The count range never justifies breaking rule 1 or 2. Drop the least important candidates instead of packing several points into one card, and never split one point into two cards in order to reach 5.

## Inputs

Accept:

- Article text pasted into the conversation.
- A local `.md`, `.markdown`, or `.txt` file path (read it with the file-reading tool).

Redirect anything else, and never guess at content you cannot read:

- URL or web page: "I can't fetch web pages. Paste the article text, or save it locally as .md/.txt, and I'll card it."
- PDF: "PDF isn't supported yet. Convert it to text or Markdown first."
- Anki deck or a card UI: "Anki export and a graphical interface aren't supported. I'll produce Markdown cards you can paste anywhere."

## Workflow

1. **Load the whole source.** Read every section before writing anything. Do not sample from a long file; read it in chunks if needed.
2. **Inventory the knowledge.** Walk the source in order and write one line per candidate knowledge point, tagged with where it came from (heading or paragraph). Later steps may only select from this inventory, so nothing can be introduced that the source does not contain.
3. **Filter with the importance test.** Keep a candidate that does at least one of these:
   - states a definition, category, or named concept the source is built on, including the prior belief or theory the source argues against
   - explains a mechanism, cause, or causal chain
   - gives a rule, criterion, or decision the reader can apply
   - reports a non-obvious fact, number, or finding that changes what the reader would otherwise assume

   Drop candidates that only decorate: scene-setting, anecdotes with no takeaway, opinions the source tosses in without arguing for them, promotion of a product or course, marketing or support asks such as "contact us", changelogs, navigation or meta text, generic reassurance that nothing else changes, and restatements of a point already kept.

   A technical recommendation the reader is expected to act on is a rule to keep, not a marketing call to action. An exception that names who or what is exempt is a fact to keep.

   When the source's purpose is to argue a position, its thesis and the reasons it gives for it are knowledge points, so card them with their attribution and hedging intact.
4. **Merge duplicates.** Treat two candidates as one point when they make the same claim about the same thing, even in different words. To decide, write both core knowledge sentences: if they assert the same thing, merge them; if one states a claim and the other states what follows from it, keep both as separate cards. Keep the strongest, most concrete wording and fold any extra detail from the weaker one into the merged card. A merged card takes the position of the earliest passage it draws from.
5. **Decide the count** using the count policy below.
6. **Write the cards** in source order, following the card rules.
7. **Run the self-check**, then answer. Do not show the inventory or the rejected candidates unless the user asks for them.

## Card rules

### One knowledge point per card

- A sentence that states two distinct points becomes two cards. Splitting is always correct when the points are genuinely distinct.
- A step, procedure, checklist, or closed set of options that the source presents as one item is one point: the source's own numbered step counts as one item, even when it contains several commands. Independent rules that merely share a list stay separate points; what makes a set one point is that its members only work together, either as one procedure or as alternatives you choose between. Do not give each step or each command its own card, and do not bundle such a set with an unrelated rule. The article as a whole is never one card.
- If deleting half of a card's explanation still leaves a coherent card, the card was holding two points: split it or drop the weaker one.
- A card whose core knowledge sentence joins two independent claims with "and" usually needs splitting too. Do not split when the joined clauses are a single claim together with its condition or consequence.

### The four fields

| Field | Requirement |
|---|---|
| Title | Names the single point in a few words. |
| Core knowledge | One declarative sentence. This is the sentence the reader should remember. |
| Explanation | 2-5 sentences: what it means, why or how it works, and what it implies, using only the source's content. Fewer is fine when the source is thin; never pad to reach the minimum, and never spend a sentence restating the core knowledge. |
| Example or self-test | A concrete instance of this point that the source itself gives, such as a command it prints, a named case it describes, or a scenario it walks through, used only where the source actually applies the point to that case. A datum the source reports as part of the point is not an instance. Do not restate the card's own point as its example. If the source gives no instance, write one question answerable from the source, plus a one-line answer key. Never invent an example to fill the field. |

### Fidelity: never invent

- Every sentence must be traceable to a passage of the source. If you cannot point to the passage, cut the sentence.
- Add no outside facts, definitions, history, examples, numbers, dates, or names, even when they are correct and well known.
- Keep the source's own terms. When the source uses a term it never defines, use the term as-is rather than supplying a definition.
- Preserve hedging. "Some studies suggest" must not become "studies prove"; "usually" must not become "always".
- State no number the source does not state, and do not derive new numbers from it unless the source performs that calculation.
- Do not generalize the source's illustrative values into variables, formulas, or placeholders. Report them as the source presents them, and keep the source's own example labeled as its example.
- Do not add observations that connect two facts the source states separately, unless the source itself makes that connection.
- Do not paper over gaps. If a card needs something the source never explains, either drop the card or say, in the source language, that the source does not state it, placing the note at the end of the Explanation field. Add that note only when a reader needs the missing detail to use the card.

## Count policy

- Target 5-8 cards. This is a range, not a quota.
- Output fewer than 5 when the source only supports fewer. State the count and the reason on one line under the header, in the source language, for example: "This article contains only 3 independent knowledge points, so it gets 3 cards."
- Never reach 5 by splitting one point into two, by restating a card, by padding with background the source treats as given, or by promoting a minor mention into its own card.
- More than 8 candidates means the inventory was too fine-grained. Do not bundle distinct points into one card to fit the range. Instead, work through the candidates in this order, repeating until at most 8 remain:
  1. restatements or near-duplicates of a card you are keeping: merge them into that card
  2. background the reader can be assumed to know, or that the source itself treats as given: discard it
  3. supporting detail that only makes another card's point concrete: fold it into that card's explanation rather than giving it its own card
  4. the least consequential of what is left: discard it, keeping warnings, failure modes, and commands that undo damage in preference to verification steps and nice-to-know facts

  Steps 1 and 3 lose no content because the detail lives on in the card it belongs to; steps 2 and 4 do lose content, which is what the left-out line below reports.
- If points a reader might have expected are dropped this way, add one line under the header, in the source language, saying how many were left out, without listing them.
- Exactly 8 candidates stays at 8 cards.
- A genuinely short source may yield 1-2 cards, and that is a correct answer.
- If the user requests a specific number, honor it only when the source supports it; otherwise deliver the honest count and say why.

## Output format

Use the label set for the source language from [references/label-sets.md](./references/label-sets.md). Translate the whole shell, not just the four field labels. For a language that file does not cover, translate the English shell below directly, and keep the labels identical in every card of the set.

For the source line, use the file path exactly as the user gave it, or a short note that the text was pasted.

The shape (English labels shown):

```markdown
# Knowledge cards: <source title>

Source: <pasted article | file path> | Cards: <N>

---

## Card 1: <title>
- **Core knowledge**: <one sentence>
- **Explanation**: <2-5 sentences>
- **Example or self-test**: <source example, or question plus answer key>

---

## Card 2: <title>
...
```

Keep the separator lines between cards so the set is scannable, and keep the field order and labels identical in every card. Reuse the source's own title when it has one; otherwise derive the title from the source's own wording. If that title already contains a colon or a label of its own, keep it verbatim rather than editing it to read more smoothly.

## Self-check before answering

1. **Priority order**: no card bundles two points, and no single point was split to reach 5.
2. **Count**: 5-8 cards, or fewer with a stated reason.
3. **One point each**: you can name each card's point in a single sentence.
4. **No duplication**: no card restates another card's point.
5. **Fidelity**: every sentence is traceable; no outside facts; no invented numbers or examples; no generalized illustrative values; hedging and original terms preserved.
6. **Fields**: all four fields present in every card, and each example or self-test question is answerable from the source alone.
7. **Explanations**: no sentence merely restates the core knowledge, and none is padding.
8. **Language**: the whole shell, including labels, is in the source language and identical across cards.
9. **Noise removed**: no promotion, no changelog or meta text, no restatement padding.

## Worked example

Source excerpt:

> By default, entries in the response cache expire after 60 seconds. Passing a `ttl` value when writing an entry overrides the default for that entry.

Card:

```markdown
## Card 1: Cache entries expire after 60 seconds by default
- **Core knowledge**: A response cache entry lives for 60 seconds unless the write sets its own `ttl`.
- **Explanation**: 60 seconds is the default lifetime of a cached response entry. A write that passes a `ttl` value replaces that default for the entry being written.
- **Example or self-test**: Self-test: how long does an entry survive when the write does not pass a `ttl`? Answer: 60 seconds.
```

## Edge cases

- **Several articles pasted at once**: ask whether to produce one card set per article or a single set for the combined text, then proceed.
- **Not really an article** (changelog, release notes, price list, spec table): card the durable rules and facts and skip the rest. If the non-knowledge content dominates, deliver the cards and state on one line directly under the header what you skipped.
- **Truncated or unreadable source**: state exactly what could not be read and card only what you could. Never fill the gap with outside knowledge.
- **Mixed languages**: follow the language of the surrounding prose for the card text, and keep quoted terms in their original form.
