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

***

# 2. Installing Docker

## Docker Desktop

-   **The Core Problem:** Docker is a Linux-native technology because it relies on Linux **control groups (cgroups)** and **namespaces**. Since most developers use macOS or Windows, a workaround is needed.

-   **The First Workaround: Docker Machine**
    -   Used Oracle's **VirtualBox** to create a small Linux VM whose sole purpose was to run the Docker engine.
    -   **Drawbacks:**
        1.  **Complexity:** Required users to know VirtualBox and its command-line tool (`VBoxManage`) for tasks like port forwarding or volume mounting, making adoption difficult.
        2.  **Performance Issues:** Suffered from slow disk and networking performance, which was largely outside of Docker's control.

-   **The Modern Solution: Docker Desktop**
    -   Released in 2016 as a superior replacement for Docker Machine.
    -   **Key Improvements:**
        1.  **Faster VM:** Uses native hypervisors, which are smaller and faster.
            -   **macOS:** Apple's Virtual Kit
            -   **Windows:** Microsoft's Hyper-V
        2.  **Seamless Integration:** Automatically handles complex tasks like mounting volumes and exposing network ports from the VM to the host.
        3.  **Graphical User Interface (GUI):** Provides a user-friendly interface to configure the Docker engine and manage other features, like setting up a Kubernetes cluster.

-   **Important Note on Licensing (2021 Change):**
    -   Docker's parent company, Mirantis, updated the license for Docker Desktop.
    -   It is **no longer free for large companies** (more than 250 employees OR more than $10 million in revenue). These companies must purchase a subscription.
    -   This has led to the rise of several alternatives to Docker Desktop.

## Install Docker on a Mac with Docker Desktop

-   **Prerequisites:**
    -   macOS 10.15 or newer.
    -   At least 4 GB of RAM.
    -   To check, click the Apple icon > **About This Mac**.

-   **Method 1: GUI Installation (Direct Download)**
    1.  Go to `docker.com` and click the download button for your OS.
    2.  If you have an Apple Silicon Mac (M1/M2), be sure to select the **Apple Chip** option.
    3.  Open the downloaded `.dmg` file.
    4.  Drag the Docker icon into the Applications folder.
    5.  Open Docker using Spotlight (`Command` + `Space`, type "Docker", hit Enter).
    6.  Accept the security prompt and enter your password to allow the installation of backend components.
    7.  Accept the Docker license agreement.
    8.  The Docker whale icon in the menu bar will indicate its status. Boxes moving on its back means it's initializing; when the boxes stop, Docker is ready.

-   **Method 2: Homebrew Installation (CLI)**
    1.  Install Homebrew from `https://brew.sh` by pasting its installation command into your terminal.
    2.  Run the following command to install Docker Desktop:
        ```bash
        brew install docker --cask
        ```
    3.  The `--cask` option tells Homebrew to install a GUI application.

-   **Verification:**
    1.  Open your terminal.
    2.  Run the `hello-world` container to confirm the installation is working:
        ```bash
        docker run --rm hello-world
        ```
    3.  This command downloads the `hello-world` image, creates a container, runs its simple application (which prints a "Hello from Docker" message), and the `--rm` flag automatically removes the container after it exits.

## Install Docker on Windows with Docker Desktop

-   **Prerequisites:**
    -   System requirements are listed on the Docker website.

-   **WSL 2 vs. Hyper-V Backend:**
    -   During installation, you must choose a backend to run the Docker engine.
    -   **Hyper-V:** The traditional backend, which runs Docker in a standard virtual machine.
    -   **WSL 2 (Windows Subsystem for Linux):** The *recommended* backend. It runs a real Linux kernel directly within Windows without full virtualization, offering much better performance. Docker runs "natively" in this environment.

