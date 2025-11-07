# CS 2490 -- Session 34 -- Git and GitHub: A Beginner's Guide

## Why Are We Here?

Your homework requires you to:
1. Get code from a GitHub repository
2. Complete the skeleton program
3. Submit your work via a **Pull Request**

To do this, you need to understand **Git** and **GitHub**.

---

## Part 1: Git - The Version Control Tool

### What Problem Does Git Solve?

Git is a **version control system**: it helps you manage changes to your project (usually code) over time.

**Without version control:**
- `project_final.py`
- `project_final_v2.py`
- `project_final_ACTUALLY_FINAL.py`
- `project_final_ACTUALLY_FINAL_fixed.py`

Sound familiar? 😅

**With Git:**
- One file: `project.py`
- Full history of all changes
- Can go back to any previous version
- Can see what changed and when

Git works by remembering what lines of code you added/changed/deleted in between to consecutive versions. It packs these changes up in _patches_ that can be applied or reversed as needed.

Git runs **locally** on your computer. You can use it for collaborative works as well as solo projects! I wrote my entire PhD thesis using Git for easy rollback and versioning.

In particular: **Git is NOT GitHub!**

### Sidenote

Git has been created by Linus Torvalds, the creator of Linux, in 2005 as a tool to manage the development of the Linux kernel. It wasn't meant to take over the world at first, but it was so simple, efficient, and powerful that it quickly became the de facto standard for version control in software development. It obliterated older systems like CVS, SVN, or Mercurial.

### Installing Git

#### Do You Have Git Already?

Open a terminal/command prompt and type:
```bash
git --version
```

If you see a version number (e.g., `git version 2.45.0`), you're all set! Skip to the next section.

#### Installing Git on Windows

**Git for Windows (Recommended)**
1. Go to https://git-scm.com/download/windows
2. Download the installer
3. Run the installer - **use default settings** (just keep clicking "Next")
4. Important: Make sure "Git Bash" is included

#### Installing Git on macOS

**Option 1: Homebrew** (if you have it)
```bash
brew install git
```

**Option 2: Xcode Command Line Tools**
```bash
xcode-select --install
```
- Follow the prompts to install

**Option 3: Official Installer**
- Download from https://git-scm.com/download/mac

#### Installing Git on Linux

You probably already have Git installed. If not, use your package manager, you know what you're doing!

#### Verify Installation

After installing, open a **new** terminal and run:
```bash
git --version
```

You should see the Git version number.

#### First-Time Git Configuration

Tell Git who you are (needed for commits):
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Your email address is only used as an identifier for your _commits_.

### The Git Workflow (local usage)

Git has three main areas:

1. **Working Directory** - Your actual files where you edit code
2. **Staging Area** - Changes you've selected to save
3. **Repository** - Permanent history of saved changes

**Visual representation:**

```
┌─────────────────┐
│  Working        │  ← Edit your files here
│  Directory      │
└────────┬────────┘
         │ git add
         ↓
┌─────────────────┐
│  Staging Area   │  ← Select what to save
│  (Index)        │
└────────┬────────┘
         │ git commit
         ↓
┌─────────────────┐
│  Repository     │  ← Permanent history
│  (.git folder)  │
└─────────────────┘
```

**The basic cycle:**
```
Edit files → Add to staging → Commit to repository
```

When you commit, your changes are _saved_. It is effectively a snapshot of your project at that moment in time, and you can rollback to it later if needed. You should commit as soon as you have made a substantial change to your project (e.g., fixed an issue, added a new feature). Big collaborative projects have dozen and dozens of commits a day!

**Example workflow:**
```
1. Edit main.py
   Working Dir: [main.py*]  Staging: []  Repo: [old version]

2. git add main.py
   Working Dir: [main.py*]  Staging: [main.py*]  Repo: [old version]

3. git commit -m "Fix bug"
   Working Dir: [main.py]  Staging: []  Repo: [new version]
```

#### Key Commands (local):
```bash
git status              # What's changed?
git add <file>          # Stage a file
git add .               # Stage all changes
git commit -m "msg"     # Save staged changes with a message
git log                 # View history
```

### Branches

- A **branch** is a separate line of development
- Default branch is usually called `main` (or `master`)
- Think of branches as parallel versions of your code
- You can create, switch between, and merge branches

**Visualizing branches:**

```
Initial state - one branch (main) with several commits:

    A --- B --- C  (main)
```

**Creating a new branch:**

```
git checkout -b hotfix-issue-1267

    A --- B --- C  (main)
                 \
                  D  (hotfix-issue-1267)
```

**Both branches evolve independently:**

```
    A --- B --- C --- E --- F  (main)
                 \
                  D --- G --- H  (hotfix-issue-1267)
```

**Merging branches back together:**

