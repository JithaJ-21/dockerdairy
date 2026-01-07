## Why Docker Exists — The Problem It Solves

Before Docker, developers used to say: “It works on my machine.”

Applications depend on specific versions of languages, libraries, and configurations. When you move code from a developer’s laptop to another system (testing, staging, or production), differences in environment cause failures.

**Traditional Solutions**:
* Virtual Machines (VMs): Each app runs inside its own OS.
 * ✅ Isolated environments
 * ❌ Heavy (need full OS per VM)
 * ❌ Slow to boot
 * ❌ Hard to distribute

**Docker’s Solution**:

Docker packages the application + dependencies + environment configs into a container that runs anywhere the same way.

It’s lightweight, portable, and fast because it shares the host OS kernel.

Think of a container as a “mini, self-contained environment” that runs only what’s needed — not a full OS.

**How Docker Is Different from a VM??**
| Feature     | **Virtual Machine**                  | **Docker Container**                        |
| ----------- | ------------------------------------ | ------------------------------------------- |
| OS          | Each VM has its **own OS**           | All containers **share the host OS kernel** |
| Size        | Heavy (GBs)                          | Lightweight (MBs)                           |
| Boot Time   | Minutes                              | Seconds                                     |
| Performance | Slower (full virtualization)         | Faster (OS-level isolation)                 |
| Isolation   | Strong (hardware-level)              | Slightly weaker (process-level)(still safe for most apps)             |
|Ideal for| Running different OSs (Windows, Linux, etc.)| Running multiple apps on same OS efficiently|

**Analogy**:
* Virtual Machines = like owning multiple houses — each with its own walls, kitchen, and power supply.
* Docker Containers = like renting rooms in one big house — separate, but sharing the same foundation (OS kernel).

## Core Docker Concepts

1) **Image**
* A read-only blueprint of your application.
* They cannot run by themselves; Built using a Dockerfile. Example: docker pull ubuntu downloads the Ubuntu image.
* Contains your app, dependencies, and environment settings.
* Example: python:3.10, nginx:latest, or your custom myapp:v1.

2) **Container**
* A running instance of an image, like “mini-computers” running inside your system.
* It’s ephemeral — when stopped or deleted, its data vanishes (unless stored in volumes).
* You can run multiple containers from one image.
* Example: docker run -it ubuntu bash opens an interactive Ubuntu terminal inside the container; Containers are isolated from your host machine, so changes inside do not affect your computer.

| Feature   | Image                       | Container                  |
| --------- | --------------------------- | -------------------------- |
| Nature    | Read-only template          | Running, writable instance |
| Execution | Cannot run                  | Runs commands / apps       |
| Isolation | N/A                         | Fully isolated from host   |
| Multiples | One image → many containers | Each container independent |

3) **Dockerfile**
* A text file that defines how to build an image- a recipe that tells docker how to build an image.
* Each instruction creates a new layer in the image.
* Example: (within dockerfile)

       FROM ubuntu
       CMD ["echo", "Hello from Dockerfile"]
 * FROM ubuntu → Use Ubuntu image as base
 * CMD [...] → Command that runs when the container starts
   
Build and run: (in bash)

       docker build -t myimage .
       docker run myimage
Output:

Hello from Dockerfile

4) **Registry**
* A storage hub for Docker images.
* **Public**: Docker Hub
* **Private**: AWS ECR, GitHub Container Registry, etc.

5) **Docker Engine**
* The core runtime that runs and manages containers.
* It includes:
**Docker Daemon** (dockerd) – manages containers/images.
**Docker CLI** – command-line tool to talk to the daemon.

## Docker Architecture

