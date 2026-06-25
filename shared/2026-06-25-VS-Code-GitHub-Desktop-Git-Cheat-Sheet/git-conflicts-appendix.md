# Appendix: Resolving Merge Conflicts

Companion to the main cheat sheet. A conflict isn't an error you broke—it's Git saying *"two changes touched the same lines, you decide which wins."*

---

## When conflicts happen

When you **merge** (or pull/sync) and both sides changed the *same lines* of the *same file*. Most common moments for you:
- Updating your feature branch **from `dev`** (team flow)
- Updating your fork **from upstream** (contributing)
- A **Sync** that pulls teammate work overlapping yours

If changes touched *different* lines, Git merges them automatically—no conflict. Conflicts are only the genuine overlaps.

---

## What you'll see

- The merge stops partway. The app tells you there are conflicts.
- Conflicted files are flagged: **C** status in VS Code, or a "conflicted files" list in GitHub Desktop.
- Inside the file, Git inserts **conflict markers**:

```
<<<<<<< HEAD (Current Change)
your version of the lines
=======
the incoming version of the lines
>>>>>>> dev (Incoming Change)
```

- **Current / HEAD** = what's on *your* branch (where you are).
- **Incoming** = what's coming *in* (e.g., from `dev` or upstream).

---

## Resolving in VS Code

1. Open each conflicted file (they're listed under **Merge Changes** in Source Control).
2. VS Code shows colored blocks with clickable buttons above each conflict:
   - **Accept Current Change** — keep yours, drop theirs
   - **Accept Incoming Change** — keep theirs, drop yours
   - **Accept Both Changes** — keep both (stacked; you may need to reorder)
   - **Compare Changes** — side-by-side view to decide
3. Click the right one for each conflict. The markers disappear as you resolve.
4. **Or edit by hand:** delete the markers (`<<<<<<<`, `=======`, `>>>>>>>`) and shape the lines into the final version you want. (Sometimes the answer is a *blend*, not one side.)
5. **Save** the file.
6. **Stage** the resolved file (**+**) — this tells Git "this one's handled."
7. When all conflicts are staged, **Commit** to finish the merge. VS Code pre-fills a merge commit message; just accept it.

> VS Code also has a **3-way Merge Editor** (a button appears: "Resolve in Merge Editor"). It shows Current, Incoming, and Result panes—nicer for messy conflicts. Same accept/blend idea, clearer layout.

---

## Resolving in GitHub Desktop

GitHub Desktop is deliberately minimal here—it hands you off to your editor:

1. After a conflicting merge, Desktop shows a banner listing conflicted files.
2. Click **Open in [your editor]** (usually VS Code) — Desktop won't resolve them inline.
3. Resolve there using the steps above (the same conflict markers / buttons).
4. **Save** each file.
5. Back in GitHub Desktop, resolved files show a checkmark. When all are resolved, the **Continue merge / Commit merge** button activates.
6. Click it to finish, then **Push origin**.

> So in practice: **Desktop detects, VS Code resolves.** This is why the VS Code section above is the real "how."

---

## The mental checklist

```
merge → conflict → open each flagged file
   → for each conflict: pick Current / Incoming / Both, or hand-edit
   → delete all <<<<<<< ======= >>>>>>> markers
   → save → stage resolved file
all resolved → commit the merge → push
```

---

## Tips & gotchas

- **Don't leave markers behind.** If `<<<<<<<` or `=======` survive into a commit, the code is broken. Search the file for `<<<` before committing.
- **"Both" rarely means literally both.** Accepting both is a starting point—usually you then trim/reorder into something that actually works.
- **Smaller, more often = fewer conflicts.** Updating your feature branch from `dev` frequently keeps each conflict tiny. Waiting until the end stacks them up.
- **Test after resolving.** A merge can be "clean" to Git but still wrong logically—run/check your code.
- **Bail-out if it's a mess:** before you've committed the merge, you can abort and start over.
  - VS Code: **... menu → Branch → Abort Merge** (or Command Palette → "Git: Abort Merge")
  - GitHub Desktop: **Abort merge** button in the conflict banner
  - This returns you to before the merge—nothing lost, try again when ready.
- **Conflict status letters:** VS Code marks conflicts and groups them under **Merge Changes**; resolved-and-staged files move out of that group.

---

## Quick reference: the markers

| Marker | Meaning |
|---|---|
| `<<<<<<< HEAD` | Start of *your* version (Current) |
| `=======` | Divider between the two versions |
| `>>>>>>> branchname` | End of the *incoming* version |

Your job: delete all three markers and leave only the final lines you want.
