---
layout: post
title: "Playing with my nameplate"
date: 2026-06-24
tags: [llm, website, css, geocities]
excerpt: "I needed to put chatGPT through its paces so decided to update the oldest website I have"
---

I needed to put chatGPT through its paces a few weeks ago, so decided to update the oldest website I have. That's right, the one that is my quasi CV that has been a dark theme since I started it, [benjaminneely.com](https://benjaminneely.com). I still can't remember where I got the template from, but in my memory it was on Lifehacker, described as a nameplate so that you could control what google thought of you. Since then I have kept up with it by updating jobs, and papers, etc. (in a text editor with HTML, which also seems pretty on brand for an academic). I could have sworn I have had this html website forever, like grad school c2008, but I don't actually know.

&nbsp;

Edit: Found [this epic 2010 version](https://web.archive.org/web/20101010023354/http://www.benjaminneely.com/), I guess right after finishing Ph.D. Later in 2013 I shifted to the two column dark theme. So here is my best reconstruction, which annoys me that I can't find the original attribution: In the early 2010s, I built a simple personal landing page or “nameplate” style homepage inspired by [Lifehacker](https://lifehacker.com/)–era advice, especially the kind of practical web guidance [Adam Dachis](https://lifehacker.com/author/adam-dachis) wrote about. The idea was to present a clean, professional front door for myself online, something closer to a living resume than a blog, like the advice in [How to Make Your Personal or Professional Landing Page](https://lifehacker.com/how-to-make-your-personal-or-professional-landing-page-5886755) and [How to Clean Up Your Online Presence and Make a Great First Impression](https://lifehacker.com/how-to-clean-up-your-online-presence-and-make-a-great-f-5963864). Over time, that evolved into a more polished portfolio/CV site, but the original impulse came from that Lifehacker mindset: make a small, useful personal page that can be easily updated and adapted.

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
