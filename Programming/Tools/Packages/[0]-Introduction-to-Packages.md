[⬅ Back to README](../../../README.md)

# Packages

Welcome! This is a self-paced course for learning packages and dependency management — the systems that let developers reuse, share, and install code instead of writing everything from scratch.

## What are Packages?

Packages let you:
- Reuse code written by others instead of reinventing the wheel
- Pull in tools and libraries with a single install command
- Track and manage the exact versions your project depends on
- Share your own code so other developers can install and use it
- Keep dependencies consistent across machines and teammates

## Table of Contents

1. **[What Is A Package?](./%5B1%5D-What-Is-a-Package.md)**  
   1.1 Overview  
   1.2 What's Inside a Package?  
   1.3 A Simple Example  
   1.4 Packages vs. Libraries vs. Modules  
   1.5 Why Not Just Copy-Paste the Code?  
2. **[Package Managers](./[2]-Package-Managers.md)**  
   2.1 What Is a Package Manager?  
   2.2 What a Package Manager Does  
   2.3 Common Package Managers by Language  
   2.4 Example: Installing a Package  
   2.5 Project-Level vs. System-Level Package Managers  
   2.6 How a Package Manager Resolves Dependencies  
   2.7 Choosing Between Package Managers  
3. **[Installing, Updating And Removing Packages](./%5B3%5D-Installing-Updating-and-Removing-Packages.md)**  
   3.1 Installing Packages  
   3.2 Installing a Specific Version  
   3.3 Development-Only Dependencies  
   3.4 Updating Packages  
   3.5 Removing Packages  
   3.6 Reinstalling Everything From Scratch  
   3.7 Where Do Installed Packages Go?  
4. **[Dependencies And Dependency Trees](./%5B4%5D-Dependencies-and-Dependency-Trees.md)**  
   4.1 What Is a Dependency?  
   4.2 Transitive (Indirect) Dependencies  
   4.3 Viewing a Dependency Tree  
   4.4 Why Dependency Trees Get Complicated  
   4.5 Dependency Depth and "Dependency Bloat"  
   4.6 Peer Dependencies  
   4.7 Optional Dependencies  
5. **[Versioning And Semantic Versioning](./%5B5%5D-Versioning-and-Semantic-Versioning.md)**  
   5.1 Why Versions Matter  
   5.2 Semantic Versioning (SemVer)  
   5.3 Version Ranges  
   5.4 Pre-Release and Build Versions  
   5.5 Reading a Changelog  
   5.6 Not Every Ecosystem Follows SemVer Strictly  
6. **[Lockfiles And Reproducible Installs](./%5B6%5D-Lockfiles-and-Reproducible-Installs.md)**  
   6.1 The Problem: "It Works on My Machine"  
   6.2 The Solution: Lockfiles  
   6.3 Should You Commit the Lockfile?  
   6.4 Installing From a Lockfile  
   6.5 What's Actually Inside a Lockfile?  
7. **[Local Vs. Global Vs. Project-Level Packages](./%5B7%5D-Local-vs-Global-vs-Project-Level-Packages.md)**  
   7.1 Project-Level (Local) Installs  
   7.2 Global Installs  
   7.3 Running a Package Without Installing It Globally  
   7.4 Virtual Environments (Python's Special Case)  
   7.5 Project-Level "Local Bin" Tools  
8. **[Package Registries](./[8]-Package-Registries.md)**  
   8.1 What Is a Registry?  
   8.2 Major Public Registries  
   8.3 What a Registry Actually Stores  
   8.4 How Package Managers Talk to Registries  
   8.5 Searching a Registry  
   8.6 Registry Mirrors and Proxies  
   8.7 Trust and the Public Registry Model  
9. **[Creating Your Own Package](./[9]-Creating-Your-Own-Package.md)**  
   9.1 When Should Code Become a Package?  
   9.2 Basic Structure  
   9.3 Example: Creating a JavaScript Package  
   9.4 Example: Creating a Python Package  
   9.5 Example: Creating a Rust Package (Crate)  
   9.6 Writing a Good README  
   9.7 Writing Tests  
   9.8 Choosing a License  
10. **[Publishing Packages](./[10]-Publishing-Packages.md)**  
    10.1 Before You Publish: A Checklist  
    10.2 Publishing to npm  
    10.3 Publishing to PyPI  
    10.4 Publishing to crates.io  
    10.5 Semantic Versioning When Publishing Updates  
    10.6 Yanking / Unpublishing  
    10.7 Automating Publishing with CI/CD  
    10.8 README and Metadata Matter More Than You'd Think  
11. **[Monorepos And Workspaces](./%5B11%5D-Monorepos-and-Workspaces.md)**  
    11.1 What Is a Monorepo?  
    11.2 What Are Workspaces?  
    11.3 Benefits of Monorepos + Workspaces  
    11.4 Trade-offs  
    11.5 Running Commands Across a Workspace  
    11.6 Publishing From a Monorepo  
12. **[Private And Internal Registries](./%5B12%5D-Private-and-Internal-Registries.md)**  
    12.1 Why Use a Private Registry?  
    12.2 Common Private Registry Solutions  
    12.3 Scoped Packages (npm)  
    12.4 Pointing Your Package Manager at a Private Registry  
    12.5 Registry Proxies / Pull-Through Caches  
    12.6 Authentication  
13. **[Package Security And Supply-Chain Risks](./%5B13%5D-Package-Security-and-Supply-Chain-Risks.md)**  
    13.1 What Is a Supply-Chain Risk?  
    13.2 Common Types of Package-Based Attacks  
    13.3 Auditing Your Dependencies  
    13.4 Automated Dependency Update Tools  
    13.5 Reducing Your Attack Surface  
    13.6 Verifying Package Integrity  
    13.7 A Real Example: left-pad (2016)  
14. **[Dependency Conflicts And Troubleshooting](./%5B14%5D-Dependency-Conflicts-and-Troubleshooting.md)**  
    14.1 What Is a Dependency Conflict?  
    14.2 How Different Ecosystems Handle This  
    14.3 "Dependency Hell"  
    14.4 Reading an Error Message  
    14.5 Common Fixes  
    14.6 Debugging Strategy  
15. **[License Compliance](./[15]-License-Compliance.md)**  
    15.1 Why Licenses Matter  
    15.2 Common Open-Source Licenses  
    15.3 Permissive vs. Copyleft  
    15.4 Checking a Package's License  
    15.5 Auditing Licenses Across Your Dependency Tree  
    15.6 Some Packages Have No License at All  
16. **[Optimizing Install Size And Build Performance](./%5B16%5D-Optimizing-Install-Size-and-Build-Performance.md)**  
    16.1 Why This Matters  
    16.2 Auditing Your Dependency Size  
    16.3 Removing Unused Dependencies  
    16.4 Choosing Lighter Alternatives  
    16.5 Deduplication  
    16.6 Excluding Files From Published Packages  
    16.7 Caching to Speed Up Installs  
    16.8 Lazy-Loading and Code Splitting  
