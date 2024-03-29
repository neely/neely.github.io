---
layout: post
title:  The magic of Bluesky
categories: [social media, Twitter, SciTwitter, ProteomicSky, TeamMassSpec]
excerpt: I think today may be the day for Bluesky to finally be the replacement for SciTwitter. It has the right pieces and may actually have people.
---

Last fall I wrote about what was going on with the initial Twitter exodus, and tried to present a [primer on Mastodon](https://neely.github.io/MarchToMastodon/). Since then many did in fact leave Twitter for good and are only on Mastodon. For whatever reason, these are some of my favorite computational folks ([Ron Beavis](https://x.com/neely615/status/1705028254048866547?s=20), Phil Wilmarth, David Tabb, etc.). Bluesky has slowly been building in invite-only mode, but for me has never been more than some of my favorite authors (Neil Gaiman and John Scalzi) posts... but this past week I started to see a lot of the genomics comp bio folks posting on Bluesky, and then even more yesterday and today. Maybe it was that [Elon just said Twitter might charge everyone](https://techcrunch.com/2023/09/18/elon-musk-says-x-will-charge-users-a-small-monthly-payment-to-use-its-service/), but something seemed to happen. As Carl Bergstrom [wrote last fall](https://web.archive.org/web/20230714014429/https://www.nytimes.com/2022/11/19/opinion/pandemic-twitter.html) "Until now, scientists were locked into staying on Twitter by the “network externality” problem — the value of Twitter comes from the community there, and unless scientists could move en masse to a new home, there was no incentive for any individual or small group to switch. Mr. Musk may have broken that particular dam."

&nbsp;  

So here is my effort to present what I know and like about Bluesky, and what are their future plans (not exhaustive):
- [Algorithm Choice with Custom Feeds](https://blueskyweb.xyz/blog/7-27-2023-custom-feeds) and [another](https://www.engadget.com/bluesky-now-lets-you-choose-your-own-algorithm-183824105.html)
- [BlueSky for Scientists](https://docs.google.com/document/d/1aPddaH-d7N53jZm1S3vUrfjVVy5nykPQz6qd7QpPCQE/edit#heading=h.58mkl21vxs3m) - this has a tool to scrape your twitter follows and get them into Bluesky
- [Vox article on Bluesky](https://www.vox.com/technology/2023/4/29/23702979/bluesky-twitter-elon-musk-jack-dorsey-chrissy-teigen-aoc-dril-decentralized)
- [BlueSky's Business Plan](https://blueskyweb.xyz/blog/7-05-2023-business-plan)

&nbsp;  

For me, it clicked today when I made a custom proteomics feed using the skyfeed.app (instructions [here](https://bsky.app/profile/markrubin.bsky.social/post/3k7bn2bbkmr2s)) and realized that by pinning it I can have multiple feeds to click through at the top of the webUI or the app. Why is this a big deal for me? I thought (and still think) Mastodon is awesome and has all the right things going on... except being able to browse your follows when time is limited ([a thread by me about this](https://x.com/neely615/status/1683599480229068801?s=20)). Let's say all 2-3k team mass spec folks come over to Mastodon, and here I am browsing a chronologically sorted timeline... I am gonng miss tons of posts unless I have hours to scroll and will be biased towards those active when I am active (like a shared time zone or region). Instead, with Bluesky I can make up whatever feed I want that can gather certain users or tags (even emjoi), mixing in random posts from all over, picking the last 3o minutes or day or 3 days or 7 days, or I can sort it all based on likes, chronologically, random, or Hackter News Rank (whatever that means). Specifically, it means I can (and did) make a ProteomicSky feed and now I can see others *are* actually here and I can quickly browse it. Obviously I need to fine tune this custom feed (maybe make a "hot" ProteomicSky feed instead), but that's the thing, I can make whatever I want or anyone else can. This is not a choice in Mastodon (short of maybe the app Mammoth starting to add it).

&nbsp;  

Feed Update: [ProteomicSky](https://bsky.app/profile/did:plc:gl7bvz3uo4ym2fnmvgkjzeb3/feed/aaakx5my5bkl2) is chronological and [ProteomicSky-Hot](https://bsky.app/profile/did:plc:gl7bvz3uo4ym2fnmvgkjzeb3/feed/aaalgayjgixrs) is last 3 days sorted by likes.

&nbsp;

Okay, but going back to my post about Mastodon last year, what *was* the function and functionality of sciTwitter and what are we getting right/wrong now with Bluesky.
- Posts are technically called skeets (akin to toots or tweets), but calling them posts seems acceptable
- There are "Repost" and "Quote Post" options (quote posting is still a contentious topic on Mastodon)
- Has an app and webUI, and they are pretty good (specifically controlling feeds and moderation are really cool)
- Posts are not public (yet), so public SciComm sharing doesn't work (yet)
- AltMetric isn't here (but it isn't anywhere but Twitter, yet)
- Many of my nonScience follows (think cycling, weather, sports) are on Twitter and/or Threads, but not here (this is still a big bummer to me about [the whole twitter exodus](https://www.theverge.com/2023/7/3/23782607/social-web-public-apps-end-reddit-twitter-mastodon))
- No DMs ([feature is coming](https://docs.google.com/document/d/1aPddaH-d7N53jZm1S3vUrfjVVy5nykPQz6qd7QpPCQE/edit#heading=h.58mkl21vxs3m))
- No GIFs ([feature is coming](https://docs.google.com/document/d/1aPddaH-d7N53jZm1S3vUrfjVVy5nykPQz6qd7QpPCQE/edit#heading=h.58mkl21vxs3m))
- No post editing (most Mastodon clients have a pretty nifty "edit" feature, which is essentially is a delete a re-post, but it works well)
- No polls
- No Spaces thingy, but POTRH has some plans if needed (Discord I think)
- No pinned post (not a deal breaker, but I liked this feature)
- No verification, unless you count using custom domain handle (for comparison, some Mastodon servers let you verify yourself with your domain). I don't think this is essential, but is a point to mention.
- Hashtags aren't really what they are on other sites (autosuggestable clickable aggregators), but this is [by design](https://github.com/bluesky-social/proposals/tree/main/0003-hashtags), and custom feeds are the answer instead
- Lingering question of will I be able to access everything via an "AllMyTweets" type service?
- I *can* view all my follows without worrying about servers or truncated conversation threads (looking at you Mastodon)
- Custom feeds are becoming more and more the main selling point, imo (make your feed and UI exactly what *you* want). Having custom feeds to flip through on the app or webUI is a really fun hack I didn't think I would like as much as I do ([here](https://docs.google.com/spreadsheets/d/1tJw1r_Dif9AN6lVNNaZ-nwDdbM7tPemobo5_S2aEl_U/edit) are more science ones)


&nbsp;
&nbsp;
I wrote a follow up to this after a couple weeks using it, with some final thoughts. Check it out [here](https://neely.github.io/BlueSkyWeek2/).


&nbsp;  
&nbsp;  
&nbsp;  