-   **Installation Steps:**
    1.  Go to `docker.com` and download the `.exe` installer.
    2.  Run the installer. At the security prompt, confirm the publisher is **Docker Inc.**
    3.  In the configuration step, ensure **"Use WSL 2 instead of Hyper-V"** is checked.
    4.  The installation may require a system restart.
    5.  Launch Docker Desktop from the Start Menu.
    6.  Accept the Docker Subscription Service Agreement.
    7.  **Potential Error:** You may see a "WSL 2 installation is incomplete" error. If so, click the link in the error message to install the required WSL update, then click **Restart** in Docker Desktop.
    8.  **Status Indicators:**
        -   The status bar in the bottom-left of the GUI will turn from orange (starting) to **green** (running).
        -   The whale icon in the system tray will stop showing moving boxes.

-   **Verification:**
    1.  Open **PowerShell**.
    2.  Run `docker ps` to test connectivity to the Docker engine. You should see an empty list of containers.
    3.  Run the `hello-world` container:
        ```bash
        docker run hello-world
        ```
    4.  A successful run will display the "Hello from Docker" message.

## Install Docker on Linux

-   **Key Concept:** On Linux, Docker runs natively. You do **not** need Docker Desktop. You install the **Docker Engine** and the Docker CLI directly.

-   **Installation Steps (Ubuntu Example):**
    1.  Ensure `cURL` is installed: `sudo apt install curl`
    2.  Use `cURL` to download the official installation script:
        ```bash
        curl -o /tmp/get-docker.sh https://get.docker.com
        ```
    3.  **(Security Best Practice):** It's always a good idea to inspect scripts downloaded from the internet before executing them.
    4.  Run the installation script:
        ```bash
        sh /tmp/get-docker.sh
        ```

-   **Post-Installation: Running Docker without `sudo`**
    1.  **The Problem:** By default, you must use `sudo` to run any `docker` command, which is inconvenient.
    2.  **The Solution:** Add your user to the `docker` group.
    3.  Run the command, replacing `linkedin` with your username or simply using the `$USER` variable:
        ```bash
        sudo usermod -aG docker $USER
        ```
        -   `-a`: Append the user to the group.
        -   `-G`: Specify the group name (`docker`).
    4.  **Applying Changes:** For the group membership to take effect, you must **log out and log back in**.
    5.  **Workaround (for immediate testing):** You can start a new shell with the updated permissions: `sudo -u $USER sh`.

-   **Verification:**
    1.  After applying the group change (either by logging out/in or using the workaround), open a new terminal.
    2.  Confirm you are in the `docker` group by running `groups`.
    3.  Run the `hello-world` container **without `sudo`**:
        ```bash
        docker run hello-world
        ```
    4.  This confirms the installation and user permissions are set up correctly.

***

# 3. Using Docker

## Exploring the Docker CLI

-   The **Docker Command Line Interface (CLI)** is the most popular way to interact with Docker.
-   **Terminal Access:**
    -   **Mac:** Use Spotlight (`Command` + `Space`), type `Terminal`, and press Enter.
    -   **Windows:** Use the Start Menu to search for and open **PowerShell**.
-   **Command Structure:** Most Docker commands follow a straightforward pattern:
    ```
    docker [TOP-LEVEL COMMAND] [SUB-COMMAND] [OPTIONS/FLAGS]
    ```
    -   **Top-Level Commands:** `login`, `run`, `pull`, `network`, etc.
    -   **Sub-Commands:** Some top-level commands have nested commands (e.g., `docker network create`).
-   **The Most Useful Flag: `--help`**
    -   The `--help` flag can be used with *any* Docker command or sub-command to get information on how to use it.
    -   It provides a usage summary and a list of all supported options.
    -   **Examples:**
        -   `docker --help`: Shows all top-level commands.
        -   `docker network --help`: Shows sub-commands for the `network` command (`connect`, `create`, etc.).
        -   `docker network create --help`: Shows all options for creating a network.
    -   **Pro-Tip:** If you're ever stuck, just add `--help` to your command.

## Create a Docker container

This section covers the "long way" of creating a container to understand the individual steps.

-   **Container Images:**
    -   A container image is a compressed, pre-packaged file system containing an application, its environment, and a starting instruction called an **entry point**.
    -   If an image isn't on your local machine, Docker will pull it from a registry (by default, **Docker Hub**).

-   **Step 1: Create a Container (`docker container create`)**
    -   This command creates a container from an image but **does not start it**.
    -   It requires an image name and an optional **tag** (e.g., `:linux`).
    -   **Command:**
        ```bash
        docker container create hello-world:linux
        ```
    -   The command returns the new container's unique **ID**.