![dock](https://github.com/JithaJ-21/dockerdairy/blob/main/image.png?raw=true)

The CLI sends commands → Daemon executes them → Containers/images created/managed.

## Networking & Volumes
### Docker Networking
* Docker provides isolated virtual networks for containers.
* Bridge (default): Containers on the same host communicate.
* Host: Container shares host network stack.
* None: No network.
* Overlay: Used in multi-host clusters (Swarm/Kubernetes).
* Example: (in bash)

      docker run -d --name web --network mynet nginx

### Docker Volumes
* Used for persistent storage.
* Example: (in bash)

      docker volume create mydata
      docker run -d -v mydata:/app/data myapp

So, even if the container is deleted, the data stays.

By default, containers are ephemeral — any changes vanish when stopped. Volumes allow persistence. 

### Docker Compose

Docker Compose is a tool for defining and running multi-container applications. Instead of running docker run multiple times for each container, you can define all services, networks, and volumes in a single YAML file and start them with one command.

Example: docker-compose.yml 

(within yaml)

     version: "3"
     services:
       web:
         image: nginx
         ports:
           - "8080:80"
       db:
         image: mysql
         environment:
           MYSQL_ROOT_PASSWORD: example
* services → defines all containers in the app
* web → Nginx container, exposed on host port 8080
* db → MySQL container with root password

Run: (in bash)

     docker-compose up -d
This launches both web and db containers together.
* -d → run in detached (background) mode
* Docker Compose automatically:
  * Creates containers for all services
  * Creates a default network so containers can talk to each other
  * Can attach volumes if defined
   
## Common Docker Commands

| Action                                      | Command                                           | Description                                    |
| ------------------------------------------- | ------------------------------------------------- | ---------------------------------------------- |
| **Check Docker version**                    | `docker --version`                                | Verify Docker is installed                     |
| **Check Docker info**                       | `docker info`                                     | Display system-wide info about Docker          |
| **Pull an image from Docker Hub**           | `docker pull ubuntu`                              | Download image from Docker registry            |
| **List local images**                       | `docker images`                                   | Show all images on your system                 |
| **List all containers (running + stopped)** | `docker ps -a`                                    | See all containers                             |
| **List running containers only**            | `docker ps`                                       | See active containers                          |
| **Run a container interactively**           | `docker run -it ubuntu bash`                      | Open a terminal inside the container           |
| **Run a container in detached mode**        | `docker run -d -p 3000:3000 myapp`                | Run container in background, map ports         |
| **Stop a container**                        | `docker stop <container_id>`                      | Gracefully stop a container                    |
| **Remove a container**                      | `docker rm <container_id>`                        | Delete a stopped container                     |
| **Remove an image**                         | `docker rmi <image_id>`                           | Delete a Docker image                          |
| **Build image from Dockerfile**             | `docker build -t myapp:v1 .`                      | Create image from Dockerfile in current folder |
| **Run container with a name**               | `docker run --name backend -d -p 3000:3000 myapp` | Assign custom name to container                |
| **View container logs**                     | `docker logs <container_id>`                      | See output or errors from container            |
| **Access a running container**              | `docker exec -it <container_id> bash`             | Open terminal in running container             |
| **Create a bridge network**                 | `docker network create my-network`                | Custom network for multiple containers         |
| **Connect container to network**            | `docker network connect my-network <container>`   | Link container to a network                    |
| **List networks**                           | `docker network ls`                               | Show all Docker networks                       |
| **Remove network**                          | `docker network rm <network>`                     | Delete a network                               |
| **Run container with volume**               | `docker run -v myvolume:/data -d mongo`           | Persist container data outside container       |
| **List volumes**                            | `docker volume ls`                                | Show all volumes                               |
| **Remove volume**                           | `docker volume rm <volume>`                       | Delete unused volume                           |
| **Push image to Docker Hub**                | `docker push myusername/myapp:v1`                 | Upload image to registry                       |
| **Login to Docker Hub**                     | `docker login`                                    | Authenticate to Docker Hub                     |
| **Inspect container details**               | `docker inspect <container>`                      | Detailed info about container configuration    |

## Real-World Use Cases
* Web App Deployment — Run Node.js, Django, or Flask apps anywhere.
* Microservices — Each service runs in its own container.
* Dev/Test Environments — Same setup for every developer.
* CI/CD Pipelines — Used in Jenkins, GitHub Actions, GitLab CI.
* Cloud Deployments — Works seamlessly with AWS ECS, Azure, GCP.

## Summary: Why Docker matters?
| Feature         | Benefit                     |
| --------------- | --------------------------- |
| Portability     | Runs everywhere             |
| Lightweight     | Faster than VMs             |
| Isolation       | No dependency conflicts     |
| Version Control | Image tagging & rollback    |
| Automation      | Build once, deploy anywhere |
