

## Using Docker Quiz

### **Question 1 of 14**
You have upgraded your application to a new version, `1.0.1`, and created an image for it called `my-app`. You successfully pushed the image with `docker push`. Upon logging into Docker Hub, you notice that `1.0.1` is not there but `1.0.0` is. What is the most likely cause for this happening?

*   You were temporarily offline while pushing `1.0.1` to Docker Hub.
*   Docker Hub only allows you to store one tag per image at a time.
*   **You ran `docker push $DOCKER_HUB_USERNAME/my-app:1.0.0` and forgot to run `docker push $DOCKER_HUB_USERNAME/my-app:1.0.1`. (Correct)**

---

### **Question 2 of 14**
You have upgraded your application to a new version, `1.0.1`, and created an image for it called `my-app`. Which of these commands should you run to make it easy for others to find on the Docker Hub?

*   `docker tag my-app $DOCKER_HUB_USERNAME/my-app:latest`
*   **`docker tag my-app $DOCKER_HUB_USERNAME/my-app:v1.0.1` (Correct)**
*   `docker tag my-app my-app:latest`
*   `docker tag my-app my-app:v1.0.1`

---

### **Question 3 of 14**
Select the **least true** statement from the list.

*   Docker Hub is the default container registry used by the Docker client.
*   By default, Docker images will receive a `latest` version tag if a version tag is not provided.
*   **Anyone can push to Docker Hub. You do not need to register an account. (Correct)**
*   Anyone can pull from the Docker Hub without an account.

---

### **Question 4 of 14**
Why would you want to mount a directory to your container?

*   **Because containers do not save data after they are deleted. (Correct)**
*   So that your container can access the Internet using the host's network sockets.
*   To improve disk IO performance.
*   So that the container can create directories.

---

### **Question 5 of 14**
You have just installed Docker to begin moving an application that your team supports into containers. You tried running a CLI command but were shown text similar to the below:
```
docker [COMMAND]" requires at least 1 argument.
See 'docker [COMMAND] --help'.

Usage: docker [COMMAND] [OPTIONS] [ARG...]
```
What is the most likely cause for this happening?

*   **You forgot to provide additional options or arguments to the command. Run `--help` to find out what you missed. (Correct)**
    > That's right! Commands that require extra options or arguments will show you an abbreviated help page when they are missing. Run `--help` to see what you missed.
*   You forgot to provide a subcommand. Run `--help` to see what you missed.
*   The CLI is not configured properly. Edit `/etc/docker/docker.json` to fix.
*   The CLI command is missing a plugin. Visit docker.com to learn how to install it.

---

### **Question 6 of 14**
Which of these three commands is `docker run` a combination of?

*   `docker container create`, `docker container start`, `docker container inspect`
*   `docker container create`, `docker container start`, `docker ps`
*   **`docker container create`, `docker container start`, `docker container attach` (Correct)**
*   `docker container create`, `docker ps`, `docker container attach`

---

### **Question 7 of 14**
Which of these statements about `docker container start` is true?

*   You must run `docker rm` before running `docker container start`.
*   **You can run `docker container start` against a container multiple times. (Correct)**
*   You can only run `docker container start` against a container once. To re-run, delete the container first.
*   You must run `docker run` before running `docker container start`.

---

### **Question 8 of 14**
You are helping a colleague learn Docker. They said that they created a Docker container from the `nginx` Docker image, but it does not show up after running `docker ps`. They confirmed that they could successfully pull the `nginx` image from Docker Hub. What is the most likely cause and solution for this problem?

*   **They created the container with `docker container create` but forgot to start it. Run `docker container start` to fix. (Correct)**
    > That's right. `docker container create` creates containers but does not start them. You will need to use `docker container start` to execute the image's entrypoint.