-   **Step 2: List Containers (`docker ps`)**
    -   `docker ps` by default only shows *actively running* containers.
    -   To see all containers (including stopped or created ones), use the `--all` flag.
        ```bash
        docker ps --all
        ```
    -   The container we just created will have a status of **Created**.

-   **Step 3: Start a Container (`docker container start`)**
    -   This command starts a previously created container using its ID.
    -   **Pro-Tip:** You only need to use the first few unique characters of the ID.
    -   **Command:**
        ```bash
        docker container start [CONTAINER_ID]
        ```
    -   The container's entry point will execute, but the output is not shown in the terminal. The container's status will change to **Exited (0)**, where `0` is a success code.

-   **Step 4: View Container Logs (`docker logs`)**
    -   This command retrieves the logs (standard output) from a container. This is how you see the output of a container that has already run.
    -   **Command:**
        ```bash
        docker logs [CONTAINER_ID]
        ```

-   **Attaching to a Container's Output**
    -   To see the output immediately upon starting, use the `--attach` flag.
        ```bash
        docker container start --attach [CONTAINER_ID]
        ```
    -   This is useful for seeing real-time output.
    -   **Key Point:** Containers are not deleted by default. You can start the same container multiple times.

## Create a Docker container: The short way

-   The `docker run` command is a convenient shortcut that combines the "long way" steps into a single command.

-   **The `docker run` Formula:**
    > `docker run` = `docker container create` + `docker container start` + `docker container attach`

-   **Command:**
    ```bash
    docker run hello-world:linux
    ```

-   **Behavior:**
    1.  Creates a new container from the `hello-world` image.
    2.  Starts the container.
    3.  Attaches your terminal to the container to show its output immediately.
    4.  It does **not** print the container ID by default. You must use `docker ps --all` to find it.

-   You can still use `docker ps` to see the container's exit code and `docker logs` to view its output again later.

## Create a Docker container from Dockerfiles, part 1

-   To create a container for your own application, you first need to build a custom Docker image using a **`Dockerfile`**.
-   A `Dockerfile` is a text file containing instructions for building a Docker image.

-   **Key `Dockerfile` Instructions:**
    -   `FROM`: Specifies the **base image** to build upon (e.g., `FROM alpine:latest`). This is the foundation of your new image.
    -   `LABEL`: Adds metadata to the image, such as the maintainer's name.
    -   `COPY`: Copies files from the **build context** (the directory you run the build from) into the container image.
    -   `RUN`: Executes a command *during the build process*. This is used to install software, create directories, or configure the image environment (e.g., `RUN apt-get update && apt-get install -y curl`).
    -   `USER`: Sets the user for subsequent `RUN`, `CMD`, and `ENTRYPOINT` instructions. This is a security best practice to avoid running as the `root` user.
    -   `ENTRYPOINT`: Defines the main command to be executed when a container is **started** from the image.

## Create a Docker container from Dockerfiles, part 2

-   **Step 1: Build the Image (`docker build`)**
    -   This command reads the `Dockerfile` and builds an image.
    -   **Key Option: `-t` or `--tag`**
        -   This assigns a human-readable name and optional tag to the image (e.g., `our-first-image:v1`). This is much more convenient than using the image ID.
    -   **The Build Context:**
        -   The last argument to `docker build` is the path to the build context. A `.` is used to specify the current directory.
    -   **Example Command:**
        ```bash
        # Build an image from the Dockerfile in the current directory
        # and tag it as 'our-first-image'
        docker build -t our-first-image .
        ```
    -   **How it Works:** Docker images are composed of **layers**. Each instruction in the `Dockerfile` creates a new intermediate image layer. The final image is a combination of all these layers.

-   **Step 2: Run the Container (`docker run`)**
    -   Once the image is built and tagged, you can run a container from it using the name you provided.
    -   **Command:**
        ```bash
        docker run our-first-image
        ```
    -   This will start a new container and execute the `ENTRYPOINT` defined in the `Dockerfile`.