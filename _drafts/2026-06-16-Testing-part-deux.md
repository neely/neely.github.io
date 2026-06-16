---
layout: post
title: "Testing part deux"
date: 2026-06-16
excerpt: "this is our second batch of testing"
---

Token

✅ Paste PAT, verify green, reload and confirm it persists

Write flow

✅ Title, date, excerpt, tags (add, delete, backspace)
✅ Slug preview updates as you type
✅ Live URL preview updates correctly (case preserved, no date in URL)

Tabs

✅ Write → Preview: line breaks render correctly, Markdown formats right
✅ Preview → Raw: front matter YAML looks correct, no categories field
   I don't see any line breaks in the raw code but looks great in write and preview

Autosave

✅ Type something, pause 2 seconds, watch word count line flash "· saved"
   this is adorable but is likely not even something I need, but I like it a lot. Maybe make the "saved" persist, and so it would only vanish between saves.
✅ Close tab, reopen, confirm restore banner appears with title
✅ If body has image refs, confirm warning about re-attaching

Load picker

Opens and shows both _posts and _drafts with correct colored pills
Date and title display cleanly for all entries
Loading a post populates all fields with original date preserved
Loading a draft flips the draft toggle on automatically

Draft flow

Write, toggle draft on, publish → confirm _drafts/ on GitHub
Load it back, change title, toggle off, publish → confirm _posts/ has new file, _drafts/ old file is gone

Images

Paste screenshot in body → appears in image list with correct path
Publish → lands in /images/slug/img1.jpg on GitHub
Image refs survive autosave/restore (with re-attach warning)

GIF

Tap + gif, pick file → stages correctly, ref in body

Handoff

Attach .md file → appears in list with path
Tap copy link → paste and confirm URL is correct
Attach a second file → both appear independently
Publish → both land in /shared/slug/

Publish button

Always visible after publishing — never disappears
Success block shows file path and live URL
New post button resets everything cleanly

Light/dark

Toggle persists on reload
