# 🚀 Stacked PR Workflow & Git Alias Setup

This document explains:

1.  The Git aliases you should install
2.  How the stacked PR workflow works
3.  Why we use worktrees
4.  Daily workflow examples
5.  How to rebase and clean up safely

------------------------------------------------------------------------

# 🔧 Part 1: Required Git Aliases

Run these once on your machine:

## 1️⃣ Safer Force Push

``` bash
git config --global alias.pushf "push --force-with-lease"
```

Usage:

``` bash
git pushf
```

Why: - Safer than `--force` - Prevents overwriting someone else's
changes - Faster to type

------------------------------------------------------------------------

## 2️⃣ Visual Log for Stacked Branches

``` bash
git config --global alias.lg "log --graph --oneline --decorate --all"
```

Usage:

``` bash
git lg
```

Shows branch stacking clearly:

    * abc123 (acme-123-3)
    * def456 (acme-123-2)
    * ghi789 (acme-123-1)
    * 123abc (main)

------------------------------------------------------------------------

## 3️⃣ Quick Rebase onto Main

``` bash
git config --global alias.rbmain "!git fetch origin && git rebase origin/main"
```

Usage:

``` bash
git rbmain
```

Use when: - PR1 merges - You need to update PR2/PR3

------------------------------------------------------------------------

## 4️⃣ Create a New Worktree in One Command

``` bash
git config --global alias.wt-new '!f() { git worktree add -b "$1" "../$1" "${2:-HEAD}"; }; f'
```

Usage:

Create from current branch:

``` bash
git wt-new acme-123-2
```

Create from specific base:

``` bash
git wt-new acme-123-3 acme-123-2
```

This: - Creates branch - Creates folder - Checks it out

------------------------------------------------------------------------

## 5️⃣ List Worktrees

``` bash
git config --global alias.wt "worktree list"
```

Usage:

``` bash
git wt
```

------------------------------------------------------------------------

## 6️⃣ Remove Worktree

``` bash
git config --global alias.wt-rm "worktree remove"
```

Usage:

``` bash
git wt-rm ../acme-123-2
```

------------------------------------------------------------------------

# 📘 Part 2: Team Onboarding -- Understanding Stacked PRs

## What Is a Stacked PR?

Instead of putting all changes into one large PR, you split work into
multiple smaller PRs that build on each other:

    main
     └── PR1 (acme-123-1)
          └── PR2 (acme-123-2)
               └── PR3 (acme-123-3)

Each PR is: - Small - Focused - Reviewable - Independently testable

------------------------------------------------------------------------

## Why We Use Worktrees

Git normally only allows one checked-out branch per repo folder.

Worktrees allow:

-   Multiple branches checked out simultaneously
-   Each branch in its own folder
-   No constant branch switching
-   No dirty working directory issues

Example structure:

    acme_app/
    acme_app/acme-123-1/
    acme_app/acme-123-2/
    acme_app/acme-123-3/

Each folder is isolated and clean.

------------------------------------------------------------------------

## Daily Workflow

### Step 1: Create First PR

``` bash
git wt-new acme-123-1 main
cd ../acme-123-1
# make changes
git commit -m "Add API layer"
git push -u origin acme-123-1
```

Create PR targeting `main`.

------------------------------------------------------------------------

### Step 2: Create Second Stacked PR

``` bash
git wt-new acme-123-2 acme-123-1
cd ../acme-123-2
# make changes
git commit -m "Add validation"
git push -u origin acme-123-2
```

Create PR targeting `acme-123-1`.

------------------------------------------------------------------------

## When PR1 Merges

Inside PR2 worktree:

``` bash
git rbmain
git pushf
```

This: - Rebases onto latest main - Safely force-pushes updated history

------------------------------------------------------------------------

# 🧠 Why This Workflow Is Better

## Smaller PRs

-   Easier reviews
-   Faster approvals
-   Less cognitive load

## Cleaner History

-   Linear, readable commits
-   Clear dependency chain

## Less Context Switching

-   Each worktree is isolated
-   No branch juggling

------------------------------------------------------------------------

# 🧹 Cleaning Up

After merge:

``` bash
git wt-rm ../acme-123-1
git branch -d acme-123-1
```

Repeat for each merged branch.

------------------------------------------------------------------------

# ⚠️ Important Rules

1.  Always rebase stacked branches after lower PR merges
2.  Always use `pushf` instead of `--force`
3.  Never merge stacked PRs locally --- let GitHub handle merge
4.  Keep PRs small and focused

------------------------------------------------------------------------

# ✅ Summary

This system gives:

-   Faster reviews
-   Safer rebasing
-   Cleaner history
-   Parallel feature development
-   Minimal workflow friction

Once you get used to it, you won't want to go back.
