# 13. Logs, debugging, and troubleshooting

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Container security](./12-container-security.md) | [Notes index](../README.md) | [Next: Containerizing an application](./14-containerizing-an-application.md) |

## Start with the failing layer

First identify where the failure happens:

1. **Build:** the image does not build. Read the failing Dockerfile step and check the build context, files, and package output.
2. **Container start:** the image builds, but the process exits or fails its health check. Inspect container state and recent logs.
3. **Application request:** the process runs, but a request fails. Check port publishing, application configuration, and service-to-service networking.
4. **Host or daemon:** Docker commands cannot reach the engine, or containers cannot start. Inspect Docker Desktop or daemon status and logs.

This order helps separate a Docker configuration problem from an application problem.

## Read container logs

Applications running in containers should usually write operational logs to standard output and errors to standard error. The logging driver collects those streams.

~~~sh
docker logs --tail 100 api
docker logs --since 10m --follow api
docker logs --timestamps api
~~~

For a Compose project, inspect one service or follow all services:

~~~sh
docker compose logs --tail 100 api
docker compose logs --follow
~~~

If logs are empty, check that the application writes to the console, that the container uses a logging driver that supports local reads, and that the container name is correct. An application that writes only to a file inside the container will not automatically show that file in `docker logs`.

## Check container state and health

List containers, including those that already exited:

~~~sh
docker ps --all
docker compose ps --all
~~~

Inspect only the state fields useful for a first check:

~~~sh
docker inspect --format '{{.State.Status}} exit={{.State.ExitCode}} oom={{.State.OOMKilled}} error={{.State.Error}}' api
~~~

A non-zero exit code means the main process stopped with an error. `oom=true` means the container was stopped after using more memory than allowed or available. An empty health status can mean that no health check is configured.

For a container with a health check, inspect its current status:

~~~sh
docker inspect --format '{{.State.Health.Status}}' api
~~~

A health check reports whether the configured probe succeeds. It does not repair a failed application. Check the probe command, its expected port or path, and how long the application needs to start.

## Inspect events and resource use

Watch recent Docker events while reproducing the issue:

~~~sh
docker events --since 10m
~~~

Check a container's current CPU and memory use:

~~~sh
docker stats api
~~~

Events can reveal that a container was created, started, stopped, or killed. Resource usage helps distinguish an application error from a container approaching its configured limits.

## Check ports, mounts, and networking

Inspect the published host ports:

~~~sh
docker port api
~~~

A port mapping publishes a container port on the host. The application inside the container must listen on the container's network interface and the mapped container port. If the application listens only on `127.0.0.1` inside the container, other containers and Docker's port forwarding may not reach it.

Inspect the mounts when a file appears to be missing or an image path seems empty:

~~~sh
docker inspect --format '{{json .Mounts}}' api
~~~

A bind mount placed over a directory hides the image's existing files at that path. Check the host directory and the mount destination before changing the image.

On a Compose network, containers should usually reach one another by service name and container port. For example, an API can connect to a database at `db:5432`. `localhost` inside the API container refers to the API container itself, not the database.

## Check the Docker engine

If the Docker client cannot connect to an engine, check the active context and engine details:

~~~sh
docker context show
docker version
docker info
~~~

On a Linux host that uses systemd, inspect the daemon service:

~~~sh
sudo systemctl status docker
sudo journalctl -u docker.service --since "10 minutes ago"
~~~

On Docker Desktop for Windows with the WSL 2 backend, daemon logs are available under `%LOCALAPPDATA%\\Docker\\log\\vm\\init.log`. Docker Desktop also provides its own troubleshooting view. The exact log location depends on the platform and backend.

## Understand logging driver limits

Check the daemon's default logging driver:

~~~sh
docker info --format '{{.LoggingDriver}}'
~~~

The `json-file` driver is the default in many installations. Without log rotation, a chatty container can consume substantial disk space. Docker's `local` driver rotates logs by default. A daemon-level change applies to newly created containers; existing containers retain the logging configuration they were created with.

A remote logging driver may have different local-read behavior. Check the selected driver and its settings before assuming `docker logs` contains every record.

## Common symptoms and first checks

| Symptom | First checks |
| --- | --- |
| Container exits immediately | Read recent logs, exit code, and the configured command. |
| Container is running but unhealthy | Run the health probe inside the container and confirm the app is ready on the expected port. |
| Host cannot reach the service | Check `docker port`, host firewall rules, and the app's listening address. |
| One Compose service cannot reach another | Confirm both services share a network and use the service name plus container port. |
| A file exists in the image but disappears at runtime | Inspect bind mounts that may cover its directory. |
| Logs stop appearing | Check the app's output streams, logging driver, and daemon disk space. |
| Container is killed under load | Check `OOMKilled`, memory limits, and `docker stats`. |
| Docker commands cannot connect | Check the selected context, engine status, and daemon logs. |

## Sources

- [docker container logs](https://docs.docker.com/reference/cli/docker/container/logs/)
- [docker system events](https://docs.docker.com/reference/cli/docker/system/events/)
- [Configure logging drivers](https://docs.docker.com/engine/logging/configure/)
- [Read the daemon logs](https://docs.docker.com/engine/daemon/logs/)