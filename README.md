# git
everything about github 
Git is nothing but a version controlling system.<Br>It helps to track all of our task.




1) git merge — bring changes together (safe & non-destructive)
What it does

Merges another branch into the current branch. If the current branch can just move forward to the other branch's tip, Git does a fast-forward. If both branches have new commits since the split, Git creates a merge commit that records both parents.

Fast-forward example

Before:

A---B---C   (main)
         \
          D---E   (dev)


If main has no new commits since branch, and you run git checkout main; git merge dev, result (fast-forward) is:

A---B---C---D---E   (main, dev)

Non-fast-forward (merge commit) example

If both branches diverged:

A---B---C---F   (main)
     \
      D---E     (dev)


git checkout main; git merge dev → creates a merge commit M:

A---B---C---F---M   (main)
     \         /
      D---E---/     (dev)

Commands
git checkout main
git fetch origin         # optional good practice
git merge dev
# if conflicts -> fix files, git add <files>, git commit (merge commit auto message)

Conflict handling

When conflicts occur, Git stops and marks files. Resolve conflicts manually, git add resolved files, then git commit (Git may auto-create merge commit once staged).

Pros / Cons

Pros: preserves full history and context; safe for public/shared branches.

Cons: history can become non-linear (merge commits), but that’s often desirable for traceability.

When to use

Merging finished feature branches into main or dev.

Collaborative environments where history should not be rewritten.

2) git rebase — rewrite history to make it linear
What it does

Takes commits from your current branch and replays them on top of another branch (e.g., main). It rewrites commit hashes (new commits), producing a linear history.

Example (dev rebased onto main):

Before:

A---B---C   (main)
     \
      D---E   (dev)


git checkout dev; git rebase main → result:

A---B---C---D'---E'   (dev)


D' and E' are new commits with new hashes.

Commands
git checkout dev
git fetch origin
git rebase origin/main   # replay dev commits on top of origin/main
# if conflicts -> resolve, git add <file>, git rebase --continue
# to abort rebase: git rebase --abort

Conflict handling

Same as merge but use git rebase --continue after resolving each conflict. You can also git rebase --skip to skip a patch.

Pros / Cons

Pros: creates a clean, linear history (great for git log, bisecting); ideal for preparing a feature branch for PR.

Cons: rewrites history — never rebase commits that have been pushed and shared with others (unless everyone coordinates and force-pushes). Rewriting public history causes confusion and extra work for collaborators.

When to use

Rebase local commits before pushing or before creating a PR to keep history tidy.

Use git pull --rebase to replay local commits on top of remote when syncing.

3) git cherry-pick — copy single commits across branches
What it does

Copies one (or more) specific commits from one branch and applies them to the current branch — creating new commits with the same changes but different hashes.

Example:

# on main
git cherry-pick <commit-hash-from-dev>


Result:

The commit’s changes appear on main as a new commit with its own hash.

Commands
# while on target branch where you want the change
git cherry-pick <commit-hash>
# conflicts -> fix file, git add, git cherry-pick --continue
# abort -> git cherry-pick --abort

Pros / Cons

Pros: precise: bring a hotfix or a feature commit to another branch without merging the whole branch.

Cons: duplicates the same changes (different commit IDs) which can cause future merge conflicts or confusion if not handled carefully.

When to use

Backporting a bugfix from dev to main (or to a release branch).

Copying a single commit without merging unrelated development.

4) Create dev from main after commits (branching & resetting)
Two interpretations and how to do each:
A) Create a new dev branch from current main tip

If you want a brand new dev that starts at current main (which already has new commits):

git checkout main
git pull origin main        # update to latest
git checkout -b dev         # new dev branch created at main tip
git push -u origin dev      # push dev to remote

B) Move existing dev to point exactly to main (discard dev’s current commits)

Dangerous — this loses dev’s commits if they are not merged/preserved.

git checkout dev
git reset --hard main
# or to reset to remote main
git reset --hard origin/main
git push --force origin dev   # if dev already pushed (force required)

When to use

A) When you want dev to include latest main commits and start fresh. Safe.

B) When dev is wrong or you intentionally want to discard dev’s history — be careful and ensure no collaborator needs it.

Visual scenarios — when to choose which
Scenario 1 — main had commits, dev had commits (diverged)

You want dev to include recent main commits:

Option A (merge main into dev):

