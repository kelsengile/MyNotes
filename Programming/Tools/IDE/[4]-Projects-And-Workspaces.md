[Previous](./[3]-Popular-IDEs.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[5]-File-And-Code-Navigation.md)

*Project And Workspace Management*

# Lesson 4 - Projects And Workspaces

## 4.1 What Is a Project

A project, in IDE terms, is a folder containing all the source files, assets, and configuration that make up a single piece of software. Opening a project tells the IDE where the code lives so it can index the files, resolve imports between them, and know what to build or run. Most IDEs mark a folder as a project through a special file or folder, such as a `.idea` folder, a `.vscode` folder, or a `.sln` solution file.

---

## 4.2 Workspace Configuration

A workspace holds IDE-specific settings for a project — which formatter to use, which folders to ignore, custom build tasks, or recommended extensions. These settings are usually stored in a config file inside the project (for example, a `.vscode/settings.json` file) so that the whole team shares the same setup instead of everyone configuring their editor by hand.

---

## 4.3 Multi-Root Workspaces

Some projects are made of several separate folders that need to be open and indexed together — for example, a frontend folder and a backend folder in the same application. A multi-root workspace lets an IDE treat multiple folders as one logical project, so navigation and search work across all of them at once instead of requiring separate windows.

---

## 4.4 Project Files And Settings

Beyond the IDE's own configuration, most languages and frameworks have their own project files that describe dependencies and build steps — a `package.json` for Node.js projects, a `.csproj` for .NET, or a `pom.xml` for Java with Maven. IDEs read these files to know how to fetch dependencies, build the code, and offer relevant autocompletion, so keeping them accurate matters as much as the code itself.

[Previous](./[3]-Popular-IDEs.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[5]-File-And-Code-Navigation.md)
