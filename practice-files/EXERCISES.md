# Practice Exercises

Work through these in order. Each one builds on the last. Do these in a
throwaway copy of this folder/repo — it's meant for breaking things.

## Exercise 1 — First commit

1. `git status` — see `team-notes.txt`, `todo.txt`, `recipe.txt` as untracked
   (if this is a fresh clone) or already committed (if cloned from the
   training repo as-is).
2. Open `team-notes.txt`, add a line under the agenda list.
3. `git add team-notes.txt`
4. `git commit -m "Update team notes"`
5. `git log --oneline` to confirm.

## Exercise 2 — Diff before you commit

1. Edit `todo.txt`, check off one item by changing `[ ]` to `[x]`.
2. `git diff` — notice it shows the change *before* staging.
3. `git add todo.txt`
4. `git diff --staged` — same change, now shown as staged.
5. `git commit -m "Check off first todo item"`

## Exercise 3 — Branching

1. `git switch -c practice/add-step`
2. Edit `recipe.txt`, add a 4th step to the Steps list (e.g. "Serve with
   syrup and fruit.").
3. `git add recipe.txt && git commit -m "Add serving step to recipe"`
4. `git switch main` — notice `recipe.txt` reverts to the 3-step version
   in your working directory.

## Exercise 4 — Merge conflict (do this as a pair/group)

1. On `main`, edit `recipe.txt`'s step 3 to say "Cook on a hot griddle
   until golden brown, then flip carefully." Commit it on `main`.
2. Switch back: `git switch practice/add-step`.
3. `git merge main` — this should conflict on `recipe.txt` because both
   branches touched overlapping content.
4. Open `recipe.txt`, find the `<<<<<<<` / `=======` / `>>>>>>>` markers.
5. Edit the file to keep both the step-3 wording change and the new
   4th step, then remove the conflict markers.
6. `git add recipe.txt`
7. `git commit` (an editor will open with a pre-filled merge message —
   save and close it, or pass `-m "Merge main into practice/add-step"`).
8. `git log --oneline --graph --all` — see the merge commit tying both
   branches together.

## Exercise 5 — Stash

1. Edit `team-notes.txt` but don't commit.
2. `git stash` — working directory is clean again.
3. `git switch main`, then back to your branch.
4. `git stash pop` — your edit returns.

## Exercise 6 — Undoing

1. Make an unwanted edit to `todo.txt` and commit it.
2. `git revert HEAD` — creates a new commit undoing the last one safely.
3. Make another unwanted edit to `todo.txt`, don't commit it this time.
4. `git restore todo.txt` — discards the uncommitted change.

## Exercise 7 — Remote sync (optional)

If your team has set up a shared remote for this practice repo:

```bash
git push -u origin practice/add-step
```

Then have a teammate `git fetch` and `git pull` to see your branch.
