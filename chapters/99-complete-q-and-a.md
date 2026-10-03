# 99. Complete questions and answers

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: All code samples](./98-all-code-samples.md) | [Notes index](../README.md) | End of notes |

This question set reviews the key ideas and decisions across the Docker study notes. Use the chapter links to revisit a topic and try the related commands in a disposable environment.

## Docker and containers

### 1. What does Docker Engine do?

Docker Engine builds images and runs containers. It includes a client, a daemon that manages Docker objects, and APIs through which the client communicates with the daemon.

### 2. What is a container image?

An image is a packaged, read-only template made from filesystem layers and image configuration. Docker uses it to create containers.

### 3. What is a container?

A container is an isolated process created from an image with runtime settings such as networking, mounts, and resource limits. It has a writable layer, but that layer is not a durable data store.

### 4. Do containers include their own operating system kernel?

No. Containers share the host kernel. An image contains user-space files such as application code, runtime libraries, and tools.

### 5. How is a container different from a virtual machine?

A virtual machine runs a guest operating system on virtualized hardware. A container isolates processes while sharing the host kernel, so it usually starts with less overhead but does not provide a separate kernel boundary.

## Images, containers, and lifecycle

### 6. What does `docker pull` do?

It downloads an image from a registry to the local Docker image store. It does not start a container.

### 7. What is the difference between `docker run` and `docker start`?

`docker run` creates a new container from an image and starts it. `docker start` starts a container that already exists.

### 8. What is the difference between stopping and removing a container?

Stopping ends its main process but leaves the container record available to inspect or restart. Removing deletes the stopped container and its writable layer.

### 9. Why should I give a container a name?

A name gives commands a readable target, for example `docker logs api` or `docker stop api`, instead of requiring a container ID.

### 10. Why can a container exit right after starting?

A container runs while its main process is running. If that process finishes or fails, the container stops. Use a foreground application process as the container command.

## Dockerfile fundamentals

### 11. What is a Dockerfile?

A Dockerfile is a text file of instructions that tells the builder how to create an image, including its base image, files, dependencies, default user, and startup command.

### 12. What does `FROM` do?

`FROM` selects the base image for a build stage. It is usually the first instruction in a stage and may appear more than once in a multi-stage Dockerfile.

### 13. What is the purpose of `WORKDIR`?

`WORKDIR` sets the working directory for later Dockerfile instructions and the default working directory for the container. It avoids relying on an uncertain current directory.

### 14. How are `COPY` and `RUN` different?

`COPY` transfers files from the build context or another stage into the image. `RUN` executes a command during the image build.

### 15. What is the difference between `CMD` and `ENTRYPOINT`?

`ENTRYPOINT` defines the main executable, while `CMD` provides default arguments or a default command. Runtime arguments can replace `CMD`; the exact override behavior depends on how both instructions are used.

## Build context, layers, and cache

### 16. What is the build context?

The build context is the set of files made available to the builder for a build. The final path in `docker build -t app .` selects the current directory as the context.

### 17. What does `.dockerignore` do?

It excludes matching files from the build context. Use it to keep items such as `.git`, local dependencies, test output, and secrets out of a build.

### 18. How does the build cache help?

The builder can reuse results from unchanged instructions and their inputs. This avoids repeating work such as downloading dependencies on every build.

### 19. Why copy package manifests before application source?

Dependency manifests change less often than source files. Installing dependencies before copying frequently edited source makes the dependency layer reusable when only source changes.

### 20. What is the difference between `--no-cache` and `--pull`?

`--no-cache` reruns build steps without reusing cached layers. `--pull` checks for a newer base image. They address different parts of the build and can be used together.

## Container commands and health

### 21. What is the difference between `EXPOSE` and `--publish`?

`EXPOSE` records a container port in image metadata. `--publish` creates a host-to-container port mapping when a container starts.

### 22. Why should I use `--detach`?

`--detach` starts a container in the background and returns control of the terminal. Read its output later with `docker logs`.

### 23. What does a health check report?

A health check runs a configured probe and records whether the container is healthy, starting, or unhealthy. It does not fix the application or automatically restart an unhealthy process.

### 24. When should I use a restart policy?

Use a restart policy when Docker should try to start a container again after it exits. It helps with process recovery but does not replace monitoring, health checks, or a release rollback plan.

### 25. What does `docker exec` do?

It runs a command in an already running container. Use it for inspection or diagnosis, not as a substitute for configuring the container's normal startup command.

## Persistent data with volumes and mounts

### 26. Why is a container's writable layer temporary?

The writable layer belongs to that container. Removing the container removes data stored only there, so important application data should live in a volume or external data service.

### 27. When should I use a named volume?

