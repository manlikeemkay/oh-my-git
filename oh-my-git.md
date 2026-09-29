# 😱 Oh My Git — The Developer Cheat Sheet

**Less friction. Better commits. A few “Git can do that?” moments.**

[🎛️ Setup](#setup) · [⚡ Flow](#flow) · [✨ Commits](#commits) · [🔎 Investigate](#investigate) · [🛟 Rescue](#rescue) · [🚀 Ship](#ship)

> **📌 Before You Paste:** Bash/Zsh examples. Replace sample paths, refs, and hashes. Start with `git status`; run recipes independently, one step at a time.
>
> **🔧 Compatibility:** `switch` / `restore`: Git 2.23+. `zdiff3`: 2.35+. Config examples affect this repo; add `--global` for all repos.
>
> **Legend:** 💎 Hidden gem · ⚠️ Check the impact · 📚 Official docs

<a id="setup"></a>

## 🎛️ Tune Your Terminal

### 🎛️ Your Command Palette

**Set Once → Use Daily**

```sh
git config alias.st 'status --short --branch'
git config alias.lg 'log --graph --decorate --oneline -20'
git config alias.staged 'diff --cached'
```

`st` → status · `lg` → history graph · `staged` → next commit’s patch

**Where Did That Setting Come From?**

```sh
git config --show-origin --show-scope --get-all pull.rebase
```

💡 Swap `pull.rebase` for any config key. No output = unset.

📚 [Git configuration and aliases](https://git-scm.com/docs/git-config).

### 🧩 Better Conflict Context

```sh
git config merge.conflictStyle zdiff3
```

💡 Shows the common ancestor inside future conflicts. Older Git? Use `diff3`.

Applies to new conflicts; existing markers stay as they are.

📚 [Conflict styles](https://git-scm.com/docs/git-config#Documentation/git-config.txt-mergeconflictStyle).

### 📊 Branch Dashboard

```sh
git for-each-ref --sort=-committerdate \
  --format='%(align:32)%(refname:short)%(end) %(align:18)%(committerdate:relative)%(end) %(authorname)' \
  refs/remotes/origin/
```

**Output:** branch → tip age → author. Newest commits first; no `column` dependency.

⚠️ Age = tip commit age. Run `git fetch origin` to refresh refs; `origin/HEAD` may appear.

📚 [Ref sorting and formatting](https://git-scm.com/docs/git-for-each-ref).

<a id="flow"></a>

## ⚡ Keep Your Flow

### 💎 Two Branches, Two Folders

**Interruption? Open A Separate Working Directory.**

```sh
git fetch origin
git worktree add -b hotfix/login ../project-hotfix origin/main
git worktree list
```

💡 Work in `../project-hotfix`; your original changes stay put. History is shared; files and indexes are separate. Install dependencies there if needed.

**Clean Up When The Worktree Is Clean:**

```sh
git worktree remove ../project-hotfix
```

⚠️ Removal keeps the branch. The same branch normally cannot be checked out twice.

📚 [Git worktrees](https://git-scm.com/docs/git-worktree).

### 📦 Move Your Uncommitted Work

```sh
git stash push -u -m 'WIP: move to feature branch'
git switch feature/right-branch
git stash apply 'stash@{0}'
git status
```

⚠️ Confirm stash creation and branch switch before applying. `-u` includes untracked, not ignored files. `apply` keeps the stash; `--index` also restores staging state.

**After Review:** check `git stash list`, then drop the correct entry.

```sh
git stash drop 'stash@{0}'
```

💎 Too many conflicts? `git stash branch rescue/wip 'stash@{0}'` applies on the original base in a new branch. Drops the stash on success.

📚 [Stashing, applying, and stash branches](https://git-scm.com/docs/git-stash).

### 💎 Remember Conflict Fixes

**Enable Before Your Next Merge Or Rebase:**

```sh
git config rerere.enabled true
git config rerere.autoupdate false
```

Resolve once; Git can reuse the fix next time. Review before staging:

```sh
git rerere diff
git diff
git add path/to/resolved-file
```

⚠️ Test the reused fix, then continue your merge or rebase.

📚 [Reuse recorded resolutions](https://git-scm.com/docs/git-rerere).

<a id="commits"></a>

## ✨ Craft Cleaner Commits

### ✂️ Stage Only What You Need

```sh
git add -p -- src/cart.ts
git diff --cached
```

**Keys:** `y` stage · `n` skip · `s` split · `e` edit patch. Other edits stay in your file.

💡 New file? Run `git add -N -- src/new-file.ts` first. Review the staged patch.

📚 [Interactive staging](https://git-scm.com/docs/git-add#_interactive_mode).

### 🧹 Autosquash A Follow-Up Fix

**On Your Own Linear Feature Branch:**

```sh
git log --oneline origin/main..HEAD
git add -- src/cart.ts
git commit --fixup=abc1234
git rebase -i --autosquash origin/main
```

💡 Replace `abc1234` with a commit in the rebase range. Autosquash folds in the fix. Start clean; inspect the todo list.

⚠️ **Rewrites history.** Coordinate shared commits. Resolve + stage → `git rebase --continue`. Back out → `git rebase --abort`.

📚 [Fixup commits](https://git-scm.com/docs/git-commit), [autosquash](https://git-scm.com/docs/git-rebase).

### 💎 Review Your Rebase

**Before Rebase:** clean working tree, linear feature history, fresh backup name.

```sh
git branch backup/before-rebase
git merge-base origin/main HEAD
```

Copy the printed base ID, then:

```sh
git fetch origin
git rebase origin/main
```

**After Rebase:** replace `OLD_BASE_SHA` with the copied ID.

```sh
git range-diff OLD_BASE_SHA..backup/before-rebase origin/main..HEAD
```

💎 Compares commit patches and messages across the rewrite. Great for checking conflict resolutions. Keep the backup until satisfied.

📚 [Comparing commit ranges](https://git-scm.com/docs/git-range-diff).

### 🎨 Make Diffs Easier To Read

```sh
git diff --color-moved=dimmed-zebra
```

💡 Highlights moved lines. Add `--cached` for staged changes.

**Prefer Word-Level Changes?**

```sh
git diff --word-diff=plain
```

Review output only; do not apply it as a patch.

📚 [Moved-line and word diffs](https://git-scm.com/docs/git-diff).

<a id="investigate"></a>

## 🔎 Investigate Like A Pro

### 🕵️ Find Who Changed That

```sh
git log -p -S 'calculateDiscount' -- src/
git log -p -G 'timeout\s*=' -- src/
```

**`-S`** → string occurrence count changed. **`-G`** → added/removed lines match a regex. Add `--all` to search all refs.

📚 [Git log's pickaxe options](https://git-scm.com/docs/git-log).

### ⏳ Trace A Line Range

```sh
git log -L 40,80:src/cart.ts
```

💡 Shows patches for that line range through history. Use line numbers valid in the current file.

📚 [Line-range history](https://git-scm.com/docs/git-log).

### 🙈 Skip Formatting In Blame

Replace `abc1234` with the formatting commit:

```sh
git blame --ignore-rev abc1234 -- src/cart.ts
```

**Team Setup:** commit `.git-blame-ignore-revs` with one full hash per line, then opt in:

```sh
git config blame.ignoreRevsFile .git-blame-ignore-revs
```

⚠️ Attribution after skipping revisions can be ambiguous.

📚 [Ignoring revisions in blame](https://git-scm.com/docs/git-blame).

### 👻 Find The Ignore Rule

```sh
git check-ignore -v -- build/output.js
```

**Output:** rule file → line number → pattern. No match = no output, exit `1`.

💡 Already tracked? Add `--no-index` to inspect rules. Ignore rules do not untrack files.

📚 [Debugging ignore rules](https://git-scm.com/docs/git-check-ignore).

### 🤖 Let A Test Find The Bug

**Start Clean:** current commit fails; `GOOD_COMMIT_SHA` passes.

```sh
git bisect start
git bisect bad HEAD
git bisect good GOOD_COMMIT_SHA
git bisect run ./scripts/check-regression.sh
```

**Script Exit Codes:** `0` good · `1`–`127` bad, except `125` skip.

⚠️ Use an executable script testing the checked-out revision. Validate its environment: missing commands can return `127`. Skips can leave ambiguity.

**Record The Result, Then Return:**

```sh
git bisect reset
```

📚 [Automated bisection](https://git-scm.com/docs/git-bisect#_bisect_run).

<a id="rescue"></a>

## 🛟 Rescue Your Work

### 🛟 Recover A Lost Commit

```sh
git reflog --all --date=relative
```

Pick the lost commit’s hash, inspect it, then rescue it:

```sh
git show abc1234
git branch rescue/recovered-work abc1234
```

⚠️ Leaves current files alone. Reflogs are local, expire, and cannot recover unrecorded edits.

📚 [Reference logs](https://git-scm.com/docs/git-reflog).

### ↩️ Unstage Without Losing Edits

```sh
git restore --staged -- src/cart.ts
```

💡 Keeps your working edits; resets staging to `HEAD`. Requires an existing commit.

⚠️ Without `--staged`, `restore` overwrites unstaged edits with the index version.

📚 [Restore sources and destinations](https://git-scm.com/docs/git-restore).

### 📥 Borrow A File

**Preview:**

```sh
git show origin/main:src/cart.ts
```

**Replace:**

```sh
git restore --source=origin/main --worktree -- src/cart.ts
git diff -- src/cart.ts
```

⚠️ **Overwrites local file content.** Save edits first. Review and stage the replacement yourself.

📚 [Restoring from another tree](https://git-scm.com/docs/git-restore).

### 🧳 Pack Your History To Go

```sh
git bundle create ../project-backup.bundle --all
git bundle verify ../project-backup.bundle
```

**Restore Into A New Directory:**

```sh
git clone ../project-backup.bundle ../project-restored
```

⚠️ Git history only. Back up uncommitted files, config, hooks, and external LFS payloads separately.

📚 [Git bundles and their limitations](https://git-scm.com/docs/git-bundle).

<a id="ship"></a>

## 🚀 Ship And Tidy Up

### 🪪 Fix Your Commit Identity

**Future Commits:**

```sh
git config user.name 'Your Correct Name'
git config user.email 'your-new-email@example.com'
```

**Your Latest Unpublished Commit:** set identity above; check staging first.

```sh
git commit --amend --no-edit --reset-author
```

⚠️ **Rewrites the commit**, includes staged changes, and resets author + author date to you/now. Use only for your own work.

**Historical Display:** add to `.mailmap` at the repo root.

```text
Your Correct Name <your-new-email@example.com> <your-old-email@example.com>
```

💡 Check with `git log --use-mailmap`; commit the mapping to share it. Original identities remain in commit objects.

📚 [Identity configuration](https://git-scm.com/docs/git-config), [amending authorship](https://git-scm.com/docs/git-commit), [mailmap format](https://git-scm.com/docs/gitmailmap).

⚠️ **Full Identity Rewrite?** Plan a backed-up, coordinated migration. Git discourages `filter-branch`; see its [official alternative](https://git-scm.com/docs/git-filter-branch#_warning), `git-filter-repo` (separate install).

### 🔐 Push With An Explicit Lease

**Before An Agreed Rewrite:** capture the remote tip.

```sh
git fetch origin
expected_tip=$(git rev-parse refs/remotes/origin/feature/my-work)
```

Inspect fetched history; preserve the intended work. Rewrite and review, then in the **same shell**:

```sh
git push \
  --force-with-lease="refs/heads/feature/my-work:$expected_tip" \
  origin HEAD:refs/heads/feature/my-work
```

⚠️ **Changes remote history.** Rejects if the remote tip changed; background fetches cannot refresh this explicit expectation. A lease does not validate your rewrite. Rejected? Fetch and inspect.

📚 [Explicit force-with-lease semantics](https://git-scm.com/docs/git-push).

### 🏷️ Delete A Remote Tag

**Inspect:**

```sh
git ls-remote --tags origin refs/tags/v1.2.3
```

**Delete After Checking:**

```sh
git push origin --delete refs/tags/v1.2.3
```

💡 Full ref avoids branch/tag ambiguity. Local cleanup: `git tag -d v1.2.3`.

⚠️ Removes the published tag. Existing clones can retain copies.

📚 [Deleting remote refs](https://git-scm.com/docs/git-push).

---

**📚 Keep Exploring:** `git help everyday` · `git help <command>`
