---
layout: post
title:  Vibe-revisions
categories: [AI, LLM, revisions]
excerpt: I am realizing I subscribe to the idea that you should choose tasks to off-load to LLMs but in doing so keep tasks that make you happy or ones that you need to keep so your brain does not rot away BUT manuscript revisions are not a task that sparks joy
---

Okay, so I am realizing I subscribe to the idea (which I don't know where I read this) that you should choose tasks to off-load to LLMs, but in doing so keep tasks that make you happy or ones that you need to keep so your brain doesn't rot away. For me, I will totally let an LLM write an internal report, but I don't want to vibe-write an actual paper, because weirdly enough, I actually enjoy it (tell that to my high school and college teachers and they might laugh... I sucked at english). I think writing is a wonderful creative outlet, but also you can get some good thinking done (similar to how I seem to think better if I talk through it). Anyways, there are other things I really don't like and I assume most of us don't like, one is responding to reviewers and getting revisions done.

&nbsp;

So, when I got back a manuscript I decided to reach for an LLM, but not intending to do the whole process. My first prompt was: 

```
I want your take on how manageable these two reviewer comments are. I am uploading the submitted manuscript, reviewer 1 is pasted and reviewer 2 is upload.
```

and the LLM (btw, this was perplexity, which has a really fun "Best" model switcher) responds:

```
Both reviews are very positive on the core idea, and all of the requested changes are scientifically and rhetorically manageable for a revision; what varies is how much new work they imply.

Below I’ll group the comments into “light edit,” “moderate edit,” and “substantial new work,” and flag where you can likely respond with framing/limitations text instead of new experiments.
```

How encouraging was that? I mean, I knew there was a path forward but here was something that was laying it all out, and so instead of me using all of this mental work to come to terms with it, I had very discrete and explicit decisions to be made, and then I could just do it.

&nbsp;

One thing led to another and I realized I had gone through the revisions, crafted responses, generated a tracked change doc, and hadn't lost my sanity or felt any of the normal subconscious anger or sadness or whatever you feel as you work through edits (insert the red pen black pen cartoons about how your manuscript starts and then ends). It felt more like I had a check list I knew would work, and I just did it. You can read below (or here for [a very detailed version](https://neely.github.io/assets/general_revision_workflow_guide_long/) you could likely upload the [md](https://neely.github.io/assets/general_revision_workflow_guide_long.md) to your LLM of choice), but it was not a whole sale "do edits AI", but for some reason, working in this way made it way more manageable, arguably fun again.

&nbsp;

A final note though, is in this case the reviewer comments were great and super helpful, and I wasn't feeling super adversarial, BUT I do think this process made me think this was a great application. Also, even though I don't want to publish a vibe-written manuscript, in this case the LLM had my writing and was instructed to accomplish goals with minimal changes. That alone made me feel like I wasn't losing my voice to AI slop. This wasn't genAI, it was more like revisionAI of a flesh sack's blathering, with a lot of iterations and oversight. It was more like a strength coach telling me not to skip and keep pushing through.


&nbsp;  
&nbsp;  
&nbsp; 
# A practical workflow for revising a manuscript with AI help (short version; [long here](https://neely.github.io/assets/general_revision_workflow_guide_long/) and as [md](https://neely.github.io/assets/general_revision_workflow_guide_long.md))

## 1. Start by gathering the full review package

Give the AI the manuscript, the full reviewer comments, and any materials the reviewers are actually referring to, such as supporting information, appendices, code repositories, or public specification files. If the AI does not see the same source material the reviewer saw, it may produce edits or rebuttal text that sound good but miss the real issue.

## 2. Decide the revision scope before editing

Before changing anything, decide whether the revision should be light, moderate, or substantial. This helps separate essential corrections from attractive but out-of-scope ideas and keeps the manuscript aligned with the paper type.

## 3. Turn reviewer comments into an edit plan, not just a summary

Ask the AI to convert each reviewer comment into a concrete plan: what the concern is, how to respond, where the issue should be handled, and what still needs an author decision. This is also the right time to flag items the AI should not guess.

## 4. Ask for sentence-level edits you can actually apply

Once the plan is clear, ask for exact replacement sentences, insertion text, or short paragraph additions tied to specific parts of the manuscript. This is usually more useful than asking for a full rewrite, especially when you need tracked changes.

## 5. Make the marked-up manuscript yourself, then bring it back for checking

Apply the edits in tracked changes or another marked-up format, then give that revised file back to the AI. At this stage, the AI should compare the marked manuscript against the revision plan and tell you what was clearly changed, what is missing, and what may need review.

## 6. Write the rebuttal from the full reviewer comments, not from summaries alone

Build the response letter from the reviewers’ complete comments, not just short summaries. The response should say what was changed, what was clarified, what was left for future work, and what was intentionally not changed.

## 7. Build a response-to-edit map

For each reviewer comment, connect the proposed response to the specific manuscript edits that support it. This makes the rebuttal stronger and also helps confirm that the manuscript really says what the rebuttal claims it says.

## 8. Keep a separate list of author-decision items

Some issues should remain open until the author makes a deliberate choice, such as a specialized technical correction, a tone decision, a new table, or a repository update. Let the AI flag those items, but do not let it decide them silently.

## 9. Do a final consistency pass across all artifacts

Before submission, check the marked-up manuscript, rebuttal letter, supporting-information edits, and any external files together. The goal is to confirm that every major reviewer concern is addressed and that the rebuttal does not promise changes that are missing from the manuscript.

## 10. Keep the author in charge of the science

Use the AI for structure, drafting, comparison, and clarity, but keep scientific judgment, strategic concessions, and final technical wording under author control. The process works best when the AI improves the workflow without replacing domain expertise.


&nbsp;  
&nbsp;  
&nbsp; 