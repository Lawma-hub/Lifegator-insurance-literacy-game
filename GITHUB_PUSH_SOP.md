# SOP: Push Updates to GitHub from Local Terminal

**Purpose:** Step-by-step procedure to push code changes from a local Windows machine to a GitHub repository using Git terminal commands.

**Applies to:** Single-file HTML projects, static websites, GitHub Pages deployments

**Last updated:** 2026-07-10

---

## Prerequisites

Before starting, ensure you have:
1. **Git installed** on Windows (download from https://git-scm.com/download/win)
2. **GitHub account** with an existing repository
3. **PowerShell** or **Command Prompt** available
4. **Authentication method** set up (one of):
   - GitHub CLI (`gh auth login`) — recommended
   - Personal Access Token (PAT) — generate at github.com → Settings → Developer settings → Personal access tokens
   - SSH key configured

---

## Scenario A: First-Time Setup (Folder Not Yet a Git Repo)

Use this when your local folder has never been connected to GitHub.

### Step 1: Open Terminal and Navigate to Project Folder
```powershell
cd "c:\Users\lfwl6\Desktop\Test Code\Insurance Game"
```

### Step 2: Initialize Git Repository
```powershell
git init
```
**Expected output:** `Initialized empty Git repository in C:/Users/.../.git/`

### Step 3: Set Default Branch to `main`
```powershell
git branch -M main
```
**Why:** GitHub's default branch is `main`, not `master`.

### Step 4: Add GitHub Remote
```powershell
git remote add origin https://github.com/<your-username>/<your-repo-name>.git
```
**Example:**
```powershell
git remote add origin https://github.com/lawma-hub/Lifegator-insurance-literacy-game.git
```

### Step 5: Fetch Remote to Check for Existing Commits
```powershell
git fetch origin
```
**Expected output:**
- If repo is empty: no output
- If repo has commits: `* [new branch] main -> origin/main`

**Decision point:**
- If empty → skip to **Step 8**
- If has commits → continue to **Step 6**

### Step 6: Stage Your Local Files
```powershell
git add .
```
**Or for specific file:**
```powershell
git add gator_verse_game.html
```

### Step 7: Commit Your Local Changes
```powershell
git commit -m "Initial commit: add game features"
```

### Step 8: Handle Existing Remote Commits (Conflict Resolution)

**Problem:** Remote already has files, and your local has the same files → merge conflict.

**Solution:** Reset to remote, then re-apply your changes.

```powershell
# Reset to match remote, keeping your file changes in working tree
git reset --mixed origin/main

# Stage your updated file
git add gator_verse_game.html

# Commit
git commit -m "feat: add new features"
```

**Why this works:**
- `--mixed` unstages everything but keeps your file changes
- Your branch now matches remote, with your file showing as "modified"
- Clean commit on top of remote history

### Step 9: Push to GitHub
```powershell
git push origin main
```

**First-time push (if branch not set up):**
```powershell
git push -u origin main
```

**Expected output:**
```
To https://github.com/<username>/<repo>.git
   <old-commit>..<new-commit>  main -> main
```

---

## Scenario B: Subsequent Updates (Repo Already Set Up)

Use this for regular updates after the initial setup.

### Step 1: Navigate to Project Folder
```powershell
cd "c:\Users\lfwl6\Desktop\Test Code\Insurance Game"
```

### Step 2: Check Git Status
```powershell
git status
```
**Expected output:**
```
On branch main
Changes not staged for commit:
  modified:   gator_verse_game.html
```

### Step 3: Stage Changed Files
```powershell
git add gator_verse_game.html
```
**Or stage all changes:**
```powershell
git add .
```

### Step 4: Commit with Descriptive Message
```powershell
git commit -m "feat: add host game-over players display and insurance comparison"
```

**Commit message conventions:**
- `feat:` for new features
- `fix:` for bug fixes
- `docs:` for documentation changes
- `style:` for formatting (no code change)
- `refactor:` for code restructuring

### Step 5: Pull Latest Changes from Remote (Safety Check)
```powershell
git pull origin main
```
**Why:** Ensures you're not overwriting someone else's work.

**If conflict occurs:**
```powershell
# Abort the merge
git merge --abort

# Or resolve manually, then:
git add .
git commit -m "merge: resolve conflicts"
```

### Step 6: Push to GitHub
```powershell
git push origin main
```

---

## Scenario C: GitHub Pages Deployment Verification

After pushing, verify the live deployment.

### Step 1: Wait for GitHub Pages Build
- GitHub Pages auto-deploys from `main` branch
- Build time: ~1–2 minutes

### Step 2: Check Deployment Status
1. Go to your repo on GitHub
2. Click **Settings** → **Pages**
3. Check "GitHub Pages" section for build status

### Step 3: Hard Refresh the Live URL
```
https://<username>.github.io/<repo-name>/gator_verse_game.html
```
**In browser:** Press `Ctrl + Shift + R` (hard refresh to bypass cache)

### Step 4: Verify in DevTools
1. Open DevTools (`F12`)
2. Go to **Network** tab
3. Reload the page
4. Check file size matches your local version

---

## Troubleshooting

### Error: "fatal: not a git repository"
**Cause:** Folder not initialized as Git repo.
**Fix:** Run `git init` (Scenario A, Step 2).

### Error: "The following untracked working tree files would be overwritten by merge"
**Cause:** Remote has files that conflict with your local untracked files.
**Fix:** Use `git reset --mixed origin/main` approach (Scenario A, Step 8).

### Error: "CONFLICT (add/add): Merge conflict"
**Cause:** Both local and remote have the same file with different content.
**Fix:**
```powershell
# Keep your local version
git checkout --theirs <filename>
git add <filename>
git rebase --continue
```

### Error: "Vim editor opens during rebase"
**Cause:** Interactive rebase requires manual input.
**Fix:** Abort and use non-interactive approach:
```powershell
git rebase --abort
git reset --mixed origin/main
git add .
git commit -m "your message"
git push
```

### Error: "Authentication failed"
**Cause:** GitHub no longer accepts passwords for HTTPS.
**Fix:** Use one of:
- GitHub CLI: `gh auth login`
- Personal Access Token (use as password)
- SSH key (switch remote to `git@github.com:<user>/<repo>.git`)

### Error: "Updates were rejected because the remote contains work that you do not have locally"
**Cause:** Remote has newer commits.
**Fix:**
```powershell
git pull origin main
git push origin main
```

---

## Quick Reference Commands

| Action | Command |
|--------|---------|
| Check status | `git status` |
| Stage file | `git add <filename>` |
| Stage all | `git add .` |
| Commit | `git commit -m "message"` |
| Push | `git push origin main` |
| Pull latest | `git pull origin main` |
| View commit history | `git log --oneline` |
| Undo last commit (keep changes) | `git reset --soft HEAD~1` |
| Undo last commit (discard) | `git reset --hard HEAD~1` |
| Check remote URL | `git remote -v` |
| Change remote URL | `git remote set-url origin <new-url>` |

---

## Best Practices

1. **Commit often** with clear, descriptive messages
2. **Pull before pushing** to avoid conflicts
3. **Use branches** for experimental features:
   ```powershell
   git checkout -b feature/new-feature
   git push -u origin feature/new-feature
   ```
4. **Never commit secrets** (API keys, passwords)
5. **Use `.gitignore`** to exclude unnecessary files:
   ```
   node_modules/
   .env
   *.log
   .DS_Store
   ```
6. **Review before pushing:** `git diff` to see what changed

---

## Example: Complete Workflow

```powershell
# 1. Navigate to project
cd "c:\Users\lfwl6\Desktop\Test Code\Insurance Game"

# 2. Check status
git status

# 3. Stage changes
git add gator_verse_game.html

# 4. Commit
git commit -m "feat: add new game features"

# 5. Pull latest (safety)
git pull origin main

# 6. Push
git push origin main

# 7. Verify deployment
# Open browser: https://lawma-hub.github.io/Lifegator-insurance-literacy-game/gator_verse_game.html
# Hard refresh: Ctrl + Shift + R
```

---

## Additional Resources

- **Git documentation:** https://git-scm.com/doc
- **GitHub Guides:** https://guides.github.com
- **GitHub Pages:** https://pages.github.com
- **Conventional Commits:** https://www.conventionalcommits.org

---

**End of SOP**
