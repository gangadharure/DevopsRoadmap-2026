# 02 — Git, GitHub & GitLab

> Git is the universal language of software teams. If you can't `rebase` cleanly or write a good PR description, you'll never be senior — no matter how much cloud you know.

**Time:** 2 weeks · 1 hour/day
**Outcome:** Confident with branches, rebase, conflict resolution, PR workflow, GitHub Actions basics.

---

## 🎯 What you actually need

| # | Skill | Why |
|---|-------|-----|
| 1 | **Branching & merging** | Daily team work |
| 2 | **Rebase vs merge** | Clean history vs traceable history |
| 3 | **Conflict resolution** | Will happen daily |
| 4 | **Stash, cherry-pick, reset, revert** | The "oh no" recovery toolkit |
| 5 | **PR workflow** | How real teams ship |
| 6 | **GitHub Actions basics** | The default CI for most teams |
| 7 | **`.gitignore`, `.gitattributes`, hooks** | Repo hygiene |
| 8 | **SSH keys + signed commits** | Security |

---

## 📅 2-Week Roadmap

### Week 1 — Git fundamentals

**Day 1-2:** Setup + basics
```bash
git config --global user.name "you"
git config --global user.email "you@x.com"
git config --global init.defaultBranch main
git config --global pull.rebase true        # always rebase on pull
git init / clone / status / add / commit / log --oneline --graph
```

**Day 3:** Branching
```bash
git branch / checkout -b / switch -c
git merge feature-x        # creates a merge commit
git merge --ff-only        # fast-forward only (clean)
```

**Day 4:** Rebase (the senior skill)
```bash
git rebase main            # replay your commits on top of main
git rebase -i HEAD~5       # interactive: squash, reword, fixup, drop
git pull --rebase
```

**Day 5:** Conflicts
- Create a real conflict, resolve it, abort it, continue it
- `git mergetool`, `git diff`, `git log --all --graph --decorate`

**Day 6:** The recovery toolkit
```bash
git stash / stash pop / stash list
git cherry-pick <sha>
git reset --soft / --mixed / --hard
git revert <sha>           # the SAFE undo for shared branches
git reflog                 # the time machine — recovers "lost" commits
```

**Day 7:** Tags, hooks, ignore
- `git tag v1.0.0`, annotated vs lightweight
- `.gitignore` patterns, `.gitattributes` for line endings & LFS
- Client-side hooks: `pre-commit`, `commit-msg`
- Install **`pre-commit`** framework (Python tool, industry standard)

---

### Week 2 — GitHub & GitLab in practice

**Day 8-9:** GitHub deep dive
- Fork vs clone
- PR workflow: draft → review → squash-merge vs rebase-merge vs merge-commit
- **Code owners** file (`.github/CODEOWNERS`)
- Branch protection rules: required reviews, required checks, signed commits
- Issues, labels, milestones, projects (v2)
- **Releases** + Semantic Versioning

**Day 10:** GitHub Actions basics (full coverage in [`20-CI-CD-IaC/01-GitHub-Actions`](../../20-CI-CD-IaC/01-GitHub-Actions))
- A workflow file: triggers, jobs, steps, runners
- Marketplace actions vs custom

**Day 11:** SSH keys & signed commits
```bash
ssh-keygen -t ed25519 -C "you@x.com"
# add to GitHub: Settings → SSH and GPG keys
git config commit.gpgsign true      # SSH or GPG signing
```

**Day 12:** GitLab differences
- GitLab CI (`.gitlab-ci.yml`) vs GitHub Actions
- Merge Requests (MR) vs PRs (just naming)
- GitLab built-in container registry, CI/CD, package registry
- Self-managed vs gitlab.com

**Day 13:** Workflows
- **Trunk-based development** (modern, default)
- **GitHub Flow** (PR per feature → main)
- **GitFlow** (heavyweight, legacy, fine for release-train products)

**Day 14:** Advanced bits
- `git worktree` — multiple branches checked out at once
- `git bisect` — binary search for the bug
- `git submodule` (avoid) vs `git subtree`
- GitHub CLI: `gh pr create / gh pr checkout / gh repo clone`

---

## 📚 Official Resources

| Source | What |
|--------|------|
| **[Pro Git book (free, official)](https://git-scm.com/book/en/v2)** | THE Git book by Scott Chacon — read end-to-end |
| **[git-scm.com/doc](https://git-scm.com/doc)** | Official reference + videos |
| **[GitHub Docs](https://docs.github.com/)** | Auth, Actions, security, CLI |
| **[GitLab Docs](https://docs.gitlab.com/)** | GitLab CI, MRs, container registry |
| **[Conventional Commits](https://www.conventionalcommits.org/)** | Standard commit message format |
| **[Semantic Versioning (SemVer)](https://semver.org/)** | Version numbering standard |
| **[pre-commit framework](https://pre-commit.com/)** | The industry-standard hooks runner |

---

## 🧪 Hands-On Mini-Projects

1. Take a repo with messy history → use `rebase -i` to squash & reword into clean conventional commits.
2. Two contributors on the same file → resolve a real merge conflict via PR.
3. Configure a repo with: branch protection, CODEOWNERS, required PR review, required Actions check.
4. Sign all commits with SSH (`git config gpg.format ssh`).
5. Write a `pre-commit` config that runs `ruff`, `gitleaks`, and `conventional-commit` check on every commit.

---

## 🧠 Interview Questions

- "Rebase vs merge — when each?"
- "How do you recover a deleted commit?"
- "Walk me through your team's branching strategy."
- "How do you prevent secrets from being committed?"
- "What's the difference between `reset --soft / --mixed / --hard`?"
- "Squash-merge vs rebase-merge vs merge-commit?"
- "What is `git bisect` and when did you last use it?"

---

## ▶️ Next stop

[`00-Foundations/03-Networking-Basics`](../03-Networking-Basics) → networking essentials before you touch cloud.
