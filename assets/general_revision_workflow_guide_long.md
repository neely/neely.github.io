# A practical workflow for revising a manuscript with AI help

This is a general workflow for using an AI assistant to help revise a manuscript after peer review while keeping the authors in control of scientific judgment, tone, and final wording. The core idea is to use the AI for structure, triage, sentence-level drafting, and cross-checking, while reserving all factual, technical, and strategic decisions for the author.

## 1. Start by gathering the full review package

Give the AI the submitted manuscript, the reviewer comments, and any linked or implied materials the reviewers are actually responding to, such as supporting information, code repositories, appendices, or public specification files. If a reviewer comments on wording in a repository document or supplement rather than the main paper, the AI needs that context or it may generate responses that sound plausible but miss the real target.

**Example:** instead of sharing only a one-line summary such as “Reviewer says the methods need work,” provide the full reviewer text and any supporting files they cite.

## 2. Decide the revision scope before editing

Before changing the manuscript, ask the AI to help classify the job as light, moderate, or substantial revision. This forces an early decision about strategy: whether to make minimal framing changes, moderate structural edits, or major new analysis.

This step matters because many reviewer suggestions are good ideas but not all belong in the current revision. The AI can help separate:

- Changes that are necessary to correct overstatement or ambiguity.
- Changes that are optional but useful.
- Changes that are scientifically attractive but beyond the scope of the paper type.
- Changes that require the author to make a judgment call.

**Example prompt:** “Read these reviews and help me decide what counts as minimal versus substantial revision for a Letter. I want to preserve the core claim but avoid overpromising.”

## 3. Turn reviewer comments into an edit plan, not just a summary

Ask the AI to transform each reviewer comment into a concrete action plan. The goal is not only “what the reviewer means,” but also “what exact kinds of changes would satisfy this comment?”

A useful format is:

- Reviewer concern in plain language.
- Proposed response strategy.
- Whether the issue should be handled in the manuscript, the rebuttal, supporting information, an external repository, or future work.
- Whether the AI should draft text or flag the item for author decision.

This is also the stage where the AI should mark **author-decision flags**. These are places where it should not guess.

Typical author-decision flags include:

- Exact technical wording in a specialized domain.
- Whether to accept or reject a reviewer’s conceptual suggestion.
- Whether to change tone or retain a stylistic phrase.
- Whether to add new experiments or push them to future work.
- Whether a repository file should be updated in addition to the paper.

**Example prompt:** “For each reviewer comment, tell me what should change in the manuscript versus the rebuttal, and flag anything that I need to decide myself instead of having you guess.”

## 4. Ask for sentence-level edits you can actually apply

Once the plan is clear, ask the AI for exact text edits tied to real locations in the manuscript. This is where the workflow becomes practical.

Good requests here are very specific:

- “For this paragraph, suggest the smallest wording change that narrows the claim.”
- “Give me replacement text for the limitations paragraph that addresses long sessions, false positives, and future benchmarking.”
- “Draft one sentence to justify the model choice without promising a broader study.”
- “Write a compact table caption and a short lead-in sentence for the main text.”

This works best when you ask for **minimal, local edits** instead of a full rewrite. You can also ask for markup-friendly phrasing, such as:

- text to add after a specific sentence,
- a sentence to replace a specific sentence,
- a short paragraph to insert before or after an existing paragraph,
- or wording that is conservative enough to survive tracked changes.

**Example prompt:** “Do not rewrite the whole section. Tell me exactly what sentence to replace and give me the new sentence.”

## 5. Make the marked-up manuscript yourself, then bring it back for checking

After applying the edits in tracked changes or another marked-up form, give the revised file back to the AI. At this point the task changes from drafting to verification.

Now ask:

- Were the proposed changes actually made?
- Which reviewer-facing edits are clearly present?
- Which ones are missing, partial, or reworded enough that they need review?
- Did any new edits introduce inconsistency with the rebuttal strategy?

This is a crucial quality-control step. It prevents a mismatch between what the rebuttal claims and what the manuscript actually says.

**Example prompt:** “Compare this marked-up manuscript against the revision plan and tell me which promised changes are clearly present and which I missed.”

## 6. Write the rebuttal from the full reviewer comments, not from summaries alone

Once the manuscript edits are mostly in place, build the rebuttal using the reviewers’ original words. Summaries are useful for planning, but the final response should be anchored to what the reviewer actually said.

A strong workflow is:

1. Paste the reviewer’s complete comment.
2. Draft a response that acknowledges the point fairly.
3. State clearly what was changed, what was clarified, what was deferred to future work, and what was intentionally not changed.
4. Map that response to the manuscript edits already made.

This is also where tone matters. A good rebuttal should be:

- specific rather than generic,
- appreciative without being submissive,
- honest about limits,
- and explicit about when a reviewer suggestion is valuable but beyond the scope of the revision.

**Example prompt:** “Use the full reviewer comment, keep my intended tone, acknowledge the strong ideas, but make clear which parts we addressed directly and which remain future work.”

## 7. Build a response-to-edit map

One of the most useful intermediate documents is a reviewer-response draft that includes, under each comment, the **specific manuscript track changes related to that comment**. This helps in two directions:

- It lets you write stronger rebuttal language because you can point to real edits.
- It lets you audit whether each promised change was actually made.

A helpful structure is:

- Reviewer comment.
- Proposed response.
- Specific manuscript track changes related to this comment.
- Author follow-up still needed.

That last line is especially important. It keeps unresolved items visible until the end rather than letting them disappear into a draft.

## 8. Keep a separate list of author-decision items

Not every issue should be solved automatically. Some should remain open until the author makes an intentional decision.

Examples include:

- a specialized technical correction that should not be improvised,
- whether to retain a stylistic flourish,
- whether to add a table or figure,
- whether to revise a repository document outside the manuscript,
- and whether a reviewer request should be accepted now or left to future work.

Treat these as a checklist. The AI can flag them, but the author should close them consciously.

**Example label:** “Author follow-up still needed.”

## 9. Do a final consistency pass across all artifacts

Before submission, ask the AI to check the whole package together:

- the marked-up manuscript,
- the rebuttal letter,
- any supporting-information edits,
- and any external files or repositories that were part of the response.

The goal is to confirm three things:

1. Every major reviewer concern is addressed somewhere.
2. The rebuttal does not promise changes that are absent from the manuscript.
3. The manuscript does not introduce new claims that the rebuttal fails to explain.

This is the point where you can also ask for a short punch list of anything still unresolved.

**Example prompt:** “Look across the marked manuscript and reviewer responses and tell me whether we addressed everything we said we addressed, and what still needs author attention.”

## 10. Keep the author in charge of the science

The most important principle is that AI should help with organization, drafting, comparison, and clarity, but should not invent scientific decisions. Use it to make the work more efficient, not to outsource judgment.

A good rule is:

- Let the AI draft language.
- Let the AI compare documents.
- Let the AI flag gaps and inconsistencies.
- But require the author to approve technical claims, strategic concessions, and anything the paper will ultimately defend.

That approach makes the process faster without giving up scientific control.
