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
