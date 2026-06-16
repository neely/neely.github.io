---
layout: post
title: "Testing the new compose edit"
date: 2026-06-16
tags: [testing, blog, dev, llm-assisted]
excerpt: "I am making and testing a compose utility for writing to blog"
---

Alright, we have a new compose thing, which is super fun. Here is my testing to-do:

✅ Token — paste PAT, verify it turns green, reload and confirm it persists.
✅ Write flow — title, date, excerpt, tags (add, delete, backspace), check slug preview updates, check live URL preview updates.
✅ Tabs — write, preview (check markdown renders), raw (check front matter is correct YAML).
✅ Autosave — type something, wait 2 seconds, watch amber bar pulse. Close tab, reopen, confirm restore banner appears with your title.
    Amber bar pulse is at very top. May move to character count here
    The restore banner is awesome!
✅ Load picker — open it, confirm both _posts and _drafts show with correct pills. Load an existing post, confirm fields populate.
    This mostly works except Tilt Maze is rendering as its full slug, while others are small dates with just title shown clear.
✅ Draft flow — write something, toggle draft on, publish. Confirm _drafts/ gets the file on GitHub. Load it back, change the title, toggle draft off, publish. Confirm _posts/ gets the new file and _drafts/ loses the old one.
   Actually it is 2026-05-2-tiltMaze.md but when I have edited it now shows todays current date, June 16, 2025 and I can't seem to change it when I do edit.
   I think when you edit a title of something, it just makes a new file but keeps the old file.
   I think the drafts directory went away when I removed the only draft.
✅ Images — paste a screenshot in the body, confirm it appears in the image list with the right path.
    the image doesn't render in preview, but maybe that is fine?

![](/images/2026-06-16-Testing-the-new-compose-edit/img1.jpg)



✅ Publish and confirm the image lands in /images/slug/img1.jpg on GitHub.
✅ GIF — tap + gif, pick a file, confirm it stages. Check the Markdown reference is in the body.

![](/images/2026-06-16-Testing-the-new-compose-edit/gif2.gif)



✅ Handoff — attach a .md file, confirm it appears in the list, tap copy link, paste somewhere and confirm the URL looks right. Publish and confirm it lands in /shared/.
[view handoff](https://neely.github.io/shared/2026-06-16-Testing-the-new-compose-edit/README.md)

✅ Light/dark — toggle, confirm it persists on reload.


On reload I lose the images (so the code is still there to link but I don't think they loaded) but the handoff docs was linked, but all the images and handoff staged areas were blank.

Actually on reload I don't see a publish button. This might be a real issue that there should always be a publish button, not something that changes to "Published check mark"