Use a named volume for data that Docker should manage independently of a container, such as database files. The volume remains when the container is replaced.

### 28. When should I use a bind mount?

Use a bind mount when a container needs access to a specific host file or directory, commonly for local development. Its path and permissions depend on the host.

### 29. What happens when a mount covers a directory in the image?

The mounted content appears at that path and hides the image's existing files there for as long as the mount is active. Check the mount source and destination when files unexpectedly disappear.

### 30. Does `docker compose down` delete named volumes?

By default, it removes the Compose containers and network, but leaves named volumes. Adding `--volumes` removes Compose-managed volumes and can delete their data.

## Container networking

### 31. What does publishing a port do?

It maps a host port to a port in a container. For example, `-p 127.0.0.1:8080:3000` forwards local host port 8080 to container port 3000.

### 32. What does `EXPOSE` do for networking?

It documents a port used by a container process and adds image metadata. It does not create a host port mapping by itself.

### 33. How do two services on a user-defined network find each other?

They can usually connect by the other container's name or Compose service name and the port on which that service listens inside the network.

### 34. Why does `localhost` fail when one container calls another?

Inside a container, `localhost` refers to that same container. Use the destination service name on a shared Docker network.

### 35. Why bind a development port to `127.0.0.1`?

Binding to `127.0.0.1` limits access to the local host. Publishing a port without a host address commonly binds it to all host interfaces.

## Docker Compose fundamentals

### 36. What is Docker Compose?

Docker Compose defines and manages an application made of one or more services using a YAML configuration file and the `docker compose` command.

### 37. What is the difference between a service and a container?

A service is a desired application component in the Compose configuration. Compose creates and manages one or more containers for that service.

### 38. What does `docker compose up` do?

It creates or updates the resources described by the Compose file and starts the services. Without `--detach`, it also displays their logs in the terminal.

### 39. What does `docker compose down` do?

It stops and removes the project's containers and network. Named volumes are kept unless `--volumes` is requested.

### 40. Why keep the Compose file with the project?

It records service images or builds, ports, environment, mounts, and network relationships in a repeatable form that can be reviewed and used by other environments.

## Compose configuration and services

### 41. What is the difference between `.env` interpolation and `env_file`?

A project `.env` file supplies values that Compose can substitute into its configuration. `env_file` and `environment` place selected values into a container's environment.

### 42. What does `depends_on` guarantee?

It controls service startup and shutdown order. By default, it does not guarantee that a dependency is ready to handle requests. A health condition can add a readiness check, and applications should still handle temporary connection failures.

### 43. What are Compose profiles for?

Profiles make optional services, such as a local debugger or test helper, start only when requested. Services without a profile are enabled by default.

### 44. How do Compose secrets differ from ordinary environment values?

Secrets are granted to selected services and mounted for those services to read. They reduce accidental exposure through ordinary environment configuration, but the exact storage and security behavior depends on the deployment platform.

### 45. Why give a service a health check?

It lets Docker report whether a service's configured readiness probe succeeds. Another service can use that state for startup ordering, but the health check does not replace connection retries in the application.

## Registries and image publishing

### 46. What is a container registry?

A registry stores images so users and deployment systems can push, pull, and share them. Repositories within a registry organize related image names.

### 47. What does an image tag identify?

A tag is a readable name attached to an image reference, such as `notes-api:1.0.0`. Tags can be moved to different image content, so a tag alone is not always an immutable identity.

### 48. What is an image digest?

A digest is a content-derived identifier for an image manifest. It can identify exact image content more precisely than a mutable tag.

### 49. What does `docker push` require?

The target image must be tagged with the registry and repository name where the user has permission to publish it. The Docker client must also be authenticated when that registry requires sign-in.

### 50. Why should a release use an intentional tag instead of `latest`?

A version tag makes it clearer which release is being deployed and reviewed. The `latest` tag is not a guarantee that the image is the newest stable or secure version.

## Multi-stage builds and image optimization

### 51. What is a multi-stage build?

A multi-stage build uses more than one `FROM` instruction. Earlier stages can build or test the application, while the final stage contains the files needed at runtime.

### 52. What does `COPY --from=build` do?

It copies selected files from a stage named `build` into the current stage. This allows the runtime image to omit compilers and intermediate files.

### 53. Which stage is built by default?

The last stage in the Dockerfile is the default output. Use `docker build --target stage-name` to request a different named stage.

### 54. Why should dependency installation happen before copying frequently changed source?

The dependency layer can remain cached when source changes but dependency manifests do not. This shortens many rebuilds.

### 55. Is the smallest possible base image always best?

