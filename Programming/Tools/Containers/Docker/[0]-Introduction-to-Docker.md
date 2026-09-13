[⬅ Back to Container Fundamentals](../[0]-Introduction-to-Containers.md)

# Introduction to Docker

Docker is the tool that made containers practical for everyday development. It provides everything needed to build a container image, run it as a container, and share it with others — all through a small set of commands and a simple text file called a `Dockerfile`. If the previous Topic taught you *what* a container is, this Topic teaches you how to actually build and run one using the industry's most common tool for doing so.

Docker is usually the first hands-on stop for anyone learning containers, and its image format and command-line conventions are so widely adopted that most other container tools — including Kubernetes — are built to understand them directly.

Download: [Docker Desktop](https://www.docker.com/products/docker-desktop)

## Why Learn Docker?

- **It's the industry standard for building images** — even teams that run Kubernetes in production almost always build their container images with Docker or Docker-compatible tooling.
- **It's the fastest way to experience containers hands-on** — a single `docker run` command can have a container running on your machine in seconds.
- **It bridges directly into Kubernetes** — the images, registries, and core vocabulary (image, container, layer, volume) you learn here carry straight over into orchestration.
- **It supports full local development stacks** — with Docker Compose, you can spin up a database, a backend, and a frontend together with one command.

## What You'll Need

Docker runs on Windows, macOS, and Linux. The easiest way to get started is Docker Desktop (linked above), which bundles the Docker Engine, the `docker` command-line tool, and a graphical interface for managing images and containers. Installation steps are covered in Lesson 2.

## Table of Contents

**Getting Started**

   1. **[What Is Docker?](./[1]-What-Is-Docker.md)**  
       1.1 Docker's Role in the Container Ecosystem  
       1.2 The Docker Engine and CLI  
       1.3 Docker's Core Objects  
   2. **[Installing Docker](./[2]-Installing-Docker.md)**  
       2.1 Choosing an Installation Method  
       2.2 Installing on Windows and macOS  
       2.3 Installing on Linux  
       2.4 Verifying Your Installation  

**Working With Docker**

   3. **[Docker Images And Dockerfiles](./[3]-Docker-Images-And-Dockerfiles.md)**  
       3.1 What Goes Into an Image  
       3.2 Writing a Dockerfile  
       3.3 Common Dockerfile Instructions  
       3.4 Building an Image  
   4. **[Running And Managing Containers](./[4]-Running-And-Managing-Containers.md)**  
       4.1 Starting a Container  
       4.2 Common `docker run` Flags  
       4.3 Inspecting Running Containers  
       4.4 Stopping and Removing Containers  
   5. **[Docker Volumes And Networking](./[5]-Docker-Volumes-And-Networking.md)**  
       5.1 Why Containers Need Persistent Storage  
       5.2 Volumes vs Bind Mounts  
       5.3 Docker Networking Basics  
       5.4 Connecting Containers to Each Other  
   6. **[Docker Compose](./[6]-Docker-Compose.md)**  
       6.1 Why Docker Compose Exists  
       6.2 Writing a `docker-compose.yml` File  
       6.3 Common Compose Commands  
       6.4 From Compose to Kubernetes
