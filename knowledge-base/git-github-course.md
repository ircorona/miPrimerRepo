# Git & GitHub: From Basic to Advanced — Knowledge Base

> Course: Control de Versiones con Git y GitHub: De Básico a Avanzado
> Author: Irmin Corona | Compiled from course notes

---

## Table of Contents

1. [What is Git?](#1-what-is-git)
2. [Setup & Configuration](#2-setup--configuration)
3. [Basic Workflow](#3-basic-workflow)
4. [Branches](#4-branches)
5. [Reset vs Revert](#5-reset-vs-revert)
6. [Tags & Checkout](#6-tags--checkout)
7. [Resolving Merge Conflicts](#7-resolving-merge-conflicts)
8. [Git in VS Code](#8-git-in-vs-code)
9. [GitHub Overview](#9-github-overview)
10. [Account Setup & Security](#10-account-setup--security)
11. [Repositories & Collaboration](#11-repositories--collaboration)
12. [SSH Configuration](#12-ssh-configuration)
13. [Forks & Stars](#13-forks--stars)
14. [Pull, Push & Fetch](#14-pull-push--fetch)
15. [Issues & Templates](#15-issues--templates)
16. [Pull Requests](#16-pull-requests)
17. [GitHub Projects](#17-github-projects)
18. [Workflow Automation](#18-workflow-automation)
19. [Profile README](#19-profile-readme-with-markdown)
20. [GitHub Gist](#20-github-gist)
21. [GitHub Pages](#21-github-pages)
22. [GitHub Codespaces](#22-github-codespaces)
23. [Codespaces with Django](#23-codespaces-with-django)
24. [GitHub Actions & CI/CD](#24-github-actions--cicd)
25. [Dependabot & Security](#25-dependabot--security)
26. [GitHub Actions for Profile Activity](#26-github-actions-for-profile-activity)
27. [GitHub CLI](#27-github-cli)
28. [Real Project Setup](#28-real-project-setup)
29. [Branch & PR Management](#29-branch--pr-management)
30. [GitHub Releases](#30-github-releases)
31. [Creating & Publishing PIP Packages](#31-creating--publishing-pip-packages)
32. [GitHub Tips & Tricks](#32-github-tips--tricks)
33. [GitHub Certifications](#33-github-certifications)
34. [Quick Reference Cheat Sheet](#34-quick-reference-cheat-sheet)

---

## 1. What is Git?

- **Creator:** Linus Torvalds (also created Linux kernel)
- **Type:** Distributed version control system (open source)
- **Purpose:** Track file changes, coordinate teamwork, maintain project history
- **Why it matters:** Eliminates the chaos of "final_v2_FINAL.zip" — only stores changes (diffs), not full copies

**Key benefits:**
- Organized code history
- Team collaboration without overwriting each other's work
- Ability to contribute to open source projects

---

## 2. Setup & Configuration

### Verify installation
```bash
git --version
```

### Create a project
```bash
mkdir my-project
cd my-project
```

### Initialize a repository
```bash
git init
```

### Set default branch to `main`
```bash
git config --global init.defaultBranch main
git branch -m main          # rename current branch
```

### Configure identity
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Verify configuration
```bash
git config --list
```

### Get help
```bash
git help
```

> **Tip:** Press the up arrow key to recall and edit the last command.

---

## 3. Basic Workflow

### The three stages

```
Working Directory  →  Staging Area  →  Repository
     (edit)          (git add)       (git commit)
```

### Core commands

| Command | What it does |
|---------|-------------|
| `git status` | Shows file states (untracked, modified, staged) |
| `git add file.txt` | Stage a specific file |
| `git add .` | Stage all changed files |
| `git commit -m "message"` | Save staged changes to history |
| `git log` | View commit history |
| `git rm --cached file.txt` | Unstage a file (remove from staging) |

### Key concepts
- **Staging area:** A "waiting room" where you decide what gets committed
- **Commit message:** Should describe the action clearly (e.g., "Add login validation")
- **`.git` folder:** Hidden folder created by `git init` — stores all version history
- Git tracks **any file type** equally (text, code, images)

---

## 4. Branches

### Why use branches?
- Isolate changes without affecting `main`
- Work on features in parallel
- Discard experiments safely

### Commands

| Command | What it does |
|---------|-------------|
| `git branch` | List branches (* = active) |
| `git checkout -b branch-name` | Create and switch to new branch |
| `git switch main` | Switch to main branch |
| `git checkout main` | Switch to main (alternative) |
| `git merge branch-name` | Merge branch into current branch |
| `git branch -d branch-name` | Delete a merged branch |

### Workflow
1. Create a branch → `git checkout -b feature`
2. Make changes, add, commit
3. Switch to main → `git switch main`
4. Merge → `git merge feature`
5. Delete branch → `git branch -d feature`

> **Best practice:** Always delete branches after merging to keep the repo clean.

---

## 5. Reset vs Revert

### git reset — Rewrite history

Moves the HEAD pointer backward, removing commits.

| Flag | Effect |
|------|--------|
| `--soft` | Removes commit, keeps files staged |
| `--mixed` | Removes commit, unstages files (default) |
| `--hard` | Removes commit AND deletes file changes (**irreversible**) |

```bash
git log                        # find the commit hash
git reset --soft <hash>        # undo commit, keep changes staged
git reset --hard <hash>        # undo everything (dangerous!)
```

### git revert — Safe undo

Creates a **new commit** that reverses a previous one. History is preserved.

```bash
git log                        # find the commit hash
git revert <hash>              # creates a new "undo" commit
```

### When to use which?

| Scenario | Command |
|----------|---------|
| Undo a recent local mistake | `git reset --soft` |
| Clean up messy local history | `git reset` |
| Undo a pushed commit safely | `git revert` |
| Team environment | Always prefer `git revert` |

> **Warning:** Never use `git reset --hard` without coordinating with your team.

---

## 6. Tags & Checkout

### git tag — Mark milestones

```bash
git tag -a v1.0 -m "First stable release"    # create annotated tag
git log                                        # see tag in history
git show v1.0                                  # view tag details
git tag -d v1.0                                # delete a tag
```

### git checkout — Time travel

```bash
git checkout <hash>       # visit a previous commit (read-only exploration)
git checkout main         # return to current state
```

- Exploring old commits does **not** create branches or modify history
- Tags + checkout = organized way to navigate project milestones

---

## 7. Resolving Merge Conflicts

### What causes conflicts?
Two branches modify the **same lines** in the **same file**.

### How to create a conflict (for practice)
1. Create `conflict.txt` on `main`, commit
2. Create branch `developer`, edit `conflict.txt`, commit
3. Switch to `main`, edit same lines in `conflict.txt`, commit
4. Run `git merge developer` → conflict!

### How to resolve
1. Open the conflicted file — look for conflict markers:
```
<<<<<<< HEAD
changes from main
=======
changes from developer
>>>>>>> developer
```
2. Edit the file: keep what you want, remove the markers
3. Stage and commit:
```bash
git add conflict.txt
git commit -m "Resolve merge conflict"
```

> **After resolving:** Delete the merged branch to prevent future conflicts.

---

## 8. Git in VS Code

### Open project
```bash
code .
```

### VS Code Git features
| Feature | How |
|---------|-----|
| See active branch | Bottom-left status bar |
| Switch branches | Click branch name |
| Create branch | "Create New Branch" in source control |
| Stage changes | Click `+` next to changed files |
| Commit | Type message + click checkmark |
| Merge | Branch menu → Merge Branch |
| Resolve conflicts | Visual diff with "Accept Current/Incoming/Both" buttons |
| Initialize repo | Source Control → "Initialize Repository" |

> **Advantage over terminal:** Visual graph of branches and merges with color coding.

---

## 9. GitHub Overview

### What is GitHub?
A cloud platform built on Git for hosting repositories, collaborating, and automating workflows. Acquired by Microsoft in 2018.

### Alternatives
Bitbucket, GitLab, Azure DevOps, AWS CodeCommit, Google Cloud Source

### GitHub pillars
1. **AI** — Copilot, code suggestions
2. **Collaboration** — PRs, code review, issues
3. **Productivity** — Automation, Codespaces
4. **Security** — Secret scanning, Dependabot, CodeQL
5. **Scalability** — Millions of repos and users globally

---

## 10. Account Setup & Security

### Create account
1. Go to github.com → Sign up
2. Enter email, password, username
3. Verify email with code

### Enable Two-Factor Authentication (2FA)
1. Download **GitHub Mobile** app
2. Settings → Password and authentication
3. Scan QR code with authenticator app (e.g., Microsoft Authenticator)
4. Save recovery codes in a safe place

> **Never use SMS** for 2FA — authenticator apps are more secure.

---

## 11. Repositories & Collaboration

### Create a repository
1. Click `+` → New repository
2. Set: name, description, visibility (public/private), add README
3. Click "Create repository"

### Add collaborators
Settings → Collaborators → Add people → Wait for them to accept

### Clone a repository
```bash
git clone https://github.com/user/repo.git    # HTTPS
git clone git@github.com:user/repo.git         # SSH
```

### Professional workflow
1. Create a commit locally
2. Push changes to a **separate branch** (not main)
3. Open a Pull Request
4. Code Review by teammates
5. Merge to main after approval

> **Rule of thumb:** One task, one objective. Keep changes small and focused.

---

## 12. SSH Configuration

### Why SSH over HTTPS?
- No password needed for each operation
- More secure (authenticates the machine itself)

### Generate SSH key
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

### Start SSH agent and add key
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### Add to GitHub
1. Copy public key: `cat ~/.ssh/id_ed25519.pub`
2. GitHub → Settings → SSH and GPG keys → New SSH key → Paste

### Mac extra step
Create `~/.ssh/config`:
```
Host github.com
   AddKeysToAgent yes
   UseKeychain yes
   IdentityFile ~/.ssh/id_ed25519
```
Then: `ssh-add --apple-use-keychain ~/.ssh/id_ed25519`

### Verify connection
```bash
ssh -T git@github.com
```

---

## 13. Forks & Stars

### Fork
- **What:** Copy someone else's repo to your account
- **Why:** Work independently without affecting the original
- **How:** Click "Fork" button on any repo
- **Use case:** Contribute to open source, customize projects

### Stars
- **What:** Bookmark/favorite repos
- **Why:** Quick access to reference material
- **Where:** Your profile → "Your stars"

### Fork workflow
1. Fork the repo on GitHub
2. Clone your fork locally: `git clone <your-fork-url>`
3. Make changes, commit, push
4. (Optional) Open a PR to the original repo

---

## 14. Pull, Push & Fetch

| Command | Direction | Effect |
|---------|-----------|--------|
| `git pull origin main` | Cloud → Local | Downloads AND applies remote changes |
| `git push origin main` | Local → Cloud | Uploads local commits to GitHub |
| `git fetch origin` | Cloud → Local | Downloads changes WITHOUT applying them |

### git fetch workflow (cautious approach)
```bash
git fetch origin                    # download changes
git log main..origin/main           # compare differences
git merge origin/main               # apply after review
```

### When to use which?
- **`git pull`** — You trust the remote changes, need quick update
- **`git fetch`** — You want to review before merging

> Remember: `git pull` = `git fetch` + `git merge`

---

## 15. Issues & Templates

### What are Issues?
A way to report bugs, suggest improvements, or track tasks within a repo.

### Create an Issue
1. Go to repo → Issues tab → New Issue
2. Add title + description
3. (Optional) Assign people, add labels

### Create Issue templates
1. Create folder: `.github/ISSUE_TEMPLATE/`
2. Add markdown files (e.g., `bug_report.md`, `doc_report.md`)
3. Commit and push
4. Templates will appear when users create new issues

### Benefits
- Standardized reporting format
- Faster identification and resolution
- Better team communication

---

## 16. Pull Requests

### Why not push directly to main?
Pushing to `main` can overwrite teammates' unsynced work.

### PR workflow
1. Create a local branch: `git checkout -b developer01`
2. Make changes, commit
3. Push branch: `git push -u origin developer01`
4. On GitHub: Create Pull Request
5. Team reviews code (Code Review)
6. If approved + no conflicts → Merge
7. Delete the branch after merging

### Code Review benefits
- Catches bugs before they reach `main`
- Improves code quality
- Knowledge sharing across the team

---

## 17. GitHub Projects

### What is it?
A Kanban-style project management tool built into GitHub.

### Setup
1. Profile → Projects → New Project
2. Choose template (e.g., Team Planning)
3. Columns: To Do → In Progress → Done

### Features
- Assign tasks to team members
- Set priorities, sizes, and time estimates
- Convert tasks to Issues (links to repo)
- **My Items** tab filters your assigned tasks
- **Insights** tab shows progress charts

> **Tip:** Use issue numbers to name your branches for better traceability.

---

## 18. Workflow Automation

### Link project to repo
Repo → Projects → Link a project

### Auto-update task status
1. Project settings → Workflows
2. Configure rules:
   - Issue/PR closed → Task moves to "Done"
   - PR approved → Task moves to "In Progress"

### Auto-close Issues from PRs
In your PR description, write:
```
closes #3
```
When the PR is merged, Issue #3 closes automatically.

### Extended integrations
Workflows can trigger notifications via Slack, Teams, or email.

---

## 19. Profile README with Markdown

### Create the special repo
1. Create a repo with **your exact username** as the name
2. Make it public, add a README.md
3. This README becomes your GitHub profile page

### Markdown syntax

| Element | Syntax |
|---------|--------|
| Heading 1 | `# Title` |
| Heading 2 | `## Subtitle` |
| Bold | `**text**` |
| Link | `[text](url)` |
| Image | `![alt](url)` |
| Emoji | `:computer:` `:pencil:` |
| Badge | Get from [shields.io](https://shields.io) |

### Useful badges
- Website status
- Commit activity (weekly/monthly/yearly)
- Tech stack icons
- Social links

### Tips
- View other profiles' README in "Raw" mode for Markdown ideas
- Use **Markdown Lint** extension in VS Code for clean formatting
- Preview with VS Code's "Open Preview" (top-right icon)

---

## 20. GitHub Gist

### What is it?
A lightweight way to share code snippets without creating a full repo.

### Use cases
- Get quick feedback on a code snippet
- Pair programming discussions
- Personal snippet library
- Test ideas before integrating them

### How to use
1. Go to [gist.github.com](https://gist.github.com)
2. Paste code, add description
3. Share the URL
4. Collaborators can comment directly

---

## 21. GitHub Pages

### What is it?
Free static website hosting directly from a GitHub repo.

### Setup
1. Create repo named `username.github.io`
2. Make it public, add README
3. Clone locally, add HTML/CSS files
4. Put site files in a `docs/` folder (or root)
5. Push to GitHub

### Enable Pages
1. Repo → Settings → Pages
2. Select branch (`main`) and folder (`/docs` or `/root`)
3. GitHub provides your live URL

### Features
- Custom domain support (e.g., `yourname.com`)
- Changes reflect almost immediately after push
- Perfect for portfolios, project docs, landing pages

---

## 22. GitHub Codespaces

### What is it?
Cloud-based development environments (like VS Code in your browser).

### Create a Codespace
1. Click "New Codespace"
2. Select repo, branch, region
3. Choose machine type (cores/RAM)

### Features
- File explorer, terminal, extensions — same as VS Code
- Pre-installed languages: Node, Python, .NET, etc.
- Full git integration (commit & sync from browser)

### Important limits
- **Free tier:** 120 hours/month
- **Always delete** Codespaces when done to avoid charges

### Workflow
1. Create Codespace → make changes → commit → sync
2. Delete Codespace when task is complete

---

## 23. Codespaces with Django

### Quick start with templates
1. Go to `github.com/codespaces/templates`
2. Select Django template
3. Codespace opens with Django pre-configured

### Run the project
```bash
python manage.py runserver
```
Codespaces auto-detects the port and provides a preview URL.

### DevContainer configuration
- `.devcontainer/devcontainer.json` — defines the environment
- Customize: extensions, ports, post-create commands
- Changes require rebuilding the container

### Port management
- **Private:** Only you can access (default)
- **Public:** Anyone with the URL can access
- Configure in the Ports tab

---

## 24. GitHub Actions & CI/CD

### What is it?
Automation platform for building, testing, and deploying code.

### Key concepts
| Term | Meaning |
|------|---------|
| **Workflow** | Automated process defined in YAML |
| **Event/Trigger** | What starts the workflow (push, PR, schedule) |
| **Job** | A set of steps that run on a runner |
| **Step** | Individual task within a job |
| **Runner** | Virtual machine that executes the job |

### Workflow file location
```
.github/workflows/main.yml
```

### Example: CI on every push
```yaml
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: echo "Running tests..."
```

### Common triggers
- `push` — on every push
- `pull_request` — when PR is created/updated
- `schedule` — cron-based (e.g., daily)
- `workflow_dispatch` — manual trigger

### GitHub Actions Marketplace
Browse pre-built actions at [github.com/marketplace](https://github.com/marketplace?type=actions)

---

## 25. Dependabot & Security

### Dependabot
- Automatically scans dependencies for vulnerabilities
- Creates PRs to update outdated/vulnerable packages
- Configure in `.github/dependabot.yml`

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
```

### CodeQL
- Static analysis tool that scans code for security vulnerabilities
- Supports: JavaScript, Python, Java, C++, Go, Ruby, C#, Swift
- Configure via GitHub Actions workflow

### Security best practices
- Enable Dependabot alerts in repo settings
- Use CodeQL for code scanning
- Never commit secrets (API keys, passwords)
- Use `.gitignore` to exclude sensitive files

---

## 26. GitHub Actions for Profile Activity

### What is it?
A GitHub Action that automatically updates your profile README with your recent activity.

### Setup
1. Edit your profile repo's `README.md` — add placeholder comments:
```markdown
### Recent Activity
<!-- START_SECTION:activity -->
<!-- END_SECTION:activity -->
```

2. Create workflow file: `.github/workflows/update-readme.yml`
```yaml
name: Update README
on:
  schedule:
    - cron: '*/12 * * * *'   # every 12 hours
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: jamesgeorge007/github-activity-readme@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

3. Commit and push — the action runs on schedule

### Customization ideas
- YouTube video lists
- Blog post feeds
- Portfolio links
- Use `GITHUB_TOKEN` for authentication

### Explore more
- GitHub Actions Marketplace has many profile-related actions
- Download YAML files from inspiring profiles and adapt them

---

## 27. GitHub CLI

### What is it?
A command-line tool to interact with GitHub features (PRs, issues, repos) directly from the terminal.

### Install & verify
```bash
# Install varies by OS — see cli.github.com
gh --version
```

### Authenticate
```bash
gh auth login
# Choose: github.com → SSH → browser authentication
```

### Create a repository
```bash
gh repo create my-repo --public --description "My repo" --clone
```

### Manage Issues
```bash
gh issue create                   # create new issue
gh issue status                   # see assigned/open/mentioned issues
gh issue list                     # list all issues
```

### Manage Pull Requests
```bash
gh pr create                      # create PR
gh pr list                        # list PRs
gh pr view 123                    # view specific PR
gh pr merge 123                   # merge PR
```

### Why use it?
- Faster than switching to browser
- Scriptable — automate repetitive tasks
- Full access to GitHub features from terminal

---

## 28. Real Project Setup

### Project structure best practices
1. Create repo on GitHub with README
2. Clone locally
3. Add `.github/` folder for templates and workflows
4. Create `pull-request-template.md` for standardized PRs

```bash
mkdir .github
touch .github/pull-request-template.md
```

### Branch workflow
```bash
git checkout -b feature-branch    # never work on main
git add .
git commit -m "Implement new API"
git push -u origin feature-branch
```

### Example: Python API project
```bash
mkdir api
# Create app.py and requirements.txt
pip3 install -r requirements.txt
uvicorn app:app --reload          # run the API
```

### Golden rules
- **Never work directly on `main`**
- Use PR templates for consistent reviews
- One task = one branch = one PR
- Encourage code review from teammates

---

## 29. Branch & PR Management

### Daily workflow
1. Review active PRs each morning
2. Check diffs: green = additions, red = deletions
3. Approve or request changes
4. Merge and delete branch when done

### Before pushing your branch
```bash
git checkout main
git pull origin main              # get latest changes
git checkout my-branch
git merge main                    # bring main into your branch
# Resolve any conflicts
git push origin my-branch
```

### Handling conflicts during PR
1. VS Code shows conflict markers with color coding
2. Choose: Accept Current | Accept Incoming | Accept Both
3. Commit the resolution
4. Push and update the PR

### Best practice cycle
1. Pull latest `main` → create branch → work → commit
2. Pull `main` again → merge into your branch → resolve conflicts
3. Push → create PR → code review → merge → delete branch

---

## 30. GitHub Releases

### What are Releases?
Packaged versions of your software, tagged and downloadable from GitHub.

### Examples in the wild
- **Express.js** (npm) — 24M weekly downloads, multiple releases
- **Flask** (Python) — 14 releases, clear version history

### Create a Release
1. Go to repo → Releases → "Create a new release"
2. Choose a tag (e.g., `v0.1.0`) and target branch (`main`)
3. Add title and description
4. Upload binaries (`.tar.gz`, `.whl` files from `dist/`)
5. Click "Publish release"

### Python package setup (`setup.py`)
```python
from setuptools import setup, find_packages

setup(
    name='my_package',
    version='0.1',
    packages=find_packages(),
    author='Your Name',
    author_email='you@example.com',
    url='https://github.com/user/repo',
)
```

### Build the package
```bash
python3 setup.py bdist_wheel sdist
```
This creates `.whl` and `.tar.gz` files in `dist/`.

---

## 31. Creating & Publishing PIP Packages

### Install from GitHub Release
```bash
pip3 install <URL_of_whl_file>
pip3 list                         # verify installation
```

### Use in a project
```python
# app.py
from my_package import saludar
print(saludar("World"))
```

```bash
python3 app.py
```

### Extend to other ecosystems
| Ecosystem | Package Manager | Language |
|-----------|----------------|----------|
| PyPI | pip | Python |
| npm | npm | Node.js |
| NuGet | dotnet | .NET/C# |

### Key points
- Keep package name consistent with repo name
- `__init__.py` contains your package's main code
- Check `.gitignore` doesn't exclude critical build files

---

## 32. GitHub Tips & Tricks

### Quick editor
Press `.` (period) on any repo page to open the web editor instantly.

### Tree view
Click the menu icon (top-left) to browse folders in tree structure.

### Commit history
Click the commit counter to see full history with file-level diffs.

### PR as plain text
Add `.patch` or `.diff` to any PR URL to get plain text output — useful for sharing via email/chat.

### Link to specific lines
Click a line number → "Copy permalink" — shares a direct link to that exact line.

### Mention collaborators
Use `@username` in comments to notify specific people.

### Keyboard buttons in Markdown
```markdown
Press <kbd>W</kbd> to go up, <kbd>S</kbd> to go down
```
Renders as: Press <kbd>W</kbd> to go up, <kbd>S</kbd> to go down

### ProTips
Look for "ProTip" hints at the bottom of GitHub pages — they offer context-specific shortcuts.

---

## 33. GitHub Certifications

### GitHub Foundations Certification
- Validates core GitHub knowledge
- Covers: repos, branches, commits, PRs, Actions, Pages, security
- Good first step for professional development

### Preparation tips
- Practice with real repos
- Understand the full PR workflow
- Know the difference between Git commands
- Familiarize yourself with GitHub settings and features

---

## 34. Quick Reference Cheat Sheet

### Setup
```bash
git init                          # initialize repo
git config --global user.name ""  # set name
git config --global user.email "" # set email
git clone <url>                   # clone remote repo
```

### Daily workflow
```bash
git status                        # check file states
git add .                         # stage all changes
git commit -m "message"           # commit
git push origin main              # push to remote
git pull origin main              # pull from remote
```

### Branches
```bash
git branch                        # list branches
git checkout -b name              # create + switch
git switch main                   # switch to main
git merge name                    # merge branch
git branch -d name                # delete branch
```

### History & undo
```bash
git log                           # view history
git log --oneline                 # compact history
git reset --soft HEAD~1           # undo last commit, keep staged
git revert <hash>                 # safe undo (new commit)
```

### Tags
```bash
git tag -a v1.0 -m "message"     # create tag
git show v1.0                     # view tag details
git tag -d v1.0                   # delete tag
```

### Remote
```bash
git remote -v                     # show remote URLs
git fetch origin                  # download without merging
git push -u origin branch-name   # push new branch
```

### SSH
```bash
ssh-keygen -t ed25519 -C "email" # generate key
ssh -T git@github.com            # test connection
```

### GitHub CLI
```bash
gh auth login                     # authenticate
gh repo create name --public      # create repo
gh issue create                   # create issue
gh issue status                   # check issues
gh pr create                      # create pull request
gh pr list                        # list pull requests
gh pr merge 123                   # merge PR #123
```

---

*Built as a personal knowledge base from course materials. Last updated: March 2026.*