No. A smaller image can reduce transfer size, but it must still support the application's libraries, native modules, diagnostics, and operational needs. Test the actual runtime on the selected base image.

## Container security

### 56. Why run an application as a non-root user?

It limits the privileges of the application process if it is compromised. The image must give that user permission to read application files and write only to required locations.

### 57. What does `--read-only` change?

It mounts the container's root filesystem as read-only. Applications that need temporary or persistent writes must receive an appropriate writable temporary mount or volume.

### 58. Why avoid `--privileged`?

It gives a container broad additional access to host devices and capabilities. Grant only the specific permission a workload needs.

### 59. Why should build secrets not use `ARG` or `ENV`?

Build arguments and environment configuration can appear in image metadata, history, or build records. Use BuildKit secret or SSH mounts for credentials needed during a build.

### 60. Who should be allowed to control the Docker daemon?

Only trusted users and services. Access to a rootful daemon can allow powerful operations on the host, including access to host files through container mounts.

## Logs, debugging, and troubleshooting

### 61. How do I read recent logs and follow new output?

Use `docker logs --tail 100 container-name` for recent output and add `--follow` to stream new lines.

### 62. What should I check when a container stops?

Check `docker ps --all`, the container exit code and state, and `docker logs`. Confirm that the main process is configured correctly and has its required files and settings.

### 63. How can I tell whether Docker killed a container for memory?

Inspect the container state for `OOMKilled` and compare its use with configured memory limits. `docker stats` can show current use while the container is running.

### 64. How can I inspect published ports and mounts?

Use `docker port container-name` for port mappings and `docker inspect` to review mounts and other settings. Prefer formatted output for a specific field when full inspection output contains unnecessary details.

### 65. Where do I look if the client cannot connect to Docker?

Check `docker context show`, `docker version`, and `docker info`. Then check whether Docker Desktop or the Docker daemon is running and review daemon logs for the host platform.

## Containerizing an application

### 66. Why must a service listen on `0.0.0.0` inside a container?

It makes the server listen on the container's network interfaces. Listening only on the container's loopback address can prevent access through a published port or from another container.

### 67. How do I build the sample Node service?

Run `docker build --tag notes-api:1.0.0 .` from the directory containing the Dockerfile and application file. The final dot selects that directory as the build context.

### 68. How do I reach the sample service from the host?

Start it with `--publish 127.0.0.1:8080:3000`, then request `http://localhost:8080`. The host port is 8080 and the container port is 3000.

### 69. Does a published port make the application ready?

No. It creates a network mapping. The process still has to start successfully, listen on the mapped container port, and return a valid response.

### 70. What should be checked before removing a container?

Confirm that important data is stored outside the container's writable layer and that no process still needs the container. Then stop and remove it; remove its image separately only when appropriate.

## Local development and test workflows

### 71. What does Compose Watch do?

It observes selected files in the build context and syncs changes or rebuilds a service according to configured rules. It is intended for services built from local source.

### 72. When should a file change trigger a rebuild instead of a sync?

Use a rebuild when the image contents or installed dependencies must change, such as after editing a Dockerfile or dependency manifest. Sync ordinary source changes when the running application can use them.

### 73. Why should host `node_modules` usually stay out of a container?

Packages may include native files tied to the host's operating system or architecture. Install dependencies in the container image for the container's runtime.

### 74. What is a Compose profile useful for?

A profile lets the same Compose project include optional services such as a test runner or debugging tool without starting them during the ordinary development workflow.

### 75. How can a one-off Compose test command be run?

Use `docker compose run --rm service-name command`. It runs a new one-off container with that service's configuration and removes it after the command exits.

## Production deployment and operations

### 76. Does Compose provide cluster failover on one host?

No. A single-host Compose deployment manages containers on that host. It does not by itself reschedule workloads on another host if the server fails.

### 77. What does `restart: unless-stopped` provide?

Docker restarts a container after it exits unless an operator has explicitly stopped it. It does not fix unhealthy application behavior, deploy a corrected image, or provide service failover.

### 78. How should container resource limits be selected?

Measure CPU and memory use under representative load, then set limits with enough headroom for normal work and startup. Revisit the limits when workload behavior changes.

### 79. What makes an image update easier to roll back?

Publish each release under an intentional immutable or carefully controlled image reference and keep the previous known-good image available. Confirm that data migrations remain compatible with the application version used for rollback.

### 80. What should be included in a useful backup plan?

Back up application data and the configuration required to restore it, protect the backup, and test restoring it. A backup that has never been restored is not a verified recovery plan.

## Further reading

- [Docker Study Notes index](../README.md)
- [Docker documentation](https://docs.docker.com/)
- [Docker CLI reference](https://docs.docker.com/reference/cli/docker/)