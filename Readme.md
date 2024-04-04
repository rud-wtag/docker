# Table of Contents
1. [Why docker](#why-docker)
   - Containers vs. Virtual Machines
2. [Introduction to Docker](#introduction-to-docker)

3. [Docker Images](#docker-images)
   - Docker Hub
   - Pulling Images
   - Building Images
   - Dockerfile
      - Syntax
      - Instructions
      - Best Practices
4. [Docker Containers](#docker-containers)
   - Running Containers
   - Managing Containers
   - Lifecycle of Containers
   - Executing Commands in Containers
5. [Docker Networking](#docker-networking)
   - Default Networking
   - Docker Networks
   - Bridge Networking
   - Host Networking
   - Overlay Networking
6. [Docker Volumes](#docker-volumes)
   - Volume Types
   - Persistent Data
   - Managing Volumes
   - Sharing Data Between Containers
7. [Docker Compose](#docker-compose)
   - Defining Services
   - Managing Multi-Container Applications
   - Environment Variables
   - Networking in Docker Compose
   - Volumes in Docker Compose
   - Scaling Services
11. [Conclusion](#conclusion)
    - Recap of Key Concepts
    - Next Steps


# Why docker?
**Definition**: Docker is a software platform that allows us to build, test, and deploy applications quickly.

### Virtualization vs Containerization
!['image file'](arch.webp)

Let's talk about some core concept of virtualization and containerization: 
well all know about the **virtual machine** which is the virtualization or emulation of a computer system. Some characteristics of a virtual machines are:- 

- VMs emulate a physical computer and run an entire operating system (OS) stack.
- Each VM includes its own kernel, libraries, applications, and binaries.
- Hypervisors (such as VMware or VirtualBox) are used to manage VMs.
- VMs typically consume more resources due to the duplication of OS components.
- Startup time for VMs is relatively slower compared to containers.

A **container** is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.

Some characteristics of a containers are:- 

- Containers provide a lightweight and portable way to encapsulate application dependencies.
- Containers share the host OS kernel and only package the application code, libraries, and dependencies.
- Containers are highly efficient in terms of resource utilization and startup time.
- Isolation between containers is achieved through namespaces and control groups (cgroups).

SO, Docker manages the containers and it has some core feature like:
- Image
- Container
- Network
- Volume
- Dockerfile
- Docker compose file

### Docker Images
Docker images are built using the Dockerfile, which consists of a set of instructions that are required to containerize an application

#### Docker Hub

```bash
docker pull <image_name>:<tag>
```
#### Dockerfile
```Dockerfile
# Start with a base image
FROM <base_image>

# Set metadata for an image
LABEL <key>=<value>

# Run commands in the image
RUN <command>

# Copy files or directories into the image
COPY <source> <destination>

# Define environment variables
ENV <key>=<value>

# Expose ports
EXPOSE <port>

# Set the working directory
WORKDIR <directory>

# Specify a default command to run when the container starts
CMD ["executable", "parameter1", "parameter2"]
```

!['image file'](image.png)

```bash
docker build -t your_image_name:tag -f path/to/Dockerfile .
```

### Find all docker images

```bash
docker images
```

## Docker containers
Container is a running instance of an image.

### Run a docker image as container (or image running as a process)

```bash
docker run -d --name <container_name> <image_name>:<tag>
```

```bash
docker run -d -p host_machine_port:docker_container_port test_nginx
```

### Now, How can we found our webpage running inside docker nginx server?

http://localhost:port/


### How can we share our file system with docker container?

```bash
docker run -v /host/directory:/container/directory
```

### Find all docker running container

```bash
docker ps
```

### stopping and removing the containers

```bash
docker container stop <name_of_the_container/id_of_the_container>
docker container rm <name_of_the_container/id_of_the_container>
docker container rm -f <name_of_the_container/id_of_the_container>
```


# Docker Networking

**Bridge Networking**
Bridge networking, also known as the default Docker network, creates an internal bridge network on the host.

```bash
docker network create my_bridge_network
```

```bash
docker run -d --network=my_bridge_network --name=my_container1 my_image1
```

```bash
docker run -d --network=my_bridge_network --name=my_container2 my_image1
```

```bash
docker exec -it my_container1 ping my_container2
```

**Host Networking**
Host networking removes network isolation between the Docker container and the Docker host.

```bash
docker run -d --network=host --name=my_container my_image
```
**Overlay Networking**
Overlay networking facilitates communication between containers running on different Docker hosts or Swarm nodes.


# Docker volumes
Docker volumes are a feature of Docker that provide a way to persistently store and manage data in containers. By default, the data in a container is stored in an ephemeral, writable container layer. Removing the container also removes it's data.

### Types of volumes
- Named Volumes: These are managed volumes created and managed by Docker. They are stored in a location controlled by Docker and are independent of the container's lifecycle.

- Host-mounted Volumes: These volumes are directories on the Docker host that are mounted directly into the container. Changes made in the container are reflected on the host and vice versa.

- Anonymous Volumes: These are temporary volumes created by Docker when no explicit volume is specified. They are useful for storing temporary or non-persistent data.

### Manage volumes

```bash
docker volume create my_volume
```

```bash
docker volume ls
```

```bash
docker volume inspect my_volume
```

```bash
docker volume rm my_volume
```

# Docker compose file

```yml
version: '3'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.appserver
    command: "uvicorn app.main:app --reload --host 0.0.0.0 --port 80"
    volumes:
      - .:/app
    ports:
      - 8000:80
    depends_on:
      - db
    networks:
      - fast_api

  db:
    build:
      context: .
      dockerfile: Dockerfile.pgsql
    volumes:
      - psql_volume:/var/lib/postgresql/data
    networks:
      - fast_api

  pgadmin:
    build:
      context: .
      dockerfile: Dockerfile.pgadmin
    volumes:
      - pgadmin_volume:/var/lib/pgadmin
    ports:
      - "5050:80"
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@domain.com
      PGADMIN_DEFAULT_PASSWORD: admin
    networks:
      - fast_api

volumes:
  pgadmin_volume:
  psql_volume:

networks:
  fast_api:
```
### Build image, up and run the container with a single command
```bash
docker-compose up
```

### Stop or down all the container manage py docker compose file
```bash
docker-compose down
```

### Managing development and production containers

```bash
docker-compose -f docker-compose.dev.yml up
```

```bash
docker-compose -f docker-compose.prod.yml down
```

### Run command in the container

```bash
docker-compose exec -it <service-name> <bash/shell>
```