# 02. Images, containers, and lifecycle

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Docker and containers](./01-containers-and-docker.md) | [Notes index](../README.md) | [Next: Dockerfile fundamentals](./03-dockerfile-fundamentals.md) |

## Image and container commands

An image is the starting point for a container. A container has its own identity and lifecycle, even when several containers are created from the same image.

| Task | Command |
| --- | --- |
| List local images | `docker image ls` |
| Download an image | `docker image pull IMAGE` |
| List running containers | `docker container ls` |
| List running and stopped containers | `docker container ls -a` |
| Show container details | `docker container inspect NAME` |
| Show container output | `docker container logs NAME` |
| Stop a running container | `docker container stop NAME` |
| Start a stopped container | `docker container start NAME` |
| Remove a stopped container | `docker container rm NAME` |

The shorter forms, such as `docker ps` and `docker stop`, are commonly used aliases. The longer forms make it clear whether the command acts on an image or a container.

## Understand `docker run`

The basic form is:

~~~sh
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
~~~

Docker creates a new container from the image and starts its configured command. If the image is not local, Docker first pulls it from its registry.

Run a web container for local testing:

~~~sh
docker run --name web-demo --detach --publish 127.0.0.1:8080:80 nginx:alpine
~~~

- `--name` gives the container a readable, unique name.
- `--detach` starts it in the background and returns control to the terminal.
- `--publish 127.0.0.1:8080:80` maps host port 8080 to container port 80 and binds it to the local machine.
- `nginx:alpine` selects the image and tag.

Open `http://127.0.0.1:8080` in a browser. Binding the host port to `127.0.0.1` keeps this local example off other network interfaces. A published port without a host address can be reachable through the host's network interfaces, depending on the environment.

Check the running container and its port mapping:

~~~sh
docker container ls
docker container port web-demo
docker container logs web-demo
~~~

Inspect the container configuration and state:

~~~sh
docker container inspect web-demo
~~~

The inspect command returns detailed JSON. Use `--format` to select one value when you need a short answer:

~~~sh
docker container inspect --format '{{.State.Status}}' web-demo
~~~

## Stop, start, and remove a container

Stopping sends a termination signal to the main process, then force-stops it if it does not exit within the configured timeout. The container still exists and can be started again.

~~~sh
docker container stop web-demo
docker container ls -a
docker container start web-demo
~~~

Starting a stopped container reuses that container's writable layer and configuration. It does not create a fresh container from the image.

Remove the test container when finished:

~~~sh
docker container stop web-demo
docker container rm web-demo
~~~

A running container cannot normally be removed without stopping it first. `docker container rm --force web-demo` stops and removes it in one step, so reserve that option for cases where forced removal is intended.

## Run a short-lived command

The `--rm` option removes a container after its main process exits. It is useful for a one-time command:

~~~sh
docker run --rm alpine:3 echo "Hello from a container"
~~~

The container is removed after printing the message. The image remains on the machine.

For an interactive shell, combine `--rm`, `--interactive`, and `--tty`:

~~~sh
docker run --rm --interactive --tty alpine:3 sh
~~~

Changes inside this temporary container disappear when it exits. Use a volume for files that need to remain available after the container is removed.

## Run a command inside an existing container

Use `exec` to start an additional process inside a running container:

~~~sh
docker container exec --interactive --tty web-demo sh
~~~

The command runs in the existing container. Exiting the shell does not stop the main container process.

Use `docker container cp` to copy a file between the host and a container. For application data that must persist, a volume is a better fit than relying on the container's writable layer.

## Understand image tags and container names

An image reference commonly has this form:

~~~text
registry.example.com/team/application:version
~~~

The registry, namespace, image name, and tag identify what Docker should pull. If the registry is omitted, Docker uses its default registry configuration. If the tag is omitted, Docker uses the `latest` tag.

A tag is a movable name. It can point to different image content over time. For repeatable releases, use a deliberate versioning policy and record the digest of the image that was tested.

Container names are unique on one Docker Engine. A stopped container still owns its name until it is removed. Use `docker container rename` to change a name, or remove the old container before reusing it.

## Clean up this exercise

Stop and remove the test container if it is still present:

~~~sh
docker container stop web-demo
docker container rm web-demo
~~~

List the remaining containers and images:

~~~sh
docker container ls -a
docker image ls
~~~

Removing a container does not normally remove its image. Remove an image only when no container still uses it and it is no longer needed. Be cautious with prune commands because they remove resources beyond the one named example.

## Chapter summary

- `docker run` creates and starts a new container.
- `docker start` reuses an existing stopped container.
- `docker stop` stops a process but keeps the container.
- `docker rm` removes the container and its writable layer.
- `docker image ls` and `docker container ls -a` inspect different object types.
- Published ports connect a host port to a container port.
- `--rm` removes a short-lived container after its process exits.

## Further reading

- [docker run](https://docs.docker.com/reference/cli/docker/container/run/)
- [List containers](https://docs.docker.com/reference/cli/docker/container/ls/)
- [Start and stop containers](https://docs.docker.com/reference/cli/docker/container/stop/)
- [Inspect a container](https://docs.docker.com/reference/cli/docker/container/inspect/)
- [Container logs](https://docs.docker.com/reference/cli/docker/container/logs/)
- [List images](https://docs.docker.com/reference/cli/docker/image/ls/)
- [Image digests](https://docs.docker.com/dhi/core-concepts/digests/)