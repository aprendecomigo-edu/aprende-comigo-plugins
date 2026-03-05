---
description: >
  This skill should be used when the user wants to open a pull request, submit work for review, or push a branch and create a PR. Trigger phrases: "open a PR", "create a pull request", "submit a PR", "open pull request against staging", "push and open PR", "create PR for this branch".
---

# Create Pull Request

Push the current feature branch, verify tests, open a pull request against `staging`, then clean up the worktree if one was used.

## Step 1: Verify State

```bash
git status
git log --oneline staging..HEAD
```

- Uncommitted changes → ask the user whether to commit or stash first.
- No commits ahead of `staging` → stop, tell the user there is nothing to PR.
- Current branch is `staging` or `main` → stop, ask which branch they intended.

## Step 2: Verify Tests Pass

Run the project's test suite before creating the PR:

```bash
pnpm test
```

If tests fail, stop and report:

```
Tests failing (<N> failures). Must fix before creating PR:

[Show failures]

Cannot proceed until tests pass.
```

Do not create the PR until tests are green.

## Step 3: Push Branch

```bash
git push -u origin <feature-branch>
```

If the push is rejected (non-fast-forward), do not force-push. Tell the user and ask how to proceed.

## Step 4: Create PR

Derive the title from the branch name and commits:

```bash
git log --oneline staging..HEAD
```

**Title format**: `feat: <what was built>` or `fix: <what was fixed>` — concise, imperative, under 72 characters.

If the caller provides a spec summary or quality review notes, incorporate them into Summary and Test Plan.

```bash
git push -u origin <feature-branch>

gh pr create \
  --base staging \
  --title "<title>" \
  --body "$(cat <<'EOF'
## Summary
- <what changed — 2-3 bullets drawn from commits and any provided spec>

## Test Plan
- [ ] <verification steps>
EOF
)"
```

Return the PR URL.

## Step 5: Cleanup Worktree

Check whether the current working directory is a worktree:

```bash
git worktree list | grep $(git branch --show-current)
```

If the branch appears in the worktree list (i.e. this is not the main working tree):

```bash
git worktree remove <worktree-path>
```

If the worktree has uncommitted changes, `git worktree remove` will fail — report this to the user and do not force-remove.

After removal, tell the user which path was cleaned up.

## Return

Report:
- PR URL
- Worktree removed (path) or "no worktree to clean up"
