# Introduction

## Get up and running quick with Docker

-   **Docker's Role:** A key technology that has revolutionized how software is packaged and shipped, making it easy to run software reliably across different machines.
-   **Core Technology:** Docker simplifies the use of **containers**, which solves the "it works on my machine" problem.
-   **Course Goals:**
    -   Learn the basics of containers: what they are, why they exist, and how they work.
    -   Get started on a containerization journey.

## What you should know

-   **Target Audience:** This is an introductory course for anyone curious about Docker.
-   **Prerequisites:**
    -   No prior programming or shell scripting experience is required.
-   **Required Tools:**
    -   A computer with internet access.
    -   A **terminal**:
        -   **Windows:** PowerShell is recommended.
        -   **Mac/Linux:** A built-in terminal is sufficient.
    -   A **code editor** (optional, for Chapter 3):
        -   Examples: Visual Studio Code, Atom, Sublime Text.
        -   An IDE like IntelliJ or Eclipse also works.

# 1. Docker Explained

## What is Docker?

-   **The Problem:** The "it works on my machine" problem, where an application works in one environment (e.g., a developer's laptop) but fails in another (e.g., a production server).
    -   **Causes:** Differences in installed tools, software dependencies, system configurations, or hardware.
-   **Previous Solutions & Their Drawbacks:**
    -   **Configuration Management (Chef, Ansible, Puppet):** Requires writing code to describe machine state and often demands deep knowledge of the underlying operating system.
    -   **Virtual Machines (Vagrant):** Requires creating entire virtual machines, which is cumbersome and involves configuring a full OS and virtual hardware.
-   **Docker's Approach:**
    -   Docker is software that lets developers package applications into **images** that run inside **containers**.
    -   **Images:** Built from lightweight configuration files (`Dockerfiles`) that describe everything an application needs to run (code, dependencies, tools, etc.).
    -   **Containers:** Virtualized operating systems configured with *just enough* to run the application and nothing more. They are not full virtual machines.
-   **The Key Advantage:** As long as a machine can run Docker, your application will run and behave identically everywhere, ensuring consistency, speed, and safety.
-   **Analogy: The Family Recipe**
    -   Making a recipe at home tastes perfect. Making it at a friend's or parent's house tastes different due to different pans, salt, or stoves.
    -   Docker is like a magic box containing the exact pans, ingredients, and stove needed to make the recipe taste the same, no matter where you are.

## Containers vs. virtual machines

-   The most important difference:
    -   **Virtual Machines (VMs)** virtualize **hardware**.
    -   **Containers** virtualize the **operating system kernel**.

| Feature                | Virtual Machines (VMs)                                                                 | Containers                                                                           |
| ---------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Foundation**         | Runs on a **Hypervisor** (e.g., VMware, VirtualBox).                                   | Runs on a **Container Runtime** (e.g., Docker).                                      |
| **Virtualization**     | Emulates a full hardware stack (CPU, RAM, Disk).                                       | Shares the host's OS kernel; does not emulate hardware.                                |
| **Operating System**   | Each VM requires a full, installed guest OS.                                           | Uses the host machine's OS.                                                          |
| **Size & Resources**   | Large disk footprint (GBs) and significant memory overhead.                            | Very lightweight (MBs) and uses fewer resources.                                     |
| **Startup Speed**      | Slow; requires a full OS boot-up process.                                              | Extremely fast; starts almost instantly like a regular application.                  |
| **Density**            | Can run a limited number of VMs on a single host.                                      | Can run an order of magnitude more containers on a single host.                      |
| **Isolation**          | Strong isolation. VMs cannot see the host or each other.                               | Weaker isolation by default. Containers share the same kernel as the host.           |
| **Application Scope**  | Can run many applications inside one VM.                                               | Designed to run a single application or process per container.                       |

## The anatomy of a container

-   A container is primarily composed of two Linux kernel features:
    1.  **Linux Namespaces**
    2.  **Linux Control Groups (cgroups)**

-   **Namespaces:**
    -   Provide isolated "views" of the system to an application.
    -   An application can believe it has root access or its own file system when it is actually restricted.
    -   **Key Namespaces Used by Docker:**
        -   `USERNS`: Isolates user and group IDs.
        -   `MOUNT`: Isolates filesystem mount points.
        -   `NET`: Isolates network interfaces.
        -   `IPC`: Isolates inter-process communication.
        -   `PID`: Isolates process IDs.
        -   `UTS`: Isolates host and domain names.
    -   **Note:** Docker uses all namespaces *except* for the `TIME` namespace, meaning you **cannot** change the system time inside a Docker container.

-   **Control Groups (cgroups):**
    -   A Linux kernel feature that restricts how much hardware a process can use.
    -   Docker uses cgroups to monitor and limit:
        -   **CPU Usage:** The amount of CPU time a container can use.
        -   **Memory Consumption:** The amount of RAM a container can use (a very common use case).
        -   **Network & Disk Bandwidth:** The I/O throughput for a container.
    -   **Note:** cgroups **cannot** be used to set disk quotas for containers.

-   **Important Caveats:**
    1.  **Platform Dependency:** Because namespaces and cgroups are Linux features, Docker is a Linux-native technology (though it now runs on newer Windows versions and has workarounds for macOS).
    2.  **Kernel Compatibility:** Container images are tied to the kernel they were created for. A **Linux container image can only run on a Linux host**, and a Windows container image can only run on a Windows host.

## The Docker difference

-   The idea of containers existed long before Docker.
-   **History of Container-like Technology:**
    -   **`chroot` (1979):** The earliest attempt. Changed an application's root directory, providing basic filesystem isolation.
    -   **BSD Jails (1999) & Solaris Zones (2004):** Created more complete virtual environments without hardware emulation.
    -   **Linux Containers (LXC) (2007):** The Linux equivalent using namespaces and cgroups. It is powerful but complex to configure manually (user mappings, network bridges, etc.).

-   **Why Docker Shines (The Docker Advantages):**
    1.  **Easy Configuration:**
        -   Uses simple, human-readable **`Dockerfiles`** to define a container's environment and build an image.
    2.  **Easy Sharing:**
        -   **Docker Hub** provides a global public repository for sharing and downloading images.
        -   Users can easily create private or alternative registries.
    3.  **Easy to Use Command-Line Interface (CLI):**
        -   The `docker` command simplifies everything.
        -   Starting a container is as easy as running `docker run <image_name>`.
        -   It abstracts away the complexities of configuring networking, storage, and user mappings.