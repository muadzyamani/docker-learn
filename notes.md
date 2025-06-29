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

## Interact with your container

-   **Long-Running Containers:** So far, we've used containers that exit immediately. Docker is also used for long-running processes like web servers.

-   **Building from a specific Dockerfile:**
    -   If your Dockerfile is not named `Dockerfile`, you must specify the file path using the `-f` or `--file` flag with `docker build`.
    -   **Example:** `docker build --file server.Dockerfile -t our-first-server .`

-   **Running a Server Container:**
    -   If you use `docker run` on a server image, your terminal will "hang" because `docker run` attaches your terminal to the container's output by default. The server process doesn't exit, so it holds control.
    -   Containers are not "interactive" by default, so you cannot send keystrokes (like `Ctrl+C`) to the process inside.

-   **Stopping a "Hung" Container:**
    1.  Open a **new terminal**.
    2.  Find the running container's ID with `docker ps`.
    3.  Forcefully stop the container using `docker kill [CONTAINER_ID]`.

-   **Running a Container in the Background (Detached Mode):**
    -   To run a container without it taking over your terminal, use the `-d` (detached) flag.
    -   **Command:** `docker run -d our-first-server`
    -   This starts the container and immediately returns your terminal prompt. You can then use `docker ps` to see that it's running.

-   **Executing Commands in a Running Container (`docker exec`):**
    -   `docker exec` allows you to run additional commands inside a container that is already running. This is extremely useful for troubleshooting.
    -   **Syntax:** `docker exec [CONTAINER_ID] [COMMAND]`
    -   **Example (get the time):** `docker exec [CONTAINER_ID] date`

-   **Starting an Interactive Shell in a Container:**
    -   You can use `docker exec` to open a terminal session (like `bash`) inside a container.
    -   You need two flags for this:
        -   `-i` or `--interactive`: Keeps the connection open to receive input.
        -   `-t` or `--tty`: Allocates a pseudo-terminal so your shell looks and acts correctly.
    -   These are almost always used together as `-it`.
    -   **Command:** `docker exec -it [CONTAINER_ID] bash`
    -   Once inside, you have a shell prompt from within the container. You can exit by typing `exit` or pressing `Ctrl+D`.

## Stopping and removing the container

-   **Problem:** By default, Docker does not remove stopped containers. They accumulate over time, creating "cruft" and taking up disk space.

-   **Stopping a Container (`docker stop`):**
    -   This command attempts a **graceful shutdown**. It sends a `SIGTERM` signal to the main process and waits (by default, up to 10 seconds) for it to stop cleanly.
    -   If you need to stop a container immediately, use `docker stop -t 0 [CONTAINER_ID]`. This is faster but can lead to data loss if the application is in the middle of a task.

-   **Removing a Container (`docker rm`):**
    -   Removes one or more *stopped* containers. It will error if you try to remove a running container.
    -   To stop and remove a running container in one step, use the `-f` (force) flag: `docker rm -f [CONTAINER_ID]`.

-   **Pro-Tip: Removing All Containers at Once:**
    -   You can combine `docker ps` and `docker rm` with a command-line utility called `xargs` to clean up all containers.
    -   **Command:**
        ```bash
        docker ps -a -q | xargs docker rm
        ```
    -   **Breakdown:**
        -   `docker ps -a -q`: Lists the IDs (`-q` for quiet) of *all* (`-a`) containers.
        -   `|`: A "pipe" that sends the output of the first command as input to the second.
        -   `xargs docker rm`: Takes the list of IDs and runs `docker rm` for each one.

-   **Removing Images (`docker rmi`):**
    -   To remove Docker images and free up significant disk space, use `docker rmi [IMAGE_NAME_OR_ID]`.
    -   This will fail if the image is currently being used by any container (even a stopped one).
    -   You must remove the dependent containers first, or use `docker rmi -f` to force removal.

## Binding ports to your container

-   **Concept:** **Port binding** (or publishing) allows you to map a network port on your host machine to a network port inside a container. This lets you access services like web servers running inside containers.
-   **The Flag:** Use the `-p` or `--publish` flag with `docker run`.
-   **Syntax and Mnemonic:**
    -   `-p [HOST_PORT]:[CONTAINER_PORT]`
    -   A helpful way to remember the order is **Outside:Inside**. The host port is "outside" the container, and the container port is "inside."
