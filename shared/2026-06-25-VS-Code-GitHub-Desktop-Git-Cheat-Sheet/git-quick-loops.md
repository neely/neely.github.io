# Git Quick Loops

Just the order. See the full sheet for what each button does.

**Stage** = pick files for the commit (VS Code **+** / Desktop **checkbox**).
**Publish Branch** first time on a branch → **Push/Sync** after.

---

## 1) Solo

```
(optional) new feature branch
   → edit → save → stage → commit
   → Publish Branch (first time) / Push (after)
   → repeat
finish:
   → switch to main → merge feature in → push main → delete branch
```

No PRs needed.

---

## 2) Team (Git Flow)

```
switch to dev → Sync (get latest)
   → new feature branch off dev
   → edit → save → stage → commit
   → Publish Branch (first time) / Sync (after)
   → repeat
stay fresh (do this often!):
   → refresh dev (Sync) → merge dev into your feature branch
finish:
   → open PR/MR: feature → dev
   → review → merge
   → switch to dev → Sync → delete feature branch
release:
   → PR/MR: dev → main
```

**Update feature from dev:** whenever dev moves, and ALWAYS before opening your PR.
- VS Code: **... → Branch → Merge Branch... → dev**
- Desktop: **Branch → Update from dev** (one click)

---

## 3) Contributing (fork)

```
ONE-TIME:
   fork on website → clone your fork → add upstream (the original)
EACH CONTRIBUTION:
   switch to main → pull from upstream → new feature branch
   → edit → save → stage → commit
   → Publish Branch (goes to YOUR fork)
   → open PR: your fork → upstream
   → address feedback (push more to same branch, PR auto-updates)
   → maintainer merges
keep fork current:
   main → pull from upstream → push (updates your fork)
```

**origin = your fork · upstream = the original.**
- Add upstream — VS Code: **... → Remote → Add Remote**. Desktop: automatic ("Update from parent").

---

## Universal reminders

- **● dot on tab** = unsaved → save before commit
- **Hollow circle** = local only · **Filled** = pushed
- **Shared branch** = pull before you push (Sync does this)
- **GitLab** = use VS Code, not GitHub Desktop
```
