[Previous](./[3]-Popular-IDEs.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[5]-File-And-Code-Navigation.md)

*Project And Workspace Management*

# Lesson 4 - Projects And Workspaces

## 4.1 What Is a Project

A project, in IDE terms, is a folder containing all the source files, assets, and configuration that make up a single piece of software. Opening a project tells the IDE where the code lives so it can index the files, resolve imports between them, and know what to build or run. Most IDEs mark a folder as a project through a special file or folder, such as a `.idea` folder, a `.vscode` folder, or a `.sln` solution file.

**Example folder structure the moment VS Code recognizes a project:**

```
my-app/
├── .vscode/
│   └── settings.json
├── src/
│   └── main.js
├── package.json
└── README.md
```

Opening the `my-app` folder itself (not just a single file inside it) is what tells the IDE "this whole directory is the project."

---

## 4.2 Workspace Configuration

A workspace holds IDE-specific settings for a project — which formatter to use, which folders to ignore, custom build tasks, or recommended extensions. These settings are usually stored in a config file inside the project (for example, a `.vscode/settings.json` file) so that the whole team shares the same setup instead of everyone configuring their editor by hand.

```json
{
  "editor.formatOnSave": true,
  "editor.tabSize": 2,
  "files.exclude": {
    "node_modules": true,
    "dist": true
  }
}
```

Committing a file like this to version control means every teammate who opens the project gets the same tab size and the same folders hidden from the file explorer, automatically.

---

## 4.3 Multi-Root Workspaces

Some projects are made of several separate folders that need to be open and indexed together — for example, a frontend folder and a backend folder in the same application. A multi-root workspace lets an IDE treat multiple folders as one logical project, so navigation and search work across all of them at once instead of requiring separate windows.

```
MyApp.code-workspace
├── frontend/   (React app)
└── backend/    (Node.js API)
```

A project-wide search for a shared type name would return results from both `frontend/` and `backend/` in one pass, which matters when the two halves of an app change together frequently.

---

## 4.4 Project Files And Settings

Beyond the IDE's own configuration, most languages and frameworks have their own project files that describe dependencies and build steps — a `package.json` for Node.js projects, a `.csproj` for .NET, or a `pom.xml` for Java with Maven. IDEs read these files to know how to fetch dependencies, build the code, and offer relevant autocompletion, so keeping them accurate matters as much as the code itself.

| Ecosystem | Project file | Managed by |
|---|---|---|
| Node.js | `package.json` | npm/yarn/pnpm |
| .NET | `.csproj` | MSBuild/NuGet |
| Java (Maven) | `pom.xml` | Maven |
| Python | `pyproject.toml` | pip/Poetry |

---

[Previous](./[3]-Popular-IDEs.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[5]-File-And-Code-Navigation.md)