-   **Example:**
    1.  Start a web server container listening on port 5000: `docker run -d --name my-web-server our-web-server`
    2.  Trying to access `localhost:5000` from your browser will fail.
    3.  Stop and remove the container: `docker rm -f my-web-server`
    4.  Restart it with port binding: `docker run -d --name my-web-server -p 5001:5000 our-web-server`
    5.  Now, accessing `localhost:5001` in your browser will connect to port 5000 inside the container, and the site will load.
-   **Naming Containers:** The `--name` flag gives a container a human-readable name, which is easier to work with than a random ID.

## Saving data from containers

-   **Problem:** Containers are **ephemeral**. Any data created inside a container is lost forever when the container is deleted.
-   **Solution: Volume Mounting**
    -   This feature maps a folder (or file) on your host machine to a folder inside the container. Data written to that folder inside the container is actually written to the host's folder, making it persistent.
-   **The Flag:** Use the `-v` or `--volume` flag with `docker run`.
-   **Syntax and Mnemonic:**
    -   `-v [HOST_PATH]:[CONTAINER_PATH]`
    -   The order is again **Outside:Inside**.
-   **Example:**
    -   `docker run -v /tmp/container-data:/tmp ubuntu echo "Hello there." > /tmp/file`
    -   In this command, when the container writes to `/tmp/file`, the file is actually created on the host machine at `/tmp/container-data/file`. Even if the container is removed, the data remains on the host.
-   **Caveat (Mounting a single file):** If you map a host file that *does not exist*, Docker will create an empty **directory** on the host at that path instead of a file. This often causes errors inside the container.
    -   **Lesson:** When mounting files, ensure the source file on the host already exists.

## Introducing the Docker Hub

-   **Container Image Registry:** A place to store, manage, and distribute container images.
-   **Tags:** A way to version images. The format is `image-name:version` (e.g., `nginx:1.21`). If no version is specified, Docker uses the `latest` tag by default.
-   **Docker Hub:** The default, public registry used by Docker. It's like GitHub, but for Docker images.

## Pushing images to the Docker registry

1.  **Create an Account:** Register for a free account at `hub.docker.com`.
2.  **Log In from the CLI:** Use the `docker login` command and enter your Docker Hub username and password.
3.  **Tag the Image for the Registry:** Before you can push an image to Docker Hub, you must tag it in a specific format: `<DOCKER_HUB_USERNAME>/<IMAGE_NAME>:<VERSION>`.
    -   **Command:** `docker tag <local-image-name> <username>/<new-image-name>:<version>`
    -   **Example:** `docker tag our-web-server carlosnunez/our-web-server:0.0.1`
4.  **Push the Image:** Use the `docker push` command with the newly tagged image name.
    -   **Command:** `docker push <username>/<image-name>:<version>`
    -   **Example:** `docker push carlosnunez/our-web-server:0.0.1`

## Checking your images in Docker Hub

-   You can view and manage your pushed images in your browser by logging into Docker Hub.
-   **Optimized Pushes:** Docker is smart about pushing. Images are made of layers. If some layers of your image already exist in the registry, Docker will not upload them again, making subsequent pushes much faster.
-   **Deleting an Image Repository:**
    -   This **cannot** be done from the command line.
    -   You must log in to the Docker Hub website, go to the repository's **Settings**, and click **Delete Repository**.

## Challenge & Solution: Starting NGINX

-   **Goal:** Serve a static website using the official NGINX image, with specific port and volume mappings, and ensure the container is automatically removed.
-   **The Final Command:**
    ```bash
    docker run --rm --name website -v "$PWD/website:/usr/share/nginx/html" -p 8080:80 nginx
    ```
-   **Breakdown of Flags:**
    -   `--rm`: Automatically removes the container when it stops.
    -   `--name website`: Names the container "website".
    -   `-v "$PWD/website:/usr/share/nginx/html"`: Mounts the `website` folder from the current directory (`$PWD`) into the default NGINX content directory inside the container.
    -   `-p 8080:80`: Maps host port 8080 to the container's port 80 (NGINX's default).
    -   `nginx`: The name of the image to use from Docker Hub.
-   **Verification:**
    1.  Access `http://localhost:8080` in a browser to see the website.
    2.  Go back to the terminal and stop the container with `Ctrl+C`.
    3.  Run `docker ps -a` to confirm that the "website" container is gone, proving that `--rm` worked.

***

# 4. When Things Go Wrong

## Help! I can't seem to create more containers

-   **The Problem:** You try to run a container and receive a "no space left on device" error, but checking your computer's disk space with `df -h` shows that you have plenty of space.

