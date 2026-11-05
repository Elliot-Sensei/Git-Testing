# Practice Exercises

Work through these in order. Each one builds on the last. Do these in a
throwaway copy of this folder/repo — it's meant for breaking things.

## The Big Picture: Branches, Merges, and GitHub

Before diving in, here's the mental model every exercise below is built
on. Your **local repo** has branches and merges; GitHub is just another
copy of that repo ("the remote") that you sync with using
push/fetch/pull. Nothing moves between any of these places automatically —
every arrow below corresponds to one explicit command you run:

- `git add` moves a change from your **working directory** into the
  **staging area**.
- `git commit` moves staged changes into your **local repo** as a
  permanent snapshot on whatever branch you're standing on.
- `git switch -c <branch>` splits off a new line of history so
  experiments don't touch `main`; `git merge <branch>` brings that line
  back in.
- `git push` uploads your local commits to GitHub. `git fetch` downloads
  what's new on GitHub without touching your files. `git pull` does
  both at once (fetch, then merge).

**Why this matters:**

- **Branches exist so work-in-progress never breaks `main`.** You can
  experiment, commit broken code, and abandon a branch entirely — `main`
  is untouched until you deliberately `merge`.
- **Merging is just "combine this branch's history into mine."** Git
  does it automatically when the changes don't overlap. When two
  branches edited the _same lines_, Git can't guess which one is
  correct — that's a **conflict**, and it stops to ask you.
- **Push and pull are the same idea, applied to a remote copy.** GitHub
  doesn't know about your commits until you `push`. You don't know
  about a teammate's commits until you `fetch` or `pull`. Nothing is
  shared automatically — that's what prevents accidental overwrites of
  other people's work.
- **`pull` = `fetch` + `merge`.** That's why a `pull` can conflict too:
  under the hood it's merging `origin/main` into your `main`, exactly
  like merging a local branch.

## Exercise 1 — First commit

**Why:** Git can't protect or version anything it doesn't know about.
`add` + `commit` is how you deliberately tell Git "this is a snapshot
worth remembering," as opposed to a text editor's autosave which just
overwrites the previous state with no history.

1. `git status` — see `team-notes.txt`, `todo.txt`, `recipe.txt` as untracked
   (if this is a fresh clone) or already committed (if cloned from the
   training repo as-is).
2. Open `team-notes.txt`, add a line under the agenda list.
3. `git add team-notes.txt` — _stages_ the change. Staging exists so you
   can choose exactly what goes into the next snapshot, even if you've
   edited five files but only want to commit one of them right now.
4. `git commit -m "Update team notes"` — creates the permanent snapshot.
   The message matters: six months from now, `git log` is how someone
   (maybe you) figures out _why_ a line changed, not just that it did.
5. `git log --oneline` to confirm.

## Exercise 2 — Diff before you commit

**Why:** `status` tells you _which files_ changed; `diff` tells you
_exactly what_ changed, line by line. Reading your diff before
committing is the single habit that catches the most embarrassing
mistakes — leftover debug code, accidental deletions, secrets pasted in
by accident.

1. Edit `todo.txt`, check off one item by changing `[ ]` to `[x]`.
2. `git diff` — notice it shows the change _before_ staging. This is
   your last chance to review before the change even enters staging.
3. `git add todo.txt`
4. `git diff --staged` — same change, now shown as staged. Git
   deliberately separates these two views (unstaged vs. staged) so you
   always know exactly what the _next commit_ will contain, even if
   your working directory has other unrelated edits sitting around.
5. `git commit -m "Check off first todo item"`

## Exercise 3 — Branching

**Why:** This is the `git switch -c` step from the overview above —
splitting off an isolated line of history to experiment in. If step 2
below goes horribly wrong, `main` was never touched; you just delete
the branch and nothing is lost.

1. `git switch -c practice/add-step` — creates the branch _and_ moves
   you onto it in one step. Everything you commit from here only
   exists on `practice/add-step` until it's merged.