```
git checkout main
git merge hotfix-issue-1267

    A --- B --- C --- E --- F --- M  (main)
                 \              /
                  D --- G --- H   (hotfix-issue-1267 merged into main)
```

#### Basic Branch Commands:
```bash
git branch              # List branches
git checkout -b <name>  # Create and switch to new branch
git checkout main       # Switch back to main
git merge <branch>      # Merge branch into current branch
```

*For this homework, you'll mostly work on the `main` branch.*

---

## Part 2: Introducing Remotes

### Git Can Sync with Other Repositories

So far, everything is on **your computer**. But Git can synchronize with **remote** repositories:

- A remote is just another copy of the repository
- Could be on another computer, a server, anywhere
- This enables collaboration!

### Key Remote Operations:

**Visual representation:**

```
When your local repo is ahead of the remote:

Your Computer                           Remote (e.g., GitHub)
┌─────────────────┐                    ┌─────────────────┐
│  Local Repo     │                    │  Remote Repo    │
│  A---B---C      │                    │  A---B          │
└─────────────────┘                    └─────────────────┘
         │                                     ↑
         └──────────── git push ───────────────┘
                      (send commits C)
```

```
When the remote repo is ahead of your local:

Your Computer                           Remote (e.g., GitHub)
┌─────────────────┐                    ┌─────────────────┐
│  Local Repo     │                    │  Remote Repo    │
│  A---B          │                    │  A---B---C---D  │
└─────────────────┘                    └─────────────────┘
         ↑                                     │
         └──────────── git pull ───────────────┘
                   (fetch + merge C and D)
```

**Three key operations:**

- **`push`** - Send your commits to a remote
- **`fetch`** - Download commits from a remote (doesn't merge them)
- **`pull`** - Fetch + merge in one step (most common)

Git pull is just a shorthand for `git fetch` followed by `git merge`. Notice that it is the same `merge` command as for local branches: when you fetch changes from a remote, Git creates a special local branch named `[name of remote]/[name of branch]` that is _in sync_ with the remote branch.

### Example

You have been working on fixing an issue and your project looks like this locally:

```
    A --- B --- C --- E --- F  (main)
                 \
                  D --- G --- H  (hotfix-issue-1267)
```

You'd like to merge your changes back into `main` but the remote `main` branch might have changed since your started your fix. Let's say the remote is called `origin`:

```bash
git checkout main      # Switch to main branch
git fetch origin       # Download without merging
git merge origin/main  # Merge fetched changes
```

You could have just done also `git pull origin main`. You now have all the remote commits in your local main:

```
    A --- B --- C --- E --- F --- G' --- H' (main)
                 \              
                  D --- G --- H   (hotfix-issue-1267 merged into main)
```

You can merge:

```bash
git merge hotfix-issue-1267
```

The local repo now looks like this:

```
    A --- B --- C --- E --- F --- G' --- H' --- M  (main)
                 \                             /
                  D ----------- G ----------- H   (hotfix-issue-1267 merged into main)
```

You can now push your updated `main` branch back to the remote:

```bash
git push origin main
```

The remote repo now looks like this:

```
    A --- B --- C --- E --- F --- G' --- H' --- M  (main)
```


### A few clarifications:

- Git doesn't care _where_ the remote is. It could be GitHub, GitLab, another computer, or a USB drive! It is just another version of the repository that **you** consider as being your "source of truth".
- You can have multiple remotes (e.g., `origin`, `upstream`, etc.) to sync with different repositories.
- Git keeps track of changes, but you have full control. If you decide to blank a file out completely and commit and push, Git won't stop you! Now everybody pulling from that remote will get the blanked file. (Fortunately, they can always rollback to a previous version if needed.)

---

## Part 3: GitHub - One Type of Remote

### What is GitHub?

- **GitHub is NOT Git!**
- GitHub is a **web hosting service** for Git repositories
- Just one of many options (GitLab, Bitbucket, Bitrise, self-hosted servers, etc.)
- We use it as a convenient "central" meeting place

### What Does GitHub Add?

- Web interface to browse repositories
- **Pull Requests** - propose changes to someone else's repo
- **Issues** - track bugs and features
- **Forks** - your personal copy of someone else's repository
- Access control and collaboration tools

### Why Do We Use GitHub as "Central"?

- Everyone can access it
- One "source of truth" for the assignment
- Easy submission via Pull Requests
- Industry standard for open-source projects

But remember: **Git works fine without GitHub!**

---

## Part 4: Your Homework Workflow

### The Big Picture

```
┌─────────────────────────────────────────────────────────────┐
│                         GITHUB                              │
│                                                             │
│  ┌──────────────────────┐         ┌──────────────────────┐ │
│  │ Original Repository  │ ← FORK →│   Your Fork          │ │
│  │ (Instructor's)       │         │   (YourUsername)     │ │
│  │  A---B---C           │         │   A---B---C          │ │
│  └──────────────────────┘         └──────┬───────────────┘ │
│              ↑                            │                 │
│              │                            │                 │
│              │                            ↓                 │
│         PULL REQUEST              git clone (download)     │
│         (you propose changes)             │                 │
│              │                            │                 │
│              │                            ↓                 │
└──────────────┼────────────────────────────┼─────────────────┘
               │                            │
               │                            ↓
               │                  ┌──────────────────────┐
               │                  │  Your Local Repo     │
               │                  │  (Your Computer)     │
               │                  │  A---B---C           │
               │                  │         ↓            │
               │                  │      [edit files]    │
               │                  │         ↓            │
               │                  │  A---B---C---D       │
               │                  └──────────┬───────────┘
               │                             │
               │                             │ git push
               │                             │ (upload)
               │                             ↓
               │                  ┌──────────────────────┐
               │                  │   Your Fork          │
               └──────────────────│   A---B---C---D      │
                                  │   (Updated!)         │
                                  └──────────────────────┘
```

**The workflow in words:**
1. **Fork** - Create your copy on GitHub
2. **Clone** - Download to your computer
3. **Edit** - Make changes locally
4. **Commit** - Save changes locally
5. **Push** - Upload to your fork on GitHub
6. **Pull Request** - Ask instructor to merge your changes

### Step-by-Step Instructions

#### 1. Fork the Repository (GitHub)
- Go to the assignment repository on GitHub
- Click the **"Fork"** button (top-right)
- This creates YOUR copy of the repo on GitHub

#### 2. Clone Your Fork (Git)
```bash
git clone https://github.com/YOUR_USERNAME/assignment-repo.git
cd assignment-repo
```
- Downloads the repository to your computer
- Sets up the remote connection automatically

#### 3. Make Your Changes (Local)
- Edit the skeleton code files
- Test your work!
- Save your files

#### 4. Stage and Commit (Git)
```bash
git status                           # See what changed
git add .                            # Stage all changes
git commit -m "Complete assignment"  # Commit with message
```

**Commit message tips:**
- Be descriptive but concise
- "Complete homework 3" is good
- "asdf" is bad

#### 5. Push to GitHub (Git + GitHub)
```bash
git push origin main
```
- Sends your commits from local → your fork on GitHub
- Check GitHub - you should see your changes!

#### 6. Create a Pull Request (GitHub)
- Go to the **original** repository (instructor's repo)
- Click **"Pull requests"** → **"New pull request"**
- Click **"compare across forks"**
- Select your fork as the source
- Add a description
- Click **"Create pull request"**
- Done! 🎉

---

## Essential Commands Cheatsheet

### You NEED These:
```bash
# Setup (once)
git clone <url>                      # Download a repository

# Daily workflow
git status                           # Check what's changed
git add .                            # Stage all changes
git commit -m "Description"          # Save changes
git push origin main                 # Upload to GitHub

# When starting work (good habit)
git pull origin main                 # Get latest changes
```

### Helpful Commands:
```bash
git log                              # View commit history
git diff                             # See what changed (unstaged)
git remote -v                        # See remote connections
```

---

## Live Demo

I'll now demonstrate:

1. **Cloning this presentation repository**
2. **Making edits** to the markdown file
3. **Adding and committing** changes
4. **Pushing** to GitHub
5. **Bonus:** Simulating fetch/merge with two local clones

Watch how Git and GitHub work together!

---

## Quick Troubleshooting

### "Git doesn't see my changes"
→ Did you save the file? Check `git status`

### "Nothing to commit"
→ You haven't made changes, or forgot to `git add`

### "Push rejected / failed"
→ Someone else pushed first. Run `git pull origin main` first

### "I don't know what I changed"
→ Run `git diff` to see differences

### "Help! I broke everything!"
→ Don't panic! Ask for help. Git rarely loses data.

---

## Key Takeaways

1. **Git** ≠ **GitHub**
   - Git: version control tool (local)
   - GitHub: remote hosting service (one option among many)

2. **Git workflow:** Edit → Add → Commit → Push

3. **Homework workflow:** Fork → Clone → Edit → Commit → Push → Pull Request

4. **Git is your safety net** - commit often!

5. **When in doubt:** `git status` is your friend

---

## Resources

- **Git Documentation:** https://git-scm.com/doc
- **GitHub Guides:** https://guides.github.com/
- **Interactive Tutorial:** https://learngitbranching.js.org/
- **Git Cheat Sheet:** https://education.github.com/git-cheat-sheet-education.pdf

---

## Questions?

Remember: The best way to learn Git is to use it. Don't be afraid to experiment (that's what version control is for)!
