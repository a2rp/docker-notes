# 07. Container networking

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md) | [Notes index](../README.md) | [Next: Docker Compose fundamentals](./08-compose-fundamentals.md) |

## How containers connect

A container has its own network interfaces and network namespace. Docker networks connect containers to one another and, when configured, to other networks.

For containers on the same user-defined bridge network, Docker provides name-based discovery. A container can reach another by its container name or network alias instead of depending on a changing IP address.

## Create a user-defined bridge network

Create a network, start Redis on it, then run a one-off Redis client on that same network:

~~~sh
docker network create app-net
docker run --detach --name cache --network app-net redis:7-alpine
docker run --rm --network app-net redis:7-alpine redis-cli -h cache ping
~~~

The client resolves `cache` through Docker's network DNS and should print `PONG`. The Redis port does not need to be published to the host for another container on this same network to connect to it.

List and inspect the network:

~~~sh
docker network ls
docker network inspect app-net
~~~

When finished, remove the container and network:

~~~sh
docker container rm --force cache
docker network rm app-net
~~~

## Connect a running container to a network

A container can be connected to or disconnected from a user-defined network while it is running:

~~~sh
docker network connect app-net cache
docker network disconnect app-net cache
~~~

The named network must exist first, and the container must exist. Networks are useful for limiting which groups of containers can discover and reach one another.

## Publish a container port to the host

A port published with `--publish` maps a host address and port to a container port. Run a local web server on the loopback interface:

~~~sh
docker run --detach --name web --publish 127.0.0.1:8080:80 nginx:alpine
~~~

Open `http://127.0.0.1:8080`. The mapping means host port 8080 forwards to port 80 in the container.

~~~text
HOST_IP:HOST_PORT:CONTAINER_PORT
127.0.0.1:8080:80
~~~

If the host IP is omitted, Docker normally binds the published port to all host addresses. Bind to `127.0.0.1` for a local development service that should only be reachable from the Docker host.

`EXPOSE` in a Dockerfile documents an application port. It does not publish the port on the host. Containers on the same user-defined network can communicate on listening ports without host publishing.

## Keep service ports private when possible

An application and a database can share a user-defined network. Publish only the port that must be reached from outside that network, such as the web application's host port.

~~~sh
docker network create app-net
docker run --detach --name cache --network app-net redis:7-alpine
docker run --detach --name web --network app-net --publish 127.0.0.1:8080:80 nginx:alpine
~~~

Containers attached to `app-net` can reach the Redis service at `cache:6379`. A client on the host can reach the web service at `127.0.0.1:8080`. Redis does not need a published host port for this example.

A container's `localhost` means that container itself. It is not the host and does not refer to another container. Use the other container's network name to connect to its service.

## Compare common network drivers

| Driver | Typical use |
| --- | --- |
| `bridge` | Connect containers on one Docker Engine host. User-defined bridges provide name-based discovery. |
| `host` | Share the host network namespace where supported. Port publishing does not apply. |
| `none` | Run a container without normal network interfaces. |
| `overlay` | Connect services across multiple Docker Engine hosts in a Swarm. |

Start with a user-defined bridge for containers on one host. Host and overlay networking have different isolation and deployment behavior, so use them only when the application needs those properties.

## Troubleshoot a connection

Check the container state, network membership, and service process:

~~~sh
docker container ls
docker network inspect app-net
docker container logs cache
docker container exec cache redis-cli ping
~~~

If name resolution fails, confirm both containers are attached to the same user-defined network and use the current container name or alias. If the connection works inside the network but not from the host, check the published port and the host IP address.

Do not copy a container's current IP address into application configuration. Container addresses can change when containers are recreated.

## Chapter summary

- User-defined bridge networks connect containers on the same Engine and provide DNS by name.
- Use service names or network aliases instead of fixed container IP addresses.
- Container-to-container connections use the listening container port.
- `--publish` maps a container port to a host port.
- Bind published development ports to loopback when they should stay on the host.
- `EXPOSE` documents a port but does not publish it.
- Keep internal service ports private when host access is not needed.

## Further reading

- [Docker networking overview](https://docs.docker.com/engine/network/)
- [Bridge networks](https://docs.docker.com/engine/network/drivers/bridge/)
- [Publishing ports](https://docs.docker.com/engine/network/port-publishing/)
- [Network drivers](https://docs.docker.com/engine/network/drivers/)
- [Create and manage networks](https://docs.docker.com/reference/cli/docker/network/)