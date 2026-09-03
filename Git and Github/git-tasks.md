# Git & GitHub Experiments

Quick hands-on tests done in a scratch repo to see how Git behaves under the hood, with actual shell sessions and outputs captured along the way.

---

## Experiment 1: `git commit -m` vs `git commit -a -m`

The core difference comes down to how Git handles the staging area (index).

- **`git commit -m "..."`**: Only commits changes that are currently staged in the index. If you edit a file but forget to run `git add`, Git completely ignores the edit during commit time.
- **`git commit -a -m "..."`**: Automatically stages any modifications or deletions to files that Git is *already tracking*, and then creates the commit in one shot. It skips having to run `git add` for files Git already knows about.
- **The catch with untracked files**: `-a` will never pick up brand-new, untracked files. New files always require an explicit `git add`.

### Terminal Run

```text
$ git init -q -b main .
$ echo "todo: learn git" > todo.txt
$ git add todo.txt
$ git commit -q -m "first version of todo" && git log --oneline
5f6bfdb first version of todo

$ echo "todo: practice -a flag" >> todo.txt
$ git status -s
 M todo.txt

$ git commit -m "commit without -a"
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   todo.txt

no changes added to commit (use "git add" and/or "git commit -a")

$ git commit -a -m "commit with -a picks up the tracked change"
[main 62b474a] commit with -a picks up the tracked change
 1 file changed, 1 insertion(+)

$ echo "new file" > untracked.txt
$ git status -s
?? untracked.txt

$ git commit -a -m "does -a include untracked?"
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	untracked.txt

nothing added to commit but untracked files present (use "git add" to track)

$ git log --oneline
62b474a commit with -a picks up the tracked change
5f6bfdb first version of todo
```

### Quick Takeaways

1. Running plain `git commit -m` failed because our changes lived only in the working tree, not the staging area.
2. Passing `-a` staged the modified file and committed it together without an extra step.
3. `-a` outright refused to touch `untracked.txt` — Git warns you right away that new files still need an explicit `git add`.

---

## Experiment 2: `git cherry-pick`

Cherry-picking lets you pluck a specific commit from any branch and replay it directly onto your current branch. It produces a brand-new commit containing the exact same diff and commit message, while leaving the original branch untouched.

Super handy when someone commits a critical bugfix onto a feature or hotfix branch, and you need just that one fix on `main` without pulling in half-baked work or debug noise.

### Setup: Creating a `hotfix` branch with 3 commits

```text
$ echo "config v1" > config.txt && git add config.txt && git commit -q -m "Add config"
$ git switch -c hotfix
Switched to a new branch 'hotfix'

$ echo "logging on" > logging.txt && git add logging.txt && git commit -q -m "hotfix: enable logging"
$ echo "config v1 + port fix" > config.txt && git commit -q -a -m "hotfix: correct the port in config"
$ echo "temp debug" > debug.txt && git add debug.txt && git commit -q -m "hotfix: temporary debug file"

$ git log --oneline
951a91c hotfix: temporary debug file
6480118 hotfix: correct the port in config
e69654f hotfix: enable logging
6e0fc16 Add config
62b474a commit with -a picks up the tracked change
5f6bfdb first version of todo
```

We only want commit `6480118` (the port fix) back on `main`. The temporary debug file and logging setup should stay behind on `hotfix`.

### Cherry-picking the port fix commit onto `main`

```text
$ git switch main
Switched to branch 'main'

$ git cherry-pick 6480118
[main 5dbc089] hotfix: correct the port in config
 Date: Thu Sep 3 21:46:20 2026 +0530
 1 file changed, 1 insertion(+), 1 deletion(-)

$ git log --oneline
5dbc089 hotfix: correct the port in config
6e0fc16 Add config
62b474a commit with -a picks up the tracked change
5f6bfdb first version of todo

$ ls
config.txt
todo.txt
untracked.txt

$ cat config.txt
config v1 + port fix
```

### Key Takeaways

- `main` now includes the port fix, while `logging.txt` and `debug.txt` never touched `main`.
- The new commit on `main` has a different hash (`5dbc089` vs `6480118`) because its parent commit is different, though the diff, author, and timestamp are preserved.
- If there are conflicts during a cherry-pick, Git pauses and lets you resolve them manually, then you run `git cherry-pick --continue` (or abort safely with `--abort`).
- Useful flags to remember:
  - `git cherry-pick A..B` — cherry-picks a range of commits.
  - `-n` (`--no-commit`) — applies the changes directly to your working tree and staging area without creating a commit yet.
  - `-x` — automatically adds a note saying `(cherry picked from commit ...)` inside the commit message for an audit trail.
