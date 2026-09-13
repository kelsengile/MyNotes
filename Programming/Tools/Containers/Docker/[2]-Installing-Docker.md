[Previous](./[1]-What-Is-Docker.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[3]-Docker-Images-And-Dockerfiles.md)

*Getting Started*

# Lesson 2 - Installing Docker

## 2.1 Choosing an Installation Method

There are two main ways to get Docker running on your machine:

| Method | Best for | Includes a GUI? |
|---|---|---|
| Docker Desktop | Windows and macOS users, beginners | Yes |
| Docker Engine (CLI only) | Linux servers, advanced users | No |

Docker Desktop is the recommended starting point for this Topic — it installs the Engine, the CLI, and a graphical dashboard for viewing images and containers, all in one installer.

## 2.2 Installing on Windows and macOS

1. Download Docker Desktop from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop).
2. Run the installer and follow the prompts.
3. On Windows, Docker Desktop uses WSL 2 (Windows Subsystem for Linux) under the hood — the installer will prompt you to enable it if it isn't already.
4. Launch Docker Desktop and wait for it to report that the Engine is running (usually shown as a whale icon in your system tray or menu bar).

## 2.3 Installing on Linux

On Linux, you can install the Docker Engine directly without Docker Desktop. The exact steps vary by distribution, but the general flow (for Ubuntu/Debian-based systems) looks like this:

```bash
# Update package lists
sudo apt-get update

# Install Docker's official package
sudo apt-get install docker.io

# Start the Docker service
sudo systemctl start docker
sudo systemctl enable docker
```

> 💡 **Tip:** On Linux, running `docker` commands normally requires `sudo` unless you add your user to the `docker` group with `sudo usermod -aG docker $USER`, followed by logging out and back in.

## 2.4 Verifying Your Installation

Once installed, confirm everything is working with two commands:

```bash
# Check the installed version
docker --version

# Run a small test container
docker run hello-world
```

If `docker run hello-world` succeeds, it will print a friendly confirmation message — Docker pulled the `hello-world` image from Docker Hub, ran it as a container, and the container printed its message before exiting.

### Quick check: is Docker installed correctly?

| Result of `docker run hello-world` | What it means |
|---|---|
| Prints "Hello from Docker!" message | ✅ Installed correctly |
| "command not found: docker" | ❌ CLI isn't installed or isn't on your PATH |
| "Cannot connect to the Docker daemon" | ❌ Docker Desktop/Engine isn't running |

With Docker installed and verified, you're ready to start building your own images — covered next.

---

[Previous](./[1]-What-Is-Docker.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[3]-Docker-Images-And-Dockerfiles.md)