git checkout dev
git merge main


Keeps dev commits intact; results in merge commit if diverged.

Option B (rebase dev onto main):

git checkout dev
git rebase main


Rewrites dev commits’ history to appear after main. Cleaner history.

Choose merge if branch is shared or public; choose rebase for local/private cleaning.

Scenario 2 — dev commits are ready → integrate into main

If dev is stable: git checkout main; git merge dev (creates merge commit if needed).

Or via Pull Request on GitHub: open PR from dev → main, review and merge (optionally squash & merge).

Scenario 3 — only want specific commit

Use git cherry-pick <hash> on the target branch.

Conflict resolution quick commands
During a merge

git merge dev

Git reports conflicts; files show <<<<<, >>>>>.

Edit files to resolve.

git add <resolved-files>

git commit (finalizes merge commit)

During a rebase

git rebase main

Git stops at conflict.

Resolve file(s).

git add <resolved-files>

git rebase --continue

To give up: git rebase --abort

During cherry-pick

Resolve conflicts, then git add, then git cherry-pick --continue, or git cherry-pick --abort.

Important safety rules (don’t break teammates’ workflows)

Never rebase or force-push public/shared branches (like main or a shared dev) — it rewrites history and forces everyone else to reconcile.

It’s OK to rebase your own local feature branch before push — tidies commits.

Use git pull --rebase on feature branches to avoid pointless merge commits when pulling remote updates.

Always git fetch before merging/rebasing to work with the latest remote state.

Practical cheat-sheet (commands)
# Merge dev into main (safe)
git checkout main
git fetch origin
git merge origin/dev    # or git merge dev (if local up-to-date)

# Bring main changes into dev (merge)
git checkout dev
git fetch origin
git merge origin/main

# Bring main changes into dev (rebase - rewrites dev commits)
git checkout dev
git fetch origin
git rebase origin/main

# Cherry-pick a single commit to your current branch
git cherry-pick <commit-hash>

# Create branch from current main tip
git checkout main
git pull origin main
git checkout -b dev

# Force dev to be exactly same as main (destructive)
git checkout dev
git reset --hard main
git push --force origin dev



#Resolving Marge Conflict

An event that takes place when git is unable to automatically resolve differences in code between two commits.

---Bye Jalil----
--Hello Jalil---



1️⃣ git reset

git reset is used to move the current branch’s HEAD to a specific commit.
It can operate in three modes:

Mode	Command	What it does
soft	git reset --soft <commit>	Moves HEAD to <commit> but keeps changes staged in the index (ready to commit again)
mixed (default)	git reset <commit> or git reset --mixed <commit>	Moves HEAD to <commit> and unstages changes but keeps them in the working directory
hard	git reset --hard <commit>	Moves HEAD to <commit> and removes all changes from index & working directory (dangerous!)
2️⃣ Using HEAD~1

HEAD points to the current commit.
HEAD~1 means “one commit before HEAD” (the parent of the current commit).

Example: Undo last commit but keep changes staged
git reset --soft HEAD~1


Moves HEAD to previous commit

Keeps all changes from the undone commit in the staging area

Example: Undo last commit and unstage changes
git reset HEAD~1


Default --mixed

HEAD moves back one commit

Changes are in the working directory but unstaged

Example: Undo last commit completely
git reset --hard HEAD~1


Removes the last commit

All changes lost (not recoverable unless reflog is used)

3️⃣ Using a specific commit hash

Instead of HEAD~1, you can reset to a specific commit hash:

Example: Undo to a specific commit
git reset --soft c787be5


Moves HEAD to commit c787be5

Keeps changes staged

git reset --hard c787be5


Moves HEAD to c787be5

Removes all changes after that commit

4️⃣ Safety tips

Use --soft or --mixed if unsure — this prevents accidental data loss.

Avoid --hard if commits were already pushed to remote (others may depend on them).

Check your current branch before resetting:

git status
git log --oneline


You can recover hard-reset commits using reflog:

git reflog
git checkout <old-hash>

✅ Quick Command Summary
# Undo last commit but keep changes staged
git reset --soft HEAD~1

# Undo last commit and unstage changes
git reset HEAD~1

# Undo last commit completely
git reset --hard HEAD~1

# Reset to a specific commit, keep staged
git reset --soft <commit-hash>

# Reset to a specific commit, remove changes
git reset --hard <commit-hash>