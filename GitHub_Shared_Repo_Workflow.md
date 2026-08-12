
# 📘 GitHub Shared Repository Workflow: Folder Level Access Control for Student Assignments

## 0. Purpose of this Note

This note captures the complete process of setting up a single shared GitHub repository where multiple students can each work only inside their own folder, without being able to touch anyone else's work, enforced automatically rather than by trust alone.

Two audiences are covered here:

- **Part A** is written for myself (or any instructor) setting up such a repository from scratch.
- **Part B** is written for the students, as a complete worked example of the full push and pull request cycle for one assignment.

> [!tip] Why this matters
> Once this pattern is understood, it can be reused for any future course repository (lab reports, mini projects, semester assignments) with any number of students, just by changing the roll number list and folder names.

---

## 1. Prerequisites

- Git installed locally (`git --version` to confirm)
- A GitHub account for the instructor (repository owner)
- A GitHub account for each student
- Basic terminal familiarity (bash, on Manjaro Linux in this case)
- A GitHub fine grained Personal Access Token, since password authentication for git operations has been disabled by GitHub for several years now

---

## Part A — Setting Up the Shared Repository (Instructor Side)

### A.1 Repository Layout

The target structure looked like this:

```
AOCEE581_681_B2024-28/
├── .github/
│   └── workflows/
│       └── check-student-folder.yml
├── students-map.csv
├── students/
│   ├── 120016244019/
│   ├── 120016244040/
│   ├── 120016244041/
│   ├── 120016244056/
│   ├── 120016244062/
│   ├── 120016244064/
│   └── 120016244065/
└── README.md
```

The idea: every student gets exactly one folder under `students/`, named after their roll number. They may only ever modify files inside their own folder.

### A.2 Clone the Repository — First Commands and What They Mean

```bash
cd ~/Documents
git clone https://github.com/KingsukMajumdar/AOCEE581_681_B2024-28.git
cd AOCEE581_681_B2024-28
```

| Command | Meaning |
|---|---|
| `git clone <url>` | Downloads a full copy of the remote repository (all history, all branches) onto the local machine, and automatically sets up a connection called `origin` pointing back to that remote URL |
| `cd <folder>` | Standard shell command, moves the terminal's working directory inside the newly cloned repository |

At this point, the local folder is a fully functional git repository, linked to GitHub, ready to receive changes.

### A.3 Add the CI Workflow File and the Roll Number Map

```bash
mkdir -p .github/workflows
nano .github/workflows/check-student-folder.yml
nano students-map.csv
```

| Command | Meaning |
|---|---|
| `mkdir -p <path>` | Creates a folder, and any missing parent folders along the way, without error if it already exists |
| `nano <file>` | Opens a simple terminal text editor to create or edit that file |

**`students-map.csv`** links each roll number to the student's actual GitHub username, so the automated check knows which folder belongs to which PR author:

```csv
120016244019,chanchal9641
120016244040,riktamondal125-ops
120016244041,Rupakrc9776
120016244056,Subha-56
120016244062,surjendu02
120016244064,Tanbir005
120016244065,Tirtha212
```

**`check-student-folder.yml`** is a GitHub Actions workflow. In plain terms: every time someone opens a pull request against `main`, GitHub spins up a temporary Ubuntu machine, checks out the code, compares the list of changed files against the PR author's allowed folder (looked up from `students-map.csv`), and fails the check if even one file outside that folder was touched.

> [!note] Why a CI check instead of just trusting students
> GitHub does not offer a native "this collaborator may only write to this one folder" permission. Repository access in GitHub is granted at the whole-repository level, not per path. A CI check plus a rule that blocks merging until the check passes is the standard way to simulate folder level write control.

### A.4 Create the Student Folders

```bash
mkdir -p students/120016244019 students/120016244040 students/120016244041 \
         students/120016244056 students/120016244062 students/120016244064 \
         students/120016244065

for d in students/*/; do
  echo "# Student folder: $(basename "$d")" > "${d}README.md"
done
```

Git does not track empty folders, so each one needs at least one file inside it (here, a small `README.md`) to actually exist once pushed.

### A.5 Stage, Commit and Push — Command by Command

```bash
git add .github/ students-map.csv students/
git status
git commit -m "Add student folder scaffold, CI folder-scope check, and student-to-GitHub mapping"
git push origin main
```

