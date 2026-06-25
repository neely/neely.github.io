---
layout: post
title: "Building a Mobile-First Post Composer for Jekyll"
date: 2026-06-18
tags: [jekyll, github, tools, claude, web]
excerpt: "I built a single HTML file that lets me write and publish blog posts from my phone, directly to GitHub Pages, with no server required."
---

I've been running this blog on Jekyll and GitHub Pages for a while now. The setup is clean and I like it: no server to maintain, no database, just Markdown files in a repo that build into a site.

The problem is the writing workflow. To publish a post I needed to be at my computer, write the Markdown, commit it, push it. On my phone that's basically impossible without a third-party app, and every CMS option I looked at either had account limits, cost money, or added a layer of complexity I didn't want.

So I built my own.

## The goal

A single HTML file that lives at `neely.github.io/compose.html`. Open it on my phone, write a post, hit publish. No app to install, no account to create, no server involved. Just a browser talking directly to the GitHub API.

If you want the full context on why I like the GitHub-as-backend approach, I try to keep track of it here (link tbd).

## How it works

The whole thing runs in the browser. A GitHub fine-grained Personal Access Token with Contents read/write on the repo is the only credential required: stored in localStorage on your device, never sent anywhere except GitHub's API. One token setup, and then it's just a writing tool.

When you hit publish, the composer makes two or three API calls in sequence: upload any images to `/images/`, upload any attached files to `/shared/`, then commit the post as a `.md` file to `_posts/`. GitHub Pages picks it up, Jekyll builds, and the post is live in about a minute.

No GitHub Action. No build pipeline. No server.

## Features

A few things I'm happy with:

**Front matter handled automatically.** Title, date, layout, tags, and excerpt are form fields. The composer generates the YAML front matter and the correct Jekyll filename (`YYYY-MM-DD-Title.md`) automatically. The live URL (`neely.github.io/Title/`) is previewed before you commit.

**Draft flow.** A toggle switches between committing to `_posts/` (live) and `_drafts/` (hidden). The load picker shows both with colored labels so you can see what's a draft and what's published. Loading a draft flips the toggle automatically. Publishing a draft deletes the draft file from `_drafts/` after the post lands in `_posts/`.

**Autosave.** Every couple of seconds the composer saves your work to localStorage. Close the tab accidentally, come back, tap Restore. The title shows in the banner so you know what you're restoring.

**Images via paste.** Screenshot something on your phone, paste it into the body. The composer resizes it, base64 encodes it, stages it to `/images/post-slug/img1.jpg`, and inserts the Markdown reference at your cursor. On publish it commits the image first, then the post.

**GIF support.** A `+ gif` button in the images section opens a file picker. GIFs bypass the canvas resize step so animation is preserved.

**Handoff docs.** This one is specific to how I work. When I've been building something with Claude and want to share the prompt or methodology, I attach the `.md` file in the composer. It commits to `/shared/post-slug/filename.md` and gives me a copyable Markdown link to drop into the post. Multiple files, each with its own link. Sorry that it defaults the link for these as handoff, but you can change this.

**Edit existing posts.** The load picker fetches both `_posts/` and `_drafts/` from the GitHub API and lists them newest first. Load a post, edit it, publish. If you changed the title the old file is deleted automatically: the composer fetches the old file's SHA before writing the new one, then sends a DELETE after the new commit succeeds.

**Light and dark theme.** Warm parchment in light mode to match the blog's feel. Deep navy in dark mode, which is basically the Claude app palette. Remembers your preference.

## How it was built

This was a collaborative build with Claude over several sessions started while I was in a VERY boring meeting. The approach was conversational: describe what I wanted, get a working version, test it on the actual site, feed back what broke or felt wrong, iterate. 

A few things that came out of that process that I wouldn't have thought of upfront: tracking the source file path so renames clean up after themselves, fetching the old file SHA before writing the new file rather than after (otherwise the delete fails), the draft pill labels in the load picker, the autosave restore banner showing the post title.

The process of testing on the real site caught things no amount of reasoning about it would have. The line break issue in particular went through several failed approaches before landing on the right answer.

## Tradeoffs and known limitations

**Line breaks.** Kramdown, Jekyll's Markdown processor on GitHub Pages, requires a blank line between paragraphs. Single returns join lines in the same paragraph. This is standard Markdown behaviour but it's not how most people think when writing. The composer has a hint and a `¶` button that inserts an `&nbsp;` spacer for extra visual gaps, but you do need to use double returns for paragraph breaks. I've made my peace with it.

**Images don't survive autosave.** The post body is saved to localStorage, including the Markdown image references. But the actual image data (binary) can't be stored there. If you close the tab with staged images, the body restores correctly but you'll need to re-paste the images before publishing. The composer warns you about this on restore.

**PAT is the only security.** The composer is publicly accessible at `neely.github.io/compose.html` but completely useless without the token. Anyone who finds it just sees a form that errors out. For a personal blog this is fine. For anything with multiple contributors you'd want a different approach.

**No media library.** You can paste images and they commit correctly, but there's no way to browse images already in the repo. If you want to reuse an existing image you write the Markdown reference manually.

**GitHub strips trailing whitespace.** We spent longer than I'd like to admit trying to get single returns to produce line breaks using Kramdown's two-trailing-spaces syntax. GitHub silently strips trailing whitespace on commit, so that approach doesn't work. Double returns it is.

## Future opportunities

A few things I'd add if I kept going:

**Comments.** Giscus or Utterances would fit naturally: GitHub Discussions or Issues as the comment backend, one script tag in the post layout. No third party database, comments live in the repo. I am going to add this for sure.

**Media library.** A simple view of `/images/` via the GitHub API so you can pick and reuse existing images without typing paths manually.

**Post management.** The load picker is functional but minimal. A proper list view with delete, publish/unpublish, and maybe word counts would make it more of a full management interface.

**Multi-repo support.** Right now the repo is hardcoded in the config block at the top of the HTML. A settings panel where you configure the repo would make it genuinely portable as a standalone tool.

---

The composer lives at `neely.github.io/compose.html`. The source is in the root of [this repo](https://github.com/neely/neely.github.io). If you're running a Jekyll/GitHub Pages blog and want to use it, the config block at the top of the HTML is the only thing you need to change.

A clean fork-ready version with setup instructions is [here](https://github.com/neely). This is what you want if you want your own GitHub blog with this function. 
