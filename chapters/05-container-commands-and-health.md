# 05. Container commands and health

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Build context, layers, and cache](./04-build-context-layers-and-cache.md) | [Notes index](../README.md) | [Next: Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md) |

## Inspect a running process

A container runs while its main process runs. The Docker CLI can show its state, output, resource use, and configuration.

~~~sh
docker container ls
docker container ls --all
docker container logs --tail 100 web-demo
docker container stats --no-stream web-demo
docker container top web-demo
docker container inspect web-demo
~~~

- `docker container ls` shows running containers.
- `--all` includes containers that have stopped.
- `logs` displays the main process output written to standard output and standard error.
- `stats` reports a current resource snapshot.
- `top` shows processes running inside the container.
- `inspect` returns detailed configuration and state as JSON.

Add `--follow` to `logs` to keep watching new output. Use `--since` to limit the time range.

~~~sh
docker container logs --follow --since 10m web-demo
~~~

Applications should write operational messages to standard output or standard error when the container runtime is expected to collect them. A file inside the container may be removed with its writable layer or may not be managed by the selected log driver.

## Run a one-off diagnostic command

Use `exec` to inspect a running container without replacing its main process:

~~~sh
docker container exec --interactive --tty web-demo sh
~~~

Use `exec` for a temporary diagnostic command, not as the normal way to start an application. The container's main process still controls whether the container is running.

## Understand container states and exit codes

A container can be created, running, paused, restarting, exited, or dead. The status is different from an application's health check result.

~~~sh
docker container inspect --format '{{.State.Status}}' web-demo
docker container inspect --format '{{.State.ExitCode}}' web-demo
~~~

A zero exit code normally means the main process finished successfully. A nonzero code indicates that it reported a failure. The correct interpretation depends on the application.

A container that exits quickly may have completed a one-off job, crashed, or received an invalid command. Read its logs and inspect its exit code before restarting it.

## Add a health check

A Dockerfile can define a health check that periodically tests whether the application responds as expected. This Nginx example uses the `wget` utility available in the image:

~~~dockerfile
FROM nginx:alpine

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD wget --quiet --tries=1 --spider http://127.0.0.1/ || exit 1
~~~

The command must return exit code zero when the check succeeds and nonzero when it fails. Choose a check that tests the useful behavior of the service without doing expensive work.

Inspect the health result:

~~~sh
docker container inspect --format '{{.State.Health.Status}}' web-demo
docker container inspect --format '{{json .State.Health}}' web-demo
~~~

Health status can be `starting`, `healthy`, or `unhealthy` once a health check is configured. A failed health check marks the container unhealthy. It does not stop or restart the container automatically.

Use the same check carefully in a Compose file. A check that tests a database port may prove that the port accepts connections but not that the application can complete a real request.

## Use restart policies for exited processes

A restart policy controls what Docker does when a container's main process exits or when the Engine restarts.

~~~sh
docker run --detach --name api --restart unless-stopped api-image:1.0
~~~

Common policies are:

| Policy | Behavior |
| --- | --- |
| `no` | Do not automatically restart. This is the default. |
| `on-failure` | Restart after a nonzero exit code. A retry limit can be supplied. |
| `always` | Restart when the container exits, including after an Engine restart. |
| `unless-stopped` | Restart after an exit unless the container was deliberately stopped. |

A health check only reports health. Restart policies respond to process exit and Engine restart conditions. If an unhealthy process should be replaced, the application or its deployment platform needs an explicit recovery decision.

Do not combine Docker restart policies with a separate host process manager for the same container. Competing controllers can cause confusing restarts.

## Stop a container cleanly

`docker stop` asks the main process to stop gracefully, then force-stops it after the timeout if it does not exit.

~~~sh
docker container stop web-demo
~~~

An application should handle its termination signal, stop accepting new work, finish or cancel in-flight work, and close connections. A Dockerfile should use the exec form of `CMD` or `ENTRYPOINT` so the application can receive the signal directly.

## Check health and logs together

When a service is not responding:

1. Check whether the container is running.
2. Read recent logs for startup errors.
3. Inspect its configured command, environment, ports, and mounts.
4. Check health status if the image defines a health check.
5. Verify the application responds from inside the container and through the published host port.
6. Review resource use if the process is slow or repeatedly exits.

A running status only means the container's main process is still running. It does not prove that the application can serve a useful request.

## Chapter summary

- Use `logs`, `inspect`, `top`, and `stats` to understand a container.
- The main process determines whether the container is running.
- A health check reports application health but does not restart the container by itself.
- Restart policies react to container exits and Engine restarts.
- Handle stop signals so the application can shut down cleanly.
- Check state, logs, health, and the actual application response together.

## Further reading

- [Container logs](https://docs.docker.com/reference/cli/docker/container/logs/)
- [Container inspect](https://docs.docker.com/reference/cli/docker/container/inspect/)
- [Container stats](https://docs.docker.com/reference/cli/docker/container/stats/)
- [Dockerfile HEALTHCHECK](https://docs.docker.com/reference/dockerfile/#healthcheck)
- [Restart policies](https://docs.docker.com/engine/containers/start-containers-automatically/)