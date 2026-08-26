# Contributing Guidelines

Thanks for your interest in contributing! This repository is for **educational purposes** and is open for anyone to use or contribute to. To keep things consistent and easy to navigate, please follow the guidelines below.

```
MyNotes/
├── 📁 Programmming
│   ├── 📁 Fundamentals
│   ├── 📁 Specializations
│   └── 📁 Syntax
│   └── 📁 Tools
├── 🚫 .gitignore
├── 📝 CONTRIBUTING.md
├── 📝 IDEAS.md
├── 📝 LICENSE
├── 📝 README.md
└── 📝 RESOURCES.md
```

---

**Folder Structure**

1. **File Hierarchy** — `Sector -> Topic -> Lessons`
   - **Sectors** are the top-level, fixed categories: `Fundamentals`, `Specialization`, `Syntax`, `Tools`.
   - **Topics** are subfolders that group lessons sharing a common subject.
   - **Lessons** are the `.md` files with the actual content shown to users, placed directly inside their Topic folder.
2. **Folder Naming**
   - Sector and Topic folders use plain text names — no numbering, no required casing.
   - If a lesson doesn't cleanly fit an existing Sector/Topic, don't force it into the closest match — propose a new folder in your pull request instead. See [Questions or Issues](#questions-or-issues).
   - All files belonging to a Topic — introduction, lessons, and exercises alike — live directly inside that Topic folder. Do not create additional subfolders within a Topic.

**File Structure**

1. **File Types**
   - Introduction and lesson files must be Markdown (`.md`).
   - Exercise files may use whatever file type/extension fits the exercise's syntax (e.g. `.py`, `.js`, `.html`).
2. **Introduction Files**
   - Every Topic begins with a `[0]-Introduction-to-(Topic-Name).md` file.
   - This file contains a Table of Contents section linking to every lesson file inside the Topic.
   - Introduction files carry no navigation links — the Table of Contents *is* their navigation.
3. **Lesson Files**
   - Lesson files are numbered sequentially starting at `[1]`, directly following the `[0]` introduction file: `[1]`, `[2]`, `[3]`, ... up to `[x]`, the last lesson in the Topic.
   - Numbering must stay sequential with no gaps, regardless of how lessons are grouped under headers in the Table of Contents.
   - If you insert a file in the middle, renumber all following files and update every affected navigation link and Table of Contents entry.

**File Naming**

- Numbers are always in square brackets: `[0]`, `[1]`, `[2]`, ...
- `[0]` is always reserved for an **introduction** file.
- Words within a file name are separated by hyphens (`-`), with each word capitalized (PascalCase).
  - Example: `[1]-Variables-And-Data-Types.md`
- Keep numbering sequential across the entire Topic folder — it does not reset or nest.
- The number in a lesson's title (`# Lesson X`) must match the number in its file name and the number used for that lesson in the Introduction file's Table of Contents.

**Lesson Title Format**

Every lesson file (excluding introduction files) must open with a title formatted as:

```
# Lesson X - (Name of Lesson)
```

Example: `Lesson 1 - Variables And Data Types`

- Introduction files are exempt from this title format — they use their own heading (e.g. `# Introduction to Variables`).
- If the lesson is listed under a header in its Introduction file's Table of Contents, that same group name must appear in italics directly above the lesson title:

```
*(Lesson Group Name)*

# Lesson X - (Name of Lesson)
```

Example:

```
*Basics*

# Lesson 1 - Variables And Data Types
```

**Navigation Links**

Every lesson file (excluding introduction files) must include navigation links at both the **very top** and **very bottom** of the file:

```
[Previous](./[X]-Previous-File.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[X]-Next-File.md)
```

- **Introduction files** are exempt — their Table of Contents section serves as navigation.
- **The last file in a Topic** omits `Next`, keeping only `Previous` and `Table of Contents`.
- Links must always point to the correct, currently-valid files — double check these after any renumbering.

**Table of Contents**

Every introduction file must include a Table of Contents listing the lessons in that Topic. Headers may be used to visually group related lessons, but the lesson numbering itself continues straight through and never resets at a header:

```
Example:
**(Lesson Goup Name 1)**
   1. **[Lesson Name](./[1]-Lesson-Name.md)**  
   2. **[Lesson Name](./[2]-Lesson-Name.md)**  

**(Lesson Goup Name 2)**
   3. **[Lesson Name](./[3]-Lesson-Name.md)**  
   4. **[Lesson Name](./[4]-Lesson-Name.md)**  
```

- Add two trailing spaces at the end of each line so the list renders as a stacked list on GitHub.
- Entries must be numbered in the same order the lessons appear, and kept in sync with actual file names/numbers, even across headers.

**Sub-Lesson Structure**

- Each sub-lesson within a lesson file gets its own header, numbered to match its lesson's number (e.g. lesson `[3]` → `## 3.1 What is a Variable?`).
- Sub-lessons are separated from one another by a horizontal rule (`---`) so they're visually distinct when scrolling.
- Keep explanations focused on teaching the concept — save hands-on practice for exercise files (see below).

**Exercises**

- Exercises must **never** be placed inside lesson files.
- Keep exercises in their own separate files, using an extension appropriate to the exercise's syntax (e.g. `.py`, `.js`, `.html`).
- Link to the exercise file from the relevant lesson if applicable, so lessons stay focused on explanation and exercises stay focused on practice.

---

## Content Guidelines

- Only contribute **meaningful and correct** information. Double-check technical accuracy before submitting.
- Keep explanations clear and beginner-friendly where possible — this repo is meant to teach, not just document.
- Prefer concise examples over long, unfocused explanations. Code snippets should be tested and working.
- If you're updating or correcting an existing lesson, briefly note *what* changed and *why* in your pull request description.
- Avoid duplicate content — check the Table of Contents and existing files before adding a new lesson to make sure the topic isn't already covered.
- Cite external sources if you reference specific facts, data, or quotes that aren't common knowledge.

## Submitting Changes

1. Fork the repository.
2. Create a new branch for your change (e.g. `add-lesson-5-loops`).
3. Follow the file structure, and content guidelines above.
4. Update the Table of Contents file to include any new lessons, and fix the Previous/Next links of adjacent files if you insert a lesson in the middle of the sequence.
5. Open a pull request with a short description of what you added or changed.

---

## Questions or Issues

If you spot an error, have a suggestion, or aren't sure how something should be formatted, feel free to open an issue before submitting a pull request.

*Happy learning, and thanks for helping improve this resource!*