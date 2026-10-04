[Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[2]-Editor-Tour.md)

*Getting Started*

# Lesson 1 - Installing Godot & First-Time Setup

Before you can write any GDScript, you need the Godot Engine. Godot is a single, small program that contains the editor, the script editor, the game runner, and the documentation. There is no separate "GDScript installer" because the language is built into the engine. This lesson walks you through downloading Godot, installing it on your operating system, and creating your first project.

---

## 1.1 What You Need Before You Start

Godot is lightweight, so almost any computer from the last ten years can run it. Before you download anything, check the following:

- **Operating system:** Windows 10 or newer, macOS 10.13 or newer, or a modern Linux distribution (64-bit).
- **Graphics card:** Any GPU that supports at least OpenGL 3.3 (or OpenGL ES 3.0 on older hardware). Newer GPUs that support Vulkan, Metal, or Direct3D 12 can use Godot's more advanced renderers.
- **Disk space:** The editor itself is around 100-200 MB. Your projects will need extra space for art and audio.
- **A mouse is strongly recommended.** The editor can be used with a trackpad, but 2D and 3D editing is much easier with a three-button mouse.
- **An internet connection** for the initial download and to browse the Asset Library.

You do **not** need any previous programming experience for this course, but it helps to be comfortable with basic computer tasks such as unzipping files and creating folders.

> **Tip:** Godot does not require administrator rights or a traditional installer. It is a single executable you can run from anywhere, including a USB drive.

---

## 1.2 Downloading Godot (Standard vs .NET version)

