---
layout: post
title: "VS Code + GitHub Desktop Git Cheat Sheet"
date: 2026-06-22
tags: [github, gitlab, vscode]
excerpt: "I need a cheat sheet for using git in VSCode or desktop app, and this is it."
---

> Yes this is LLM generated, but with my specific questions and goals. There is a short version as well, [here](https://neely.github.io/shared/2026-06-25-VS-Code-GitHub-Desktop-Git-Cheat-Sheet/git-quick-loops.md). This is for me. B-)

&nbsp;

Button-driven. Every step shows **VS Code** and **GitHub Desktop** side by side.

> **GitLab note:** GitHub Desktop only works with GitHub repos. For GitLab, use the **VS Code** column (it works with any remote). In-editor Pull Requests on GitLab need the **GitLab Workflow** extension; on the web, PRs/MRs always work.

Three workflows:
1. **Working solo** — your own projects
2. **Working on a team** — Git Flow with `main / dev / feature`
3. **Contributing to someone else's repo** — fork → change → PR

---

## The mental model (applies to all three)

Edit files → **stage** the ones you want → **commit** (saves locally + message) → **push** (uploads to GitHub/GitLab).

Three places a change lives: **files on disk** → **staged** (picked for next commit) → **commits** (saved history).

**File status letters (VS Code):** **U** untracked (new) · **M** modified · **A** added/staged · **D** deleted

**Visual tells:**
- **● dot on tab** (VS Code) = unsaved edits → save (Cmd/Ctrl+S) before committing
- **Hollow circle** in graph = committed locally, not pushed
- **Filled circle** = pushed, local and remote in sync

> **Big difference to know:** **GitHub Desktop auto-saves nothing and has no "stage" buttons per se** — instead it shows checkboxes next to each changed file. **Checked = will be committed** (same idea as staging). VS Code uses a **+** button to stage. Same concept, different control.

---

# 1) Working Solo

For your own projects. Keep it light—no PRs, no `dev` layer needed.

## First time: get a local repo onto a remote

| Step | VS Code | GitHub Desktop |
|---|---|---|
| Put a local-only repo online | Source Control panel → **Publish to GitHub** button (GitHub only). For GitLab: **... menu → Remote → Add Remote**, paste URL, then **Publish Branch**. | **Publish repository** button in the top bar → choose name + public/private. |

## The everyday solo loop

| Step | VS Code | GitHub Desktop |
|---|---|---|
| 1. Make a feature branch (optional) | Click branch name (bottom-left) → **Create new branch...** | **Current Branch** dropdown (top) → **New Branch** |
| 2. Edit & save files | Edit, then **Cmd/Ctrl+S** (● → X on tab) | Edit in your editor; Desktop sees changes automatically |
| 3. See your changes | **Source Control** panel (branch icon, left bar) → **Changes** | **Changes** tab (left side) lists changed files |
| 4. Pick what to commit | Click **+** next to each file → moves to **Staged Changes** | **Check the box** next to each file you want |
| 5. Write a commit message | Type in the message box | Type in **Summary** box (bottom-left) |
| 6. Commit | **Commit** button | **Commit to [branch]** button |
| 7. Push — first time on branch | **Publish Branch** button | **Publish branch** button (top) |
| 7. Push — after that | **Commit & Push**, or **Sync (↻)** | **Push origin** button (top) |

## Finishing a feature (solo — no PR needed)

| Step | VS Code | GitHub Desktop |
|---|---|---|
| 8. Switch to `main` | Branch name (bottom-left) → pick `main` | **Current Branch** dropdown → `main` |
| 9. Merge your feature in | **... menu → Branch → Merge Branch...** → pick feature | **Branch menu (top) → Merge into current branch...** → pick feature |
| 10. Push `main` | **Sync (↻)** | **Push origin** |
| 11. Delete the old branch | Branch name → right-click branch → **Delete** | **Branch menu → Delete...** |

> **Need PRs solo?** No. The PR is a review gate for teams. Skip it unless you like the PR diff view as a self-check.

---

# 2) Working on a Team (Git Flow)

`main` (stable) → `dev` (integration) → `feature/` branches. PRs are the review gate—don't merge your own work straight up.

## Starting a feature

| Step | VS Code | GitHub Desktop |
|---|---|---|
| 1. Switch to `dev` | Branch name (bottom-left) → `dev` | **Current Branch** dropdown → `dev` |
| 2. Get latest `dev` | **Sync (↻)** | **Pull origin** (or **Fetch origin** then pull) |
| 3. Branch off `dev` | Branch name → **Create new branch...** → `feature/thing` | **Current Branch → New Branch** (confirm it's based on `dev`) |

## Working

| Step | VS Code | GitHub Desktop |
|---|---|---|
| 4. Edit → save → pick files → commit | Save → **+** to stage → message → **Commit** | Save → **check boxes** → Summary → **Commit to [branch]** |
| 5. Push — first time | **Publish Branch** | **Publish branch** |
| 5. Push — after | **Commit & Sync** | **Push origin** |

> On shared branches, **pull before you push** (VS Code **Sync** does this automatically; in Desktop, **Fetch/Pull origin** first). Avoids surprises from teammates' work.

## Staying fresh while you work

| Step | VS Code | GitHub Desktop |
|---|---|---|
| Pull new `dev` work into your branch | On your branch: **... menu → Branch → Merge Branch...** → pick `dev` | **Branch menu → Update from dev** (Desktop offers this directly when on a feature branch) |

Hit a conflict? See the [Conflicts Appendix](https://neely.github.io/shared/2026-06-25-VS-Code-GitHub-Desktop-Git-Cheat-Sheet/git-conflicts-appendix.md).

## Finishing

| Step | VS Code | GitHub Desktop |
|---|---|---|
| 6. Open a PR/MR (`feature` → `dev`) | **GitHub Pull Requests** extension → **Create Pull Request**. (GitLab: **GitLab Workflow** ext, or the website.) | **Create Pull Request** button (or **Branch menu → Create Pull Request**) — opens it on the web |
| 7. Review happens | (on the website / in extension) | (on the website) |
| 8. After merge: back to `dev`, get latest | Switch to `dev` → **Sync** | `dev` → **Pull origin** |
| 9. Delete feature branch | Right-click branch → **Delete** | **Branch menu → Delete** |

> **Releases:** someone opens a PR/MR `dev` → `main` when it's time to ship—same PR steps as above.

---

# 3) Contributing to Someone Else's Repo

You don't have push access to the original, so you work on a **fork** (your copy) and propose changes back via PR.

**Vocabulary:** **upstream** = the original repo · **origin** = *your fork*

> **GitHub Desktop makes this easy** — it understands forks and offers "contribute to the parent" automatically. **VS Code has no button to add `upstream`** — you do that part via the **... menu → Remote → Add Remote** (paste the original repo's URL, name it `upstream`).

## One-time setup

| Step | VS Code | GitHub Desktop |
|---|---|---|
| 1. Fork the repo | On the website → **Fork** button | On the website → **Fork** button (or **File → Clone**, pick the repo, Desktop offers to fork) |
| 2. Clone your fork | **Clone Repository** → paste your fork's URL | **File → Clone repository** → select your fork |
| 3. Link the original as `upstream` | **... menu → Remote → Add Remote** → paste original URL, name it `upstream` | **Automatic** — Desktop already knows the parent. Set **Branch menu → "Update from parent"** behavior. |

## Making a contribution

| Step | VS Code | GitHub Desktop |
|---|---|---|
| 4. Sync from the original first | Switch to `main` → **... menu → Pull (from)... → upstream/main** | **Branch menu → Update from parent** (pulls upstream's latest) |
| 5. Make a feature branch | Branch name → **Create new branch...** → `feature/fix-thing` | **Current Branch → New Branch** |
| 6. Edit → save → pick files → commit | Save → **+** → message → **Commit** | Save → **check boxes** → Summary → **Commit to [branch]** |
| 7. Push to **your fork** | **Publish Branch** (goes to your fork = `origin`) | **Publish branch** (goes to your fork) |

## Proposing it back

| Step | VS Code | GitHub Desktop |
|---|---|---|
| 8. Open PR (your fork → upstream) | **GitHub Pull Requests** ext → **Create Pull Request**, set base = upstream. (Or the website's "Compare & pull request" prompt.) | **Preview Pull Request / Create Pull Request** button → opens on web, base = upstream |
| 9. Address feedback | Commit + push more to the same branch — PR updates automatically | Same — commit + **Push origin**, PR auto-updates |
| 10. Maintainer merges | (done on the website) | (done on the website) |

## Keeping your fork current (for later)

| Step | VS Code | GitHub Desktop |
|---|---|---|
| Refresh your fork's `main` | Switch to `main` → **Pull from upstream/main** → **Sync** to update your fork | **Branch menu → Update from parent**, then **Push origin** |

---

## Button glossary

| What you want | VS Code | GitHub Desktop |
|---|---|---|
| Pick a file for the commit | **+** (stage) | **Checkbox** next to file |
| Remove a file from the commit | **−** (unstage) | **Uncheck** the box |
| Save changes locally | **Commit** | **Commit to [branch]** |
| Edit your last (unpushed) commit | **Commit (Amend)** | **Undo** last commit, recommit (no direct amend button) |
| Commit + upload | **Commit & Push** | **Commit**, then **Push origin** |
| Commit + pull-then-push | **Commit & Sync** | **Commit**, then **Pull** then **Push** |
| Put a new branch online | **Publish Branch** | **Publish branch** |
| Put a whole repo online | **Publish to GitHub** | **Publish repository** |
| Download remote changes | **Pull** (or **Sync**) | **Pull origin** / **Fetch origin** |
| Upload your commits | **Push** (or **Sync**) | **Push origin** |
| Switch branches | Branch name (bottom-left) | **Current Branch** dropdown |
| See a diff | **Open Changes** | Click a file in the **Changes** tab |

---

## Which workflow when?

- **Solo:** branches optional, merge yourself, no PRs.
- **Team:** feature off `dev`, always PR, pull before push on shared branches.
- **Contributing:** `origin` = your fork, `upstream` = original, PR from your fork back to upstream.

> **Reminder:** for **GitLab**, ignore the GitHub Desktop column and use **VS Code** — it's your one tool that works with both.