2. Edit `recipe.txt`, add a 4th step to the Steps list (e.g. "Serve with
   syrup and fruit.").
3. `git add recipe.txt && git commit -m "Add serving step to recipe"`
4. `git switch main` — notice `recipe.txt` reverts to the 3-step version
   in your working directory. **This is expected, not a bug**: your
   commit still exists on `practice/add-step`, Git is just showing you
   `main`'s version of the file because that's the branch you're
   standing on right now.

## Exercise 4 — Merge conflict (do this as a pair/group)

**Why:** This is the `git merge` step from the overview above, except
we're deliberately engineering the "two branches edited the same line"
scenario so you experience a conflict safely, with the team, before it
happens for real with something that matters.

1. On `main`, edit `recipe.txt`'s step 3 to say "Cook on a hot griddle
   until golden brown, then flip carefully." Commit it on `main`.
   _(Now both `main` and `practice/add-step` have diverged from the
   same original file — exactly like two teammates editing the same
   function in parallel.)_
2. Switch back: `git switch practice/add-step`.
3. `git merge main` — this should conflict on `recipe.txt` because both
   branches touched overlapping content. **Git stopping here is a
   feature, not a failure** — it refuses to silently pick a winner and
   possibly delete someone's work.
4. Open `recipe.txt`, find the `<<<<<<<` / `=======` / `>>>>>>>` markers.
   Everything between `<<<<<<<` and `=======` is _your current
   branch's_ version; between `=======` and `>>>>>>>` is the _incoming_
   version. Git shows you both because only a human knows which one
   (or what combination) is actually correct.
5. Edit the file to keep both the step-3 wording change and the new
   4th step, then remove the conflict markers.
6. `git add recipe.txt` — this is how you tell Git "I've resolved this
   file," even though nothing else about `add` changed.
7. `git commit` (an editor will open with a pre-filled merge message —
   save and close it, or pass `-m "Merge main into practice/add-step"`).
   This creates a special **merge commit** with two parent commits,
   permanently recording that these two lines of history rejoined here.
8. `git log --oneline --graph --all` — see the merge commit tying both
   branches together, with two parent lines joining into one.

## Exercise 5 — Undoing

**Why:** "Undo" means different things in Git depending on _where_ the
unwanted change lives — still in your working directory, staged, just
committed, or already pushed/shared. Picking the wrong tool for the
situation can either fail to help or (for `reset --hard`) permanently
discard work, so walk through all four scenarios below to see when each
one applies.

**Scenario A — discard an edit you haven't staged yet**

1. Add an accidental line to `todo.txt`, don't stage or commit it.
2. `git status` — shows it as "Changes not staged for commit."
3. `git restore todo.txt` — throws the edit away and restores the file
   to its last committed state. This is safe _only_ because nothing was
   committed yet — there's no history to protect, so there's nothing
   wrong with deleting it outright.

**Scenario B — unstage a file without losing the edit**

4. Add another line to `todo.txt`, then `git add todo.txt`.
5. `git status` — shows it as "Changes to be committed."
6. `git restore --staged todo.txt` — moves it back to unstaged. Notice
   the edit itself is still there (check with `git diff`); only the
   staging decision was undone. Use this when you `git add .`'d too
   much and want to un-include one file before committing.

**Scenario C — fix the most recent commit before anyone else sees it**

7. `git add todo.txt && git commit -m "Fix todo list"`.
8. Realize you forgot something: add one more small edit to `todo.txt`.
9. `git add todo.txt`
10. `git commit --amend -m "Fix todo list (updated)"` — replaces the
    previous commit entirely instead of adding a new one. **Only do
    this on commits that are still local and unpushed** — amending
    rewrites the commit's identity, so if someone already pulled the
    original, their history and yours would now disagree.

**Scenario D — undo a commit that's already pushed/shared**

11. Make an unwanted edit to `todo.txt` and commit it:
    `git commit -am "Oops, bad change"`.
12. `git revert HEAD` — creates a **new** commit that undoes the last
    one, rather than deleting or rewriting it. An editor opens with a
    default message like "Revert \"Oops, bad change\"" — save and
    close it. Use `revert` (never `reset`/`amend`) once a commit is
    pushed or shared: it preserves history so teammates' copies aren't
    contradicted, while still fixing the mistake going forward.
13. `git log --oneline` — notice both the original bad commit _and_ the
    revert commit are visible. That transparency is the trade-off:
    slightly messier history, in exchange for never silently rewriting
    something someone else may already have.

## Exercise 6 — Push/pull scenarios with GitHub (do this with a teammate)

**Why:** Commits, branches, and merges all happen locally first — push
and pull are how that work actually reaches your team, and they're also
where most "wait, what happened to my changes?" confusion comes from.
Work through these four scenarios in order with a partner; they cover
the situations you'll actually run into.

> Setup: both of you `git clone` the same shared remote (or add it as a
> second remote to your existing practice repo) so you each have a
> local copy of the same history to start from.

**Scenario A — first push of a new branch**

1. `git switch -c practice/push-demo`
2. Edit `team-notes.txt`, commit it.
3. `git push -u origin practice/push-demo`
   - `push` uploads your local commits to GitHub; nothing on GitHub
     changes until this command runs — that's deliberate, you control
     exactly when your work becomes visible.
   - The `-u` (`--set-upstream`) only needs to happen once per branch:
     it tells Git "my local `practice/push-demo` corresponds to
     `origin/practice/push-demo`," so future `git push` / `git pull` on
     this branch don't need the remote and branch name spelled out.

**Scenario B — fast-forward pull (the easy case)**

4. Have your teammate `git fetch` then `git log main..origin/main
--oneline` — this shows them exactly what's new on GitHub _before_
   touching their files. `fetch` only updates Git's knowledge of the
   remote; it never changes your working directory.
5. Teammate runs `git pull` on `main` — since they have no conflicting
   local commits, Git just "fast-forwards" their branch pointer forward
   to match yours. No merge commit, no conflict — this is the common
   case when only one person has been working on a branch.

**Scenario C — push rejected (someone pushed first)**

6. Both of you, on your own clones, edit `recipe.txt` (different lines)
   and commit on `main`.
7. Whoever pushes first succeeds normally.
8. The second person runs `git push` — it's **rejected** with a message
   like "Updates were rejected because the remote contains work that
   you do not have locally." This is Git protecting the first person's
   push from being silently overwritten.
9. Fix it: `git pull` first (this merges the remote's new commit into
   your local branch, possibly opening an editor for a merge commit
   message), **then** `git push` again — now it succeeds, because your
   local branch contains both sets of commits.

**Scenario D — pull results in a merge conflict**

10. Both of you edit the **same line** of `recipe.txt` on `main` and
    commit, same as a local merge conflict but now across two different
    machines.
11. First push succeeds. Second person runs `git pull` — this time it
    conflicts, exactly like Exercise 4, because `pull` is just `fetch`
    - `merge` under the hood, and the merge step hits overlapping edits.
12. Resolve it the same way as Exercise 4: open the file, fix the
    `<<<<<<<`/`=======`/`>>>>>>>` markers, `git add recipe.txt`,
    `git commit` to finish the merge, then `git push` to share the
    resolution with the rest of the team.
