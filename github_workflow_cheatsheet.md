# GitHub Workflow Cheatsheet

This document provides a quick reference for common Git commands and workflows when working with a GitHub repository.

## Basic Workflow

1.  **Check Status:** See what files have been changed, staged, or are untracked.
    ```bash
    git status
    ```

2.  **Stage Changes:** Add files to the staging area to be included in the next commit.
    *   Add a specific file:
        ```bash
        git add path/to/your/file.liquid
        ```
    *   Add all changes in the current directory and subdirectories (includes new files, modifications, and deletions):
        ```bash
        git add .
        ```

3.  **Commit Changes:** Save your staged changes to the local repository with a descriptive message.
    ```bash
    git commit -m "Your descriptive commit message here"
    ```
    *   For more detailed messages, you can omit `-m` and Git will open your configured text editor:
        ```bash
        git commit
        ```

4.  **Pull Changes (Before Pushing):** It's good practice to pull the latest changes from the remote repository before pushing your own, to avoid conflicts or ensure you're up-to-date.
    *   **Simple Pull (merges remote changes into your local branch):**
        ```bash
        git pull origin main 
        ```
        (Replace `main` with your branch name if different)
        If there are diverged histories, this will create a merge commit.

    *   **Pull with Rebase (attempts to re-apply your local commits on top of remote changes for a linear history):**
        ```bash
        git pull --rebase origin main
        ```
        This can be cleaner but sometimes leads to more complex conflict resolution if the same lines of code were changed in both local and remote commits.

5.  **Push Changes:** Upload your local commits to the remote repository (GitHub).
    ```bash
    git push origin main
    ```
    (Replace `main` with your branch name if different)

## Handling Merges & Conflicts

### During `git pull` (Merge Strategy - Default)
*   If `git pull` results in a merge conflict, Git will tell you which files are conflicted.
*   Open the conflicted files. You'll see markers like:
    ```
    <<<<<<< HEAD
    [Your local changes]
    =======
    [Incoming changes from remote]
    >>>>>>> [commit hash of remote changes]
    ```
*   Manually edit the file to resolve the conflict: choose which version to keep, or combine them. Remove the `<<<<<<<`, `=======`, and `>>>>>>>` markers.
*   After resolving all conflicts in all files:
    ```bash
    git add . 
    git commit 
    ```
    (Git usually provides a default merge commit message, which you can edit or accept.)

### During `git pull --rebase`
*   If a conflict occurs during a rebase:
    1.  Git will pause and tell you which file(s) are conflicted.
    2.  Resolve the conflicts in the file(s) manually (same as above, removing markers).
    3.  Stage the resolved file(s):
        ```bash
        git add path/to/conflicted/file.liquid
        ```
    4.  Continue the rebase:
        ```bash
        git rebase --continue
        ```
    5.  Repeat if more conflicts occur on subsequent commits being rebased.
*   **If a rebase gets stuck or you want to stop:**
    *   `git rebase --abort`: Cancels the rebase and returns your branch to its state before the rebase began. This is often the safest way to get out of a confusing rebase.
    *   `git rebase --skip`: Skips the current commit that is causing a conflict and moves to the next. Use with caution, as it means the changes from the skipped commit won't be applied.

### If `git status` shows "All conflicts fixed but you are still merging"
*   This means a previous `git merge` (often from a `git pull`) was started, conflicts were resolved, but the merge wasn't finalized with a commit.
*   **To finalize:**
    1.  Make sure all intended changes for this merge are staged:
        ```bash
        git add .
        ```
    2.  Commit to complete the merge:
        ```bash
        git commit -m "Merge remote-tracking branch 'origin/main' and resolve conflicts" 
        ```
        (Or let Git provide the default merge message by just running `git commit`)

## Viewing History

*   **Simple Log:**
    ```bash
    git log
    ```
*   **One-line Log with Graph & Decorations (shows branches/tags):**
    ```bash
    git log --oneline --graph --decorate
    ```
*   **Log for a Specific File:**
    ```bash
    git log path/to/your/file.liquid
    ```

## Branching (Basic)

*   **List Branches:**
    ```bash
    git branch
    ```
*   **Create a New Branch:**
    ```bash
    git branch new-feature-branch
    ```
*   **Switch to a Branch:**
    ```bash
    git checkout new-feature-branch
    ```
*   **Create and Switch to a New Branch:**
    ```bash
    git checkout -b new-feature-branch
    ```
*   **Merge Another Branch into Your Current Branch:**
    ```bash
    git merge other-branch-name
    ```

## Undoing Changes

*   **Discard Changes in Working Directory (for a specific file, since last commit):**
    ```bash
    git checkout -- path/to/your/file.liquid
    ```
*   **Unstage a File (remove from staging area, but keep changes in working directory):**
    ```bash
    git reset HEAD path/to/your/file.liquid
    ```
*   **Revert a Commit (creates a new commit that undoes the changes from a previous commit):**
    ```bash
    git revert <commit-hash>
    ```
*   **Reset to a Previous Commit (use with extreme caution, can lose history):**
    *   `git reset --soft <commit-hash>`: Moves HEAD pointer, keeps changes staged.
    *   `git reset --mixed <commit-hash>` (default): Moves HEAD, unstages changes but keeps them in working dir.
    *   `git reset --hard <commit-hash>`: Moves HEAD, **discards all changes** (staged and unstaged) since that commit.

This covers many common scenarios. Remember to replace `main` with your actual primary branch name if it's different (e.g., `master`). 