*   The entrypoint for the `nginx` image they pulled is incorrect. Use `dfimage` to recreate and save its Dockerfile, change the entrypoint, and build a new image locally.
*   They need to run `docker pull` to pull the `nginx` image first before running `docker container create`.
*   They provided the wrong image to `docker container create`. Fix the image, then try again.

---

### **Question 9 of 14**
You are debugging a broken Jenkins pipeline that deploys your application into production. It creates Docker containers during the testing stage of the pipeline. You see this error message in the logs for the last build:
```
$: docker run --rm my-app:v7.2.1
Unable to find image 'my-app:7.2.1' locally
docker: Error response from daemon: manifest for my-app:7.2.1 not found: manifest unknown: manifest unknown.
See 'docker run --help'.
```
What is the most likely cause for this error?

*   The Jenkins runner node does not have enough disk space to pull new Docker images.
*   **Docker was not able to find a container image named `my-app:7.2.1` in Docker Hub or your private registry. (Correct)**
    > Correct. `docker run` first attempts to pull the Docker image from Docker Hub or your private registry if the image is not already installed on your system. You will only see this error message if Docker is unable to find the image.
*   The test stage forgot to run `docker container create` before executing `docker run`.
*   Docker was not fully installed on this Jenkins node.

---

### **Question 10 of 14**
What is the most correct explanation describing this command: `docker exec -i -t 2bf bash`

*   This starts an interactive Bash shell within a container with ID `2bf` with a pseudo-TTY allocated to it.
*   This starts a Bash shell within a container starting with ID `2bf` with a pseudo-TTY allocated to it.
*   This starts an interactive Bash shell within a container starting with ID `2bf`.
*   **This starts an interactive Bash shell within a container starting with ID `2bf` with a pseudo-TTY allocated to it. (Correct)**
    > Awesome job. `-i` is short for `--interactive`. `-t` is short for `--tty`. Therefore, this is precisely what this command is doing.

---

### **Question 11 of 14**
You started a container that ran a single command. That container has now exited. You would like to get the ID of this container for additional reporting. Which of these commands will successfully complete this task?

*   `docker ps`
*   `docker inspect`
*   `docker logs`
*   **`docker ps -a` (Correct)**
    > Nice. You need to add the `-a` or `--all` flag to see all containers on your system. `docker ps` only shows you containers that are running.

---

### **Question 12 of 14**
You are creating a Dockerfile for your web application that will be pushed into your company's private Docker registry. Your application is a NodeJS-based to-do list... Your terminal hangs while trying to start a container from this image. You can't seem to exit it, no matter how many keystrokes you provide. The application does not hang when you run it normally on your computer. What is the most likely cause for this happening?

*   This is a known bug in the `express` web server. Switch to `nginx` as your base image.
*   **You ran `docker run` without providing the `--interactive` option and cannot stop the web server hosting your web app. (Correct)**
    > Nice one. In order to provide keystrokes to your container, you need to provide `--interactive` to tell Docker to enable keystrokes for this container.
*   You have an infinite loop in your application somewhere that only happens when it is run within a Docker container.
*   You forgot to run `docker container start` after running `docker container create`.

---

### **Question 13 of 14**
When would you want to bind a port on your computer to that of a container?

*   When you want to save data from your container.
*   **When your container is running a network service that you want to use from your computer. (Correct)**
*   When your container needs access to the Internet.
*   When your container is running a network service that you want to use from within the container.

---

### **Question 14 of 14**
You are trying to remove 20 stopped containers with `docker ps -aq | xargs docker rm`. However, upon doing this, you receive the error below:
```
Error response from daemon: You cannot remove a running container 8dee53d96... Stop the container before attempting removal or force remove
```
Which of these commands fixes this problem most efficiently?

*   `docker system restart && docker ps -aq | xargs docker rm`
*   `docker container create --name 8dee... our-server && docker rm 8dee`
*   `for i in $(docker ps -aq); do docker rm -f $i; done`
*   **`docker ps -aq | xargs docker rm -f` (Correct)**