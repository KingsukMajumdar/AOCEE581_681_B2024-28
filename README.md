# Applied Exponential Models for Electrical Engineering I & II

*One Curve, Many Worlds: From Circuit Theory to Motor Drive*

**Course Code:** EE-AOC-581 & EE-AOC-681
**Type:** Add-On / Value-Added Course (Non-Credit, Certificated)
**Programme:** B.Tech (Electrical Engineering)
**Batch:** B2024-28, Academic Year 2026-27
**Semester 5 (Part I):** Modules 1 to 5, 42 contact hours (this repository's current scope)
**Semester 6 (Part II):** Modules 6 to 8, 32 contact hours (next academic year)
**Venue:** Sim Lab-III, EE Dept., BCREC

---

## 📘 About this Repository

This repository is the shared workspace for all 7 selected students of the current batch to submit their module-wise practical work for EE-AOC-581 (Semester 5). Full course details, prerequisites, assessment structure, and CO-PO mapping are documented in the official syllabus PDFs under `syllabus/`.

## 📂 Repository Structure

```
AOCEE581_681_B2024-28/
├── .github/
│   └── workflows/
│       └── check-student-folder.yml      # automated folder-scope check
├── syllabus/
│   ├── EE_AOC_581_681_Compact_Syllabus.pdf
│   ├── EE_AOC_581_681_Vivid_V6.pdf
│   └── NomenclatureEEAOC01.pdf
├── students-map.csv                       # roll number to GitHub username mapping
├── students/
│   └── <roll_no>/
│       ├── Module_01/
│       ├── Module_02/
│       ├── Module_03/
│       ├── Module_04/
│       ├── Module_05/
│       └── MiniProject/
└── README.md
```

## 🙋 Why this Repository is Helpful for Students

- **Learn real industry workflow, not just theory.** Fork, branch, commit, pull request, this is the exact process used in professional software and engineering teams, so this course quietly doubles as version control training alongside power electronics and machine theory.
- **Automatic protection of your own work.** An automated check blocks any pull request that touches a file outside your own roll number folder, so your submissions stay safe from accidental overwrites, and you always know exactly where your work lives.
- **A permanent, timestamped record.** Every submission, revision, and instructor comment is preserved in the repository history, giving you a verifiable trail of your own progress across all five modules and the mini project, useful for your own reference long after the course ends.

## 📝 How to Submit Your Work

1. Fork this repository to your own GitHub account.
2. Clone your fork locally.
3. Place your files inside `students/<your_roll_no>/Module_0X/`, following the naming pattern `<roll_no>_M0X_<descriptor>.<ext>` (see the workflow guide for full details).
4. Commit, push to your fork, then open a pull request back to this repository's `main` branch.
5. Do not modify any file outside your own roll number folder, the automated check will block the pull request if you do.

For the complete step by step walkthrough, including authentication setup and a full worked example, see the workflow documentation shared separately by the instructor.

## 🔄 Keeping Your Fork Up to Date (Sync)

### Why sync is needed

Your fork is a copy **frozen at the moment you forked it**. When the instructor adds new notes, instructions, or checks to this repository, your fork does **not** update by itself. Three copies exist at any time:

```text
KingsukMajumdar/AOCEE581_681_B2024-28   ← "upstream"  (instructor's original, always latest)
          │  Step 1: Sync fork (browser)
          ▼
<your_username>/AOCEE581_681_B2024-28   ← "origin"    (your fork on GitHub)
          │  Step 2: git pull (terminal)
          ▼
~/Documents/AOCEE581_681_B2024-28       ← local clone on your own machine
```

Sync always flows **downward**: upstream to fork, then fork to machine.

> **Note:** A late or out-of-date fork never harms other students' work. Git merges your pull request using the point where your branch started, so earlier submissions and new instructor notes already in `main` are kept. You sync to **see the latest instructions**, not to avoid damage.

### Step 1: Sync your fork on GitHub (browser)

1. Open **your fork**: `https://github.com/<your_username>/AOCEE581_681_B2024-28`
2. Make sure the branch selector (top left) shows **`main`**.
3. Below the green **Code** button, GitHub shows a line such as *"This branch is 4 commits behind KingsukMajumdar/AOCEE581_681_B2024-28:main"*.
4. Click **Sync fork**, then **Update branch**.

The line now reads *"This branch is up to date"*.

> ⚠️ **Never click "Discard N commits".** It appears only if you committed directly on your fork's `main`, and it deletes those commits permanently. Use the terminal method below instead, or contact the instructor.

### Step 2: Bring the update to your machine (terminal)

```bash
cd ~/Documents/AOCEE581_681_B2024-28
git checkout main
git pull origin main
```

| Command | Meaning |
|---|---|
| `git checkout main` | Switches to your local `main` branch |
| `git pull origin main` | Downloads the updated `main` from **your fork** and merges it into your local `main` |

### Alternative: Terminal only (Step 1 + Step 2 together)

```bash
cd ~/Documents/AOCEE581_681_B2024-28
git remote -v
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
```

| Command | Meaning |
|---|---|
| `git remote -v` | Lists remotes. Both `origin` (your fork) and `upstream` (instructor) must appear |
| `git fetch upstream` | Downloads the latest history from this repository. Your files do not change yet |
| `git merge upstream/main` | Merges that history into your local `main` |
| `git push origin main` | Uploads the updated `main` to your fork, so the fork is also in sync |

If `upstream` is missing, add it once:

```bash
git remote add upstream https://github.com/KingsukMajumdar/AOCEE581_681_B2024-28.git
```

### If your pull request is already open

First sync `main` (above), then bring the update into your work branch:

```bash
git checkout module2-submission
git merge main
git push origin module2-submission
```

| Command | Meaning |
|---|---|
| `git merge main` | Brings the synced `main` into your work branch. No conflict arises, since you touch only your own folder |
| `git push origin module2-submission` | Updates the open pull request. The folder check re-runs automatically |

Browser alternative: click **Update branch** on the pull request page.

### Routine before every new module

```bash
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
git checkout -b module3-submission
```

**Sync first, then create the new branch.** Your new branch then starts from the latest version, with all current notes visible.

### Quick troubleshooting

| Symptom | Fix |
|---|---|
| `fatal: 'upstream' does not appear to be a git repository` | Add the `upstream` remote as shown above |
| An editor opens asking for a merge message | nano: `Ctrl+O`, `Enter`, `Ctrl+X`. vim: type `:wq` and press `Enter` |
| `error: Your local changes would be overwritten` | Commit your edits first, or run `git stash`, sync, then `git stash pop` |
| `CONFLICT (content)` | Run `git merge --abort` and contact the instructor. This never happens if you stay inside `students/<your_roll_no>/` |

## 📚 Reference Material

Full syllabus, prerequisites, course outcomes, and assessment breakdown: see `syllabus/EE_AOC_581_681_Compact_Syllabus.pdf`

## 👨‍🏫 Instructor

**Kingsuk Majumdar, Ph.D. (Engg.)**
Assistant Professor (Grade II), Department of Electrical Engineering
Dr. B. C. Roy Engineering College, Durgapur, West Bengal, India
Email: kingsuk.majumdar@bcrec.ac.in
GitHub: [KingsukMajumdar](https://github.com/KingsukMajumdar)


- **Version**
  
 | Version no | Date |
 | ----|----|
 |V 1.0 | 2026-08-12|
 |V 1.1 | 2026-09-29|