Go to the official download page: [https://godotengine.org/download/](https://godotengine.org/download/)

You will see two versions of the editor for each platform:

| Version | Scripting languages | Choose it when |
| --- | --- | --- |
| **Standard** | GDScript (and GDExtension for C/C++/Rust) | You want to follow this course. **This is the one you want.** |
| **.NET** | GDScript **and** C# | You plan to write C# code as well |

Both versions fully support GDScript. The .NET version is larger and requires you to install the separate [.NET SDK](https://dotnet.microsoft.com/download). Since this course teaches GDScript, download the **Standard** version.

Other things you should know about downloading:

- **Always pick a stable release.** Stable builds are tested and reliable. Beta, release candidate (RC), and dev builds are for people who want to test new features and may contain bugs.
- **This course targets Godot 4.x.** Godot 3.x uses an older version of GDScript that is *not* compatible with the code in these lessons. If you see tutorials online using `onready var`, `export var`, or `yield`, they are written for Godot 3.
- **Steam and itch.io** also distribute Godot. These are the same engine and are convenient if you want automatic updates.

---

## 1.3 Installing on Windows, macOS, and Linux

Godot is "portable", so installing really means "extract and run".

### Windows

1. Download the `.zip` file for Windows.
2. Right-click the zip and choose **Extract All...**.
3. Move the extracted folder somewhere permanent, such as `C:\Godot\`.
4. Double-click `Godot_v4.x-stable_win64.exe` to start it.
5. Optionally right-click the executable and choose **Pin to Start** or create a desktop shortcut.

### macOS

1. Download the `.zip` file for macOS.
2. Double-click it to extract. You get `Godot.app`.
3. Drag `Godot.app` into your **Applications** folder.
4. The first time you open it, macOS may warn that the app was downloaded from the internet. Right-click the app, choose **Open**, then confirm.

### Linux

1. Download the Linux `.zip` file (for example `Godot_v4.x-stable_linux.x86_64.zip`).
2. Extract it with your file manager or with the terminal:

```bash
unzip Godot_v4.x-stable_linux.x86_64.zip
```

3. Make sure the file is executable, then run it:

```bash
chmod +x Godot_v4.x-stable_linux.x86_64
./Godot_v4.x-stable_linux.x86_64
```

Many Linux distributions also provide Godot through their package manager, Flatpak (Flathub), or Snap. Those packages work fine, but the version may lag behind the official release.

> **Note:** The exact file names change with each version number. The `x` above stands for the minor version, for example `4.4` or `4.5`.

---

## 1.4 The Project Manager

When you launch Godot, you do not see the editor right away. You see the **Project Manager**, a window that lists all your projects and lets you create new ones.

The main parts of the Project Manager are:

- **Projects tab:** A list of every project Godot knows about. Double-click one to open it in the editor.
- **Asset Library tab:** A browser for free templates, demos, and add-ons that you can download into a project.
- **Buttons on the right:**
  - **Create:** Make a brand-new project.
  - **Import:** Add an existing project folder by choosing its `project.godot` file.
  - **Scan:** Search a folder for existing Godot projects.
  - **Edit / Run / Rename / Remove:** Manage the selected project. *Remove* only removes it from the list. It does **not** delete your files.
- **Search and sort bar:** Helps you find projects when your list grows long.
- **Settings (top right):** Change the editor language, theme, and other preferences that apply to every project.

Think of the Project Manager as a launcher. It is only a doorway into the editor.

---

## 1.5 Creating Your First Project

Click **Create** in the Project Manager. A dialog appears with several fields:

1. **Project Name:** Choose a short descriptive name such as `HelloGodot`.
2. **Project Path:** Choose an **empty** folder where the project files will live. Click **Create Folder** to make a new one for you. Using a separate folder per project keeps everything tidy.
3. **Renderer:** Choose how Godot draws graphics (explained in the next sub-lesson).
4. **Version Control Metadata:** Choose **Git** if you plan to use Git. It generates a `.gitignore` file for you.

Click **Create & Edit**. Godot creates the project folder and opens the editor.

Inside your new folder, Godot creates a handful of files:

```
HelloGodot/
├── .godot/          (cache folder, generated automatically)
├── icon.svg         (the default project icon)
└── project.godot    (the project's settings file)
```

You will learn more about this structure in [Lesson 4](./[4]-Project-Structure-and-Settings.md). For now, just remember that **one folder equals one project**, and the file `project.godot` marks the folder as a Godot project.

---

## 1.6 Choosing a Renderer (Forward+, Mobile, Compatibility)

The **renderer** is the part of the engine that draws everything on screen. Godot 4 offers three renderers:

| Renderer | Graphics API | Best for | Notes |
| --- | --- | --- | --- |
| **Forward+** | Vulkan / Direct3D 12 / Metal | High-end desktop 3D games | Most features, best visual quality |
| **Mobile** | Vulkan / Direct3D 12 / Metal | Mobile and mid-range hardware | Fewer features, faster on phones |
| **Compatibility** | OpenGL 3.3 / OpenGL ES 3.0 / WebGL 2 | Older hardware, simple 2D games, Web export | Widest support, fewest features |

Here is a simple rule of thumb:

- Making a **2D game** or want maximum compatibility, including web browsers? Choose **Compatibility**.
- Making a **3D game for PC or console**? Choose **Forward+**.
- Making a **3D game for phones**? Choose **Mobile**.
- **Not sure?** Choose **Forward+**. It is the default and works well on most modern computers.

The renderer is **not permanent**. You can change it later in **Project > Project Settings > Rendering > Renderer**, though some visual features may look slightly different after the switch.

Your GDScript code is the same no matter which renderer you pick, so this choice does not affect what you learn in this course.

---

## 1.7 Using an External Editor (VS Code, etc.)

Godot has a good built-in script editor, and this course assumes you use it. Still, many programmers prefer the editor they already know. Godot supports this.

### Why use an external editor?

- You already know its shortcuts and extensions.
- You want features such as advanced Git integration or multi-cursor editing.
- You work with several languages in one window.

### Setting up Visual Studio Code

1. Install [Visual Studio Code](https://code.visualstudio.com/).
2. In VS Code, open the Extensions panel and install **godot-tools** (the official GDScript extension).
3. In Godot, open **Editor > Editor Settings** and enable **Advanced Settings** at the top right of the dialog.
4. Go to **Text Editor > External** (the exact location may differ slightly by version).
5. Turn on **Use External Editor**.
6. Set **Exec Path** to the location of your VS Code executable, for example `code` on Linux or the full path to `Code.exe` on Windows.
7. Set **Exec Flags** to:

```
{project} --goto {file}:{line}:{col}
```

Now double-clicking a script in Godot opens it in VS Code at the correct line.

### How the connection works

The built-in script editor talks to a **Language Server (LSP)** running inside Godot. External editors connect to it to get auto-completion, error checking, and documentation hints. The language server is only active **while the Godot editor is open**. If your external editor shows no completions, make sure Godot is running with the project loaded.

Other editors with GDScript support include Vim/Neovim, Emacs, Sublime Text, JetBrains Rider, and Zed. They all use the same language server.

> **Recommendation:** Use the built-in editor while you are learning. Its tight integration with the engine (clicking on a node path, previewing docs, running with one key) makes learning faster.

---

 [Table of Contents](./[0]-Introduction-to-GDScript.md) | [Next](./[2]-Editor-Tour.md)