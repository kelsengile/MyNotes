# Contributing

Thanks for your interest in contributing! This repository is for **educational purposes** and is open for anyone to use or contribute to. To keep things consistent and easy to navigate, please follow the guidelines below.

## File Structure

Every file must include the following:

1. **Folder Structure** — every lesson lives inside the folder matching its topic type: Fundamental, Specialization, Syntax, or Tools

   If a lesson doesn't fit an existing folder, propose a new one in your pull request rather than placing it in the closest match — see [Questions or Issues](#questions-or-issues).

2. **File Naming**
   - Introduction and lesson files must be Markdown (`.md`). Exercise files can use whatever file type/extension suits the exercise's syntax (e.g. `.py`, `.js`, `.html`).
   - Files are numbered using square brackets, e.g. `[0]`, `[1]`, `[2]`, ...
   - Words within a file name are separated by hyphens (`-`) and written in **PascalCase**.
     - Example: `[1]-Variables-And-Data-Types.md`
   - `[0]` is always reserved for the **introduction** file of a section/repository.
   - Numbering must stay sequential with no gaps — if you insert a lesson in the middle, renumber the following files and update their links (see [Table of Contents](#) and [Navigation links](#)).

3. **Table of Contents** — every introduction file must include a Table of Contents listing the lessons in that topic. Each entry should be structured as:
   ```
   1. **[Lesson Name](./[1]-Lesson-Name.md)**
   ```
   this is done so that readers can easily browse lessons efficiently.

4. **Lesson Title** — formatted as:
   ```
   Lesson X - (Name of Lesson)
   ```
   Example: `Lesson 1 - Variables And Data Types`

   *Introduction files are exempt from this title format.*

5. **Navigation links** — at both the very top and bottom of the file, so readers can move through the repository easily:
   ```
   [Previous](../path/to/previous-file.md) | [Table of Contents](../path/to/Introduction.md) | [Next](../path/to/next-file.md)
   ```  
   *Introduction files are exempt from this title format, and for the final lesson on a topic, there will no longer be a next button*  

6. **Lesson structure**
   - Each sublesson gets its own header (e.g. `## 1.1 What is Programming?`), numbered to match its position in the Table of Contents.
   - Sublessons are separated by a horizontal rule (`---`) so they're visually distinct when scrolling.
   - Exercises must never be placed inside lesson files — keep them in their own separate exercise files, linked from the lesson if relevant, so lessons stay focused on explanation and exercises stay focused on practice.

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
3. Follow the naming, structure, and content guidelines above.
4. Update the Table of Contents file to include any new lessons, and fix the Previous/Next links of adjacent files if you insert a lesson in the middle of the sequence.
5. Open a pull request with a short description of what you added or changed.

## Questions or Issues

If you spot an error, have a suggestion, or aren't sure how something should be formatted, feel free to open an issue before submitting a pull request.

Happy learning, and thanks for helping improve this resource!