-   **The Cause:**
    -   On macOS and some Windows setups, the Docker engine runs inside a small virtual machine.
    -   The error message refers to the disk space *inside that VM*, not on your host computer.
    -   Over time, unused images, stopped containers, and intermediate build layers can fill up this dedicated space.

-   **The Solutions:**

    1.  **Remove Unused Images (`docker rmi`):**
        -   List all images with `docker images`.
        -   Identify images you no longer need and remove them.
        -   You can remove multiple images at once.
            ```bash
            docker rmi image-one image-two image-three
            ```
        -   **Note:** If an image is being used by a container (even a stopped one), you must first remove the container with `docker rm` or use `docker rmi -f` to force deletion.

    2.  **Prune the System (`docker system prune`):**
        -   This is a powerful command that automatically cleans up unused Docker resources.
        -   It will remove:
            -   All stopped containers.
            -   All networks not used by at least one container.
            -   All dangling images (intermediate layers not associated with a tagged image).
            -   All build cache.
        -   **Command:** `docker system prune`
        -   It will prompt for confirmation (`y/N`) because it is a destructive action.
        -   This command can often reclaim gigabytes of space.

## Help! My container is really slow

-   **Problem:** A container is running much more slowly than expected.

-   **Troubleshooting Commands:**

    1.  **`docker stats`:**
        -   Provides a live, real-time stream of performance metrics for running containers, including CPU usage, memory usage, and network I/O.
        -   **Usage:** Run `docker stats [CONTAINER_NAME_OR_ID]` to view stats for a specific container.
        -   This is excellent for seeing if a container is being "hammered" or hitting a resource limit.

    2.  **`docker top`:**
        -   Shows the processes running *inside* a container, much like the `top` command on Linux.
        -   This is useful for seeing exactly what command or application is running without having to `exec` into the container.
        -   **Usage:** `docker top [CONTAINER_NAME_OR_ID]`

    3.  **`docker inspect`:**
        -   Outputs a large, detailed JSON object containing all configuration and state information about a container.
        -   This is great for deep dives and for scripting.
        -   **Usage:** `docker inspect [CONTAINER_NAME_OR_ID]`
        -   **Pro-Tip:** Pipe the output to `less` to view it page by page: `docker inspect [CONTAINER_NAME_OR_ID] | less`.
        -   You can search within `less` (e.g., by typing `/Mounts`) to find specific configuration details like mounted volumes, restart policies, network settings, and more.

## Challenge: Fix a broken container

-   **Goal:** A provided `Dockerfile` and script build a broken container. The challenge is to fix both so the container runs successfully and immediately prints a series of messages.
-   **Hints:**
    -   Use `docker run -it` to run interactively.
    -   Use `docker ps` and `docker rm` if you get stuck.

## Solution: Fix a broken container

-   **Problem 1: Docker Build Fails**
    -   **Symptom:** The `docker build` command fails with an error: "pull access denied for ubuntu:xeniall, repository does not exist or may require 'docker login'".
    -   **Investigation:** Checking Docker Hub for the `ubuntu` image reveals that the tag should be `xenial` (with one 'l'), not `xeniall`. It was a typo.
    -   **Fix:** Edit the `Dockerfile` and change `FROM ubuntu:xeniall` to `FROM ubuntu:xenial`. Rebuild the image.

-   **Problem 2: Container Runs Very Slowly**
    -   **Symptom:** The container starts but processes messages very slowly, whereas the prompt said it should be immediate.
    -   **Investigation:**
        1.  Open a second terminal and run `docker stats [CONTAINER_NAME]`. This reveals that the CPU usage is at 100%.
        2.  Run `docker top [CONTAINER_NAME]`. This shows that a program called `yes` is running inside the container.
        3.  The `yes` command is a known utility that prints 'y' repeatedly, consuming 100% of a CPU core. This is the source of the slowness.
    -   **Fix:**
        1.  Edit the script file (`app.sh`).
        2.  Find and delete the line containing the `yes` command.
        3.  Save the file.
        4.  **Rebuild the Docker image** with `docker build` to include the change.

-   **Problem 3: Container Name Conflict**
    -   **Symptom:** After fixing the script and rebuilding, running the container again fails with an error: "container name... is already in use by container...".
    -   **Cause:** The previous, slow-running container was still present (either running or stopped) and was occupying the name.
    -   **Fix:** Remove the old container using `docker rm [CONTAINER_NAME]`, then re-run the `docker run` command.
    -   **Result:** The fixed container now runs immediately and successfully.

***

# 5. Additional Docker Resources

## Docker best practices

