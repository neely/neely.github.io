---
layout: post
title: "Playing with my nameplate"
date: 2026-06-24
tags: [llm, website, css, geocities]
excerpt: "I needed to put chatGPT through its paces so decided to update the oldest website I have"
---

I needed to put chatGPT through its paces a few weeks ago, so decided to update the oldest website I have. That's right, the one that is my quasi CV that has been a dark theme since I started it, [benjaminneely.com](https://benjaminneely.com). I still can't remember where I got the template from, but in my memory it was on Lifehacker, described as a nameplate so that you could control what google thought of you. Since then I have kept up with it by updating jobs, and papers, etc. (in a text editor with HTML, which also seems pretty on brand for an academic). I could have sworn I have had this html website forever, like grad school c2008, but I don't actually know.

&nbsp;

Anyways, it has been this nice dark theme, two column design, kind of optimized for a phone but not really. But I haven't been using chatGPT for anything and wondered how easily we could make some themes and a switcher. I already had a light theme built-in that I never used, so that was easy. But then I wanted a super retro color scheme, which it did quite well (again, all within css).

&nbsp;

Well then Lindsay Pino of course [offers a ridiculous suggestion](https://bsky.app/profile/lindsaykpino.com/post/3mnus7ujt3s2d), which btw I didn't even know you could do.

![](/images/2026-06-24-Playing-with-my-nameplate/img1.jpg)

&nbsp;

From ChatGPT to summarize the somewhat clever way to do this via CSS:
> We made the site flip into a ridiculous Geocities-style version without changing the actual HTML content. The same resume/CV page stays in place, but the theme button changes a data-theme value on the page, and themes.css uses that to swap in a totally different look: starry background, Comic Sans, neon colors, rainbow text, fake construction signs, and all the good/bad late-90s web energy.
>
> For the extra nonsense, geocities.js mounts temporary Geocities-only decorations when that theme is active and removes them when you switch away. That means the marquee, visitor counter, webring, “under construction” bits, and spinning protein all appear only in chaos mode.
>
> Then we made it worse in the best way: the cursor becomes a tiny shooting star PNG, and JavaScript adds a sparkly trail while you move around the page. So it is still the same clean HTML underneath, but with a CSS/JS costume change into full “my first homepage in 1997” mode.

&nbsp;

So yeah, you can switch the theme and it will remember next time you arrive, which might startle your eyes.