# 01. Docker and containers

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| Start of notes | [Notes index](../README.md) | [Next: Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md) |

## What Docker does

Docker packages an application and its required files into an image, then runs that image as one or more containers. A container gives a process an isolated view of resources such as its filesystem, network, and process list.

The same image can be run on different machines that have a compatible container runtime. This helps make development, testing, and deployment environments more repeatable.

## Images, containers, and the Engine

An image is a built package used to create containers. It is made from read-only layers. A container is a running or stopped instance of an image, with a small writable layer for changes made while it runs.

Changes in that container layer are temporary. Removing the container removes those changes. Store data that must outlive a container in a volume or another persistent service.

The Docker client is the `docker` command that you type. It sends requests to a Docker Engine API. The Engine daemon, usually `dockerd`, manages images, containers, networks, and volumes. Docker Desktop provides the Engine and developer tools for Windows and macOS; Linux installations use the Engine packages for that distribution.

~~~text
Docker CLI
    |
    | Docker Engine API
    v
Docker daemon
    |
    +-- images
    +-- containers
    +-- networks
    +-- volumes
~~~

A build service such as BuildKit turns a build context and Dockerfile into an image. The Engine then creates a container from that image. These are related parts of Docker, but an image build and a container run are separate operations.

## Containers and virtual machines

| Container | Virtual machine |
| --- | --- |
| Isolates application processes while sharing the host operating system kernel. | Runs a guest operating system on virtualized hardware. |
| Usually starts with only the application environment needed by its processes. | Includes a full guest operating system image. |
| Often has a smaller image and starts quickly. | Usually uses more resources because it runs a guest operating system. |
| Needs a compatible kernel and container runtime. | Can run a different guest kernel through the hypervisor. |

Containers are not virtual machines, and they are not an automatic security boundary. Configure resource limits, permissions, network access, and secrets for the application. Security details are covered in chapter 12.

## Install Docker for the current platform

Choose the official installation path for the operating system:

- On Windows and macOS, install Docker Desktop and start it before using the CLI.
- On Linux, install Docker Engine and the Compose plugin for the chosen distribution.
- In managed environments, use the Docker context and Engine endpoint provided by the platform.

Follow the official system requirements and installation steps. Do not copy package commands from a different operating system.

## Check the installation

Run these commands in a terminal:

~~~sh
docker --version
docker compose version
docker version
docker info
~~~

`docker --version` reports the client version. `docker version` normally reports both client and server details. If it shows only client details or cannot connect, the CLI may be installed while the Engine is stopped or unreachable.

`docker info` shows details about the Engine selected by the active Docker context. It is useful when a machine has more than one Engine endpoint.

## Run a first container

The `hello-world` image prints a message and exits:

~~~sh
docker run --rm hello-world
~~~

Docker looks for the image locally first. If it cannot find it, Docker downloads it from the configured registry, creates a container, and starts the program. The `--rm` option removes the stopped container when the program exits. The downloaded image remains available locally.

Try an interactive shell in a small Linux image:

~~~sh
docker run --rm -it alpine:3 sh
~~~

Inside the container, inspect the environment and then leave:

~~~sh
cat /etc/os-release
exit
~~~

The `-i` option keeps standard input open, and `-t` gives the process a terminal. The `sh` argument replaces the image's default command for this run. The `--rm` option removes the container after the shell exits.

A moving image tag is convenient for a short exercise. For a repeatable deployment, select a specific supported version or image digest and update it through a tested process.

## Know which Docker endpoint a command uses

A Docker command acts on the Engine selected by the active context. Start by checking the context when an image or container seems to be missing:

~~~sh
docker context ls
docker context show
~~~

The default context commonly points to the local Engine. A different context can point to another endpoint, so commands can affect a different host than expected.

## First checks when a command fails

- Confirm Docker Desktop or the Linux Engine is running.
- Read the complete error from `docker version` or `docker info`.
- Check the active context with `docker context show`.
- Confirm the user can access the Engine endpoint.
- Recheck the image name and registry if a pull fails.

## Chapter summary

- An image is a package; a container is an instance created from that image.
- The Docker client sends requests to an Engine endpoint.
- Containers isolate processes while sharing the host kernel.
- Container writable-layer changes disappear when the container is removed.
- Volumes and other services hold data that must persist.
- `docker run --rm hello-world` verifies that the client can reach an Engine and start a container.

## Further reading

- [Docker overview](https://docs.docker.com/get-started/docker-overview/)
- [Docker Engine](https://docs.docker.com/engine/)
- [Docker Engine daemon](https://docs.docker.com/engine/daemon/)
- [Install Docker](https://docs.docker.com/get-started/get-docker/)
- [Docker CLI reference](https://docs.docker.com/reference/cli/docker/)