Three key best practices to consider as you move your apps into Docker.

### 1. Use Verified Images and Image Scanners

-   **The Risk:** It is very easy to download malicious Docker images from public registries like Docker Hub. These images can be used to steal credentials, run crypto miners, or cause other harm.
-   **Example:** An image called `Alpine 2` was uploaded to Docker Hub. It ran Alpine Linux but also secretly ran a crypto miner, which could lead to huge cloud bills from network traffic.
-   **Solution 1: Use Verified Images**
    -   Look for the **"Official Image"** or verified publisher designation on Docker Hub. This means the image has been scanned and vetted by Docker.
-   **Solution 2: Use Image Scanners**
    -   Since many safe images are not verified, use a container image scanning tool.
    -   These tools inspect each layer of an image for known vulnerabilities or malicious files.
    -   **Examples of Open Source Scanners:** Clair, Trivy, Dagda.

### 2. Always Use Version Tags (Avoid `latest`)

-   **The Problem:** By default, if you don't specify a tag, Docker uses `latest`. This is problematic for production use for three reasons:
    1.  **Ambiguity:** You don't know which specific version of an application you are getting.
    2.  **Unpredictability:** The `latest` tag can change. Pulling the same image later might give you a different, potentially breaking version without you knowing.
    3.  **Rollback Difficulty:** If you only use `latest`, it's hard to roll back to a specific, known-good previous version.
-   **Solution:** Always explicitly add a version tag when building images (e.g., `my-app:1.2.3`). It's a simple practice that prevents major problems later.

### 3. Run Containers as a Non-Root User

-   **The Risk:** By default, containers run as the `root` user, which has full administrative privileges inside the container. This is a significant security risk.
-   **Solution 1: For Existing Images**
    -   Use the `--user` flag with `docker run` or `docker container create`.
    -   You can specify a user name (e.g., `nobody`) or a Linux user ID (e.g., `1001`).
    -   **Command:** `docker run --user "nobody" [IMAGE_NAME]`
-   **Solution 2: For Your Own Images**
    -   Use the `USER` instruction in your `Dockerfile` to set a default non-root user for the container to run as.
    -   **Example:**
        ```dockerfile
        # ... other instructions ...
        RUN addgroup -S appgroup && adduser -S appuser -G appgroup
        USER appuser
        ENTRYPOINT ["/app/start"]
        ```

## Taking it to the next level with Docker Compose

-   **The Problem:** Docker is great for single applications, but modern apps often have multiple components (e.g., a web front-end, a backend API, a database). Running many `docker` CLI commands to connect them all with networks and volumes can be tedious.
-   **The Solution: Docker Compose**
    -   A tool for defining and running multi-container Docker applications.
    -   You define all your services, networks, and volumes in a single YAML file called `docker-compose.yml`.
    -   You can start the entire application stack with a single command: `docker-compose up`.
    -   This is extremely helpful for local development and for running integration or end-to-end tests.

## Level up even more with Kubernetes

-   **The Problem:** Docker and Docker Compose work well on a single machine, but they have limitations when running applications at scale across multiple machines (hosts):
    -   Docker networks don't span multiple hosts by default.
    -   There's no built-in solution for moving containers between hosts (failover) or for auto-scaling based on load.
    -   Higher-level concerns like advanced traffic routing and load balancing are not included.
-   **The Solution: Container Orchestrators**
    -   Tools that manage containerized applications across a cluster of machines. They handle scheduling, scaling, networking, and service discovery.
    -   **Examples:** Docker Swarm, Mesosphere, HashiCorp Nomad, AWS ECS.
-   **Kubernetes: The Leading Orchestrator**
    -   An open-source, "planet-scale" container orchestrator for automating deployment, scaling, and management.
    -   **Key Features:**
        1.  **Distributed System:** Designed to run across many machines, making it highly resilient and scalable. It can connect hundreds of thousands of containers as if they were on one giant machine.
        2.  **Grouping and Scaling:** Allows you to group containers (in "Pods") and easily scale them up or down in response to demand, without needing to manage individual VMs.
        3.  **Advanced Networking and Security:** Provides powerful tools to secure traffic between containers and manage how traffic is routed to them from the outside world (e.g., based on URL paths).
        4.  **Extensible Platform:** Kubernetes has a massive ecosystem of tools and products that extend its functionality, making it incredibly flexible.
-   **Analogy:** If Docker is the box that contains everything needed to make a meal, Kubernetes is the global delivery service that can clone, ship, and manage millions of those boxed meals worldwide.