| Command | Meaning |
|---|---|
| `git add <paths>` | Moves the specified files from "untracked/modified" into the "staging area", marking them as ready to be included in the next commit |
| `git status` | Shows the current state: what is staged (green), what is modified but not staged (red), what is untracked |
| `git commit -m "message"` | Takes everything currently staged and permanently records it as a new snapshot in the local repository's history, tagged with the given message |
| `git push origin main` | Uploads local commits on the `main` branch to the remote called `origin` (GitHub), so they become visible to everyone else |

> [!warning] Common mistake made during this setup
> Files were created but `git add` was skipped, so `git commit` had nothing to commit. Always run `git status` before committing to confirm files are actually staged.

### A.6 Authentication: Personal Access Token (PAT)

GitHub no longer accepts a normal account password for `git push` or `git clone` over HTTPS. A fine grained Personal Access Token must be generated instead.

**Steps:**

1. `https://github.com/settings/personal-access-tokens/new`
2. Resource owner: your own account
3. Repository access: only the specific repository needed
4. Permissions required for this workflow:
   - **Contents**: Read and write
   - **Workflows**: Read and write (mandatory if the push includes anything inside `.github/workflows/`)
   - **Metadata**: Read only (usually auto selected)
5. Generate, then copy the token immediately, GitHub shows it only once

**Using the token, two ways:**

```bash
# Option 1: embed directly in the remote URL (quick, but token sits in plain text in .git/config)
git remote set-url origin https://USERNAME:TOKEN@github.com/USERNAME/REPO.git

# Option 2: let git prompt for it at push time (safer, nothing stored on disk unless a credential helper is configured)
git push origin main
# Username: <github username>
# Password: <paste the token here, not the account password>
```

**Verifying a token works, independent of git:**

```bash
curl -H "Authorization: Bearer YOUR_TOKEN_HERE" https://api.github.com/user
```

A working token returns your GitHub profile as JSON. A bad or incomplete token returns `{"message": "Bad credentials", ...}`, which immediately tells you the problem is the token itself, not the git configuration.

> [!tip] Remembering the token so it is not typed every time
> ```bash
> git config --global credential.helper store
> ```
> This saves credentials in `~/.git-credentials` after the first successful push. Simple, though the token is stored unencrypted, fine for a personal machine.

### A.7 Trigger the Workflow Once

A GitHub Actions check only becomes selectable in branch protection settings after it has run at least once. So a small throwaway pull request is used purely to make GitHub register the check.

```bash
git checkout -b test-workflow-trigger
echo "test" >> README.md
git add README.md
git commit -m "Trigger workflow for status check registration"
git push origin test-workflow-trigger
```

| Command | Meaning |
|---|---|
| `git checkout -b <name>` | Creates a new branch starting from the current commit, and switches to it immediately |

Then, on GitHub: open a pull request from `test-workflow-trigger` into `main`, let the check run once (it is expected to fail here, since the instructor's own username is not in `students-map.csv`), then close the PR without merging.

**Cleanup afterward:**

```bash
git checkout main
git pull origin main
git branch -D test-workflow-trigger
git push origin --delete test-workflow-trigger
```

| Command | Meaning |
|---|---|
| `git checkout main` | Switches the working directory back to the `main` branch |
| `git branch -D <name>` | Force deletes a local branch |
| `git push origin --delete <name>` | Deletes that branch on the remote (GitHub) as well |

### A.8 Lock Down `main` with a Ruleset

On GitHub: **Settings → Rulesets → New ruleset → New branch ruleset**

Key settings applied:

| Setting | Value | Why |
|---|---|---|
| Enforcement status | Active | A ruleset in "Disabled" state does nothing |
| Target branches | `main` | Only this branch is protected |
| Require a pull request before merging | ✅ | Forces every change through review, no direct pushes |
| Require status checks to pass | ✅, with `folder-scope-check` selected | This is the actual enforcement mechanism, a PR cannot merge unless the folder check passes |
| Require branches to be up to date before merging | ✅ (recommended) | Ensures the PR is tested against the latest `main` |
| Block force pushes | ✅ | Prevents history rewriting on the protected branch |
| Restrict deletions | ✅ | Prevents `main` itself from being deleted |

### A.9 Give Yourself a Bypass

With an empty bypass list, even the repository owner is blocked by the ruleset, which would prevent quick direct fixes to `main`.

**Fix:** In the same ruleset, under **Bypass list**, click **Add bypass**, select role **Repository admin**, then **Create**. This allows the owner (admin role) to push directly or merge even if a check fails, while students, who only have Write access or work from forks, still must go through the full PR and check cycle.

### A.10 Final State

At this point the repository is fully wired:

- ✅ Folder scaffold and mapping file live on `main`
- ✅ CI check registered and required
- ✅ Direct pushes to `main` blocked for everyone except the admin
- ✅ Test branch cleaned up

---

## Part B — Complete Worked Example for a Student

### B.1 Scenario

Student **Chanchal Bhattacharjee**, roll number `120016244019`, GitHub username `chanchal9641`, needs to submit the Week 1 assignment (say, a Python script and a short PDF report) into the shared repository, inside their own folder only.

### B.2 Fork the Repository

A fork is a personal copy of the repository under the student's own GitHub account. This is the recommended route since it needs no collaborator invite from the instructor.

**On GitHub (in browser):**

1. Visit `https://github.com/KingsukMajumdar/AOCEE581_681_B2024-28`
2. Click **Fork** (top right)
3. Confirm, this creates `https://github.com/chanchal9641/AOCEE581_681_B2024-28`

### B.3 Clone the Fork Locally

```bash
git clone https://github.com/chanchal9641/AOCEE581_681_B2024-28.git
cd AOCEE581_681_B2024-28
```

This downloads Chanchal's own fork, not the instructor's original, onto their machine.

**Add the original repository as a second remote, called `upstream`, so future updates from the instructor can be pulled in:**

```bash
git remote add upstream https://github.com/KingsukMajumdar/AOCEE581_681_B2024-28.git
git remote -v
```

`git remote -v` should now show two remotes: `origin` (Chanchal's fork) and `upstream` (the instructor's original).

### B.4 Add Week 1 Assignment Files

**Best practice: create a dedicated branch for this piece of work, rather than committing straight on `main`:**

```bash
git checkout -b week1-assignment
```

**Then place the actual files inside the correct folder only:**

```
students/120016244019/
├── week1_assignment.py
└── week1_report.pdf
```

For example, if the files already exist elsewhere on the machine:

```bash
cp ~/Downloads/week1_assignment.py students/120016244019/
cp ~/Downloads/week1_report.pdf students/120016244019/
```

> [!warning] The one rule that matters most
> Do not touch, rename, or delete anything outside `students/120016244019/`. Not `students-map.csv`, not another student's folder, not the workflow file. The automated check exists precisely to catch this, and will fail the pull request if violated.

### B.5 Commit and Push to the Fork

```bash
git add students/120016244019/
git status
git commit -m "Add Week 1 assignment: exponential model fitting script and report"
git push origin week1-assignment
```

This uploads the new branch, with only the Week 1 files, to Chanchal's own fork (`origin`), not yet to the instructor's repository.

### B.6 Open the Pull Request

**On GitHub:**

1. Visit the fork, GitHub will show a banner: *"week1-assignment had recent pushes"* with a **Compare & pull request** button
2. Confirm: **base repository**: `KingsukMajumdar/AOCEE581_681_B2024-28`, **base**: `main` ← **head repository**: `chanchal9641/AOCEE581_681_B2024-28`, **compare**: `week1-assignment`
3. Add a clear title, e.g. *"120016244019 — Week 1 Assignment Submission"*
4. Click **Create pull request**

### B.7 What Happens Automatically

Within moments, `folder-scope-check` runs:

- It reads the PR author's GitHub username (`chanchal9641`)
- Looks it up in `students-map.csv`, finds roll number `120016244019`
- Checks every changed file in the PR
- Since every changed file is under `students/120016244019/`, the check passes ✅
- With the ruleset in place, the **Merge** button becomes available only because this check succeeded

The instructor reviews the PR (code, report, correctness) and merges it into `main` when satisfied.

### B.8 If the Check Fails

This happens if a file outside the student's own folder was accidentally modified, for example editing `students-map.csv` or another roll number's folder. The PR page will show:

```
❌ Check student folder scope / folder-scope-check — Failing
```

**To fix:**

```bash
git checkout week1-assignment
git restore --source=main -- <the file that should not have changed>
git add <that file>
git commit -m "Remove unintended change outside own folder"
git push origin week1-assignment
```

The check re-runs automatically on every new push to the same PR branch.

### B.9 Making Corrections or Adding More Files Later

If the instructor asks for a revision, or a second file needs to be added to the same submission, simply continue committing to the same branch:

```bash
git checkout week1-assignment
# make edits inside students/120016244019/ only
git add students/120016244019/
git commit -m "Revise Week 1 script per feedback"
git push origin week1-assignment
```

No need to open a new pull request, the existing one updates automatically and the check re-runs.

### B.10 Keeping the Fork Updated for Future Weeks (Week 2, Week 3...)

Before starting each new assignment, the fork should be synced with the instructor's latest `main`, in case new folders, checks, or resources were added:

```bash
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
```

| Command | Meaning |
|---|---|
| `git fetch upstream` | Downloads the latest history from the instructor's original repository, without changing local files yet |
| `git merge upstream/main` | Merges those fetched changes into the local `main` branch |
| `git push origin main` | Pushes the now updated `main` back to the student's own fork |

**Then, for each new assignment, repeat from B.4:** create a new branch (`week2-assignment`), add files only inside the own roll number folder, commit, push, open PR.

---

## Part C — Git Command Glossary (Quick Reference)

| Command | One-line meaning |
|---|---|
| `git clone <url>` | Download a full copy of a repository |
| `git status` | Show what is staged, modified, or untracked |
| `git add <path>` | Stage a file for the next commit |
| `git commit -m "msg"` | Save a snapshot of staged changes with a message |
| `git push origin <branch>` | Upload local commits to the remote |
| `git pull origin <branch>` | Download and merge the latest remote changes |
| `git fetch <remote>` | Download remote history without merging |
| `git checkout -b <name>` | Create and switch to a new branch |
| `git checkout <name>` | Switch to an existing branch |
| `git branch -D <name>` | Force delete a local branch |
| `git push origin --delete <name>` | Delete a branch on the remote |
| `git remote -v` | List configured remotes and their URLs |
| `git remote add <name> <url>` | Add an additional remote connection |
| `git remote set-url origin <url>` | Change where `origin` points to |
| `git log --oneline -n` | Show the last n commits, one line each |

---

## Part D — Troubleshooting Log (Issues Actually Faced During This Setup)

| Symptom | Cause | Fix |
|---|---|---|
| `students-map.csv` ended up inside `.github/workflows/` | File created while still inside that subfolder | `mv students-map.csv ../../students-map.csv` |
| `git status` showed files as untracked even after edits | `git add` was never run before `git commit` | Run `git add <paths>` first, confirm with `git status` before committing |
| `Invalid username or token. Password authentication is not supported` | GitHub password auth is fully disabled; either a normal password was entered, or the fine grained token lacked required scopes | Generate a fresh fine grained PAT with Contents (read/write) and Workflows (read/write), verify with `curl -H "Authorization: Bearer TOKEN" https://api.github.com/user` before retrying `git push` |
| `folder-scope-check` not selectable in branch protection | GitHub only lists a status check after it has run at least once | Open one throwaway test PR to trigger the workflow once, then it becomes selectable |
| Bypass list empty locked the admin out too | A ruleset with no bypass actors applies to everyone, including the repository owner | Add **Repository admin** role to the ruleset's bypass list |

---

## Part E — Reusing This Framework for Future Repositories

This exact pattern (shared repo, per-roll-number folders, one CI workflow, one mapping CSV, one ruleset) can be copied for any future course:

1. Create a new repository
2. Copy `.github/workflows/check-student-folder.yml` unchanged
3. Replace the contents of `students-map.csv` with the new batch's roll numbers and GitHub usernames
4. Create fresh `students/<roll_no>/` folders for the new batch
5. Repeat the ruleset setup from **A.8** and **A.9** on the new repository

> [!example] Teaching value for students
> Understanding this workflow means a student can independently set up the exact same kind of controlled, shared repository for their own future group projects, hackathons, or research collaborations, without needing to ask "how do I stop my teammate from editing my part."

---

*Compiled as part of the AOCEE581/681 course repository setup, B2024-28.*
## ⚖️ License

Copyright (c) 2026 Kingsuk Majumdar.

This work is licensed under a **Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License (CC BY-NC-ND 4.0)**.

You are free to share (copy and redistribute the material in any medium or format) for non-commercial, academic and educational purposes, provided that:

- **Attribution** : appropriate credit is given to the author, with a link to this repository.
- **NonCommercial** : the material is not used for commercial purposes.
- **NoDerivatives** : if remixed, transformed or built upon, the modified material may not be distributed.

Full legal text: https://creativecommons.org/licenses/by-nc-nd/4.0/legalcode

## 👨‍🏫 Author

**Kingsuk Majumdar, Ph.D. (Engg)**
Assistant Professor, Department of Electrical Engineering
Dr. B. C. Roy Engineering College (BCREC), Durgapur

GitHub: https://github.com/KingsukMajumdar
