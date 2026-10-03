# 15. Local development and test workflows

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Containerizing an application](./14-containerizing-an-application.md) | [Notes index](../README.md) | [Next: Production deployment and operations](./16-production-deployment-and-operations.md) |

## Use the same runtime locally

Running the application in a container gives the development environment a defined Node version and operating system image. It does not guarantee that every production behavior is identical, but it reduces differences in the runtime and avoids requiring Node to be installed on the host.

Continue with the `notes-api` files from the previous chapter. Add a `compose.yaml` file:

~~~yaml
services:
  app:
    build: .
    command: ["node", "--watch", "server.mjs"]
    ports:
      - "127.0.0.1:8080:3000"
    develop:
      watch:
        - action: sync+restart
          path: ./server.mjs
          target: /app/server.mjs
        - action: rebuild
          path: ./Dockerfile

  check:
    image: node:22-alpine
    profiles:
      - test
    depends_on:
      - app
    command:
      - node
      - -e
      - |
        fetch("http://app:3000/health").then(async (response) => {
          console.log(await response.text());
          if (!response.ok) process.exitCode = 1;
        }).catch((error) => {
          console.error(error);
          process.exitCode = 1;
        });
~~~

The `app` service builds from the current directory and runs Node's file watch mode. Compose Watch synchronizes `server.mjs` into the running container, then restarts the service. A Dockerfile change triggers a rebuild instead.

The `check` service uses Node's built-in `fetch` to request the app through the Compose network. It has a `test` profile, so it does not start with the ordinary development service.

Compose Watch requires Docker Compose 2.22.0 or later. Confirm the installed Compose version with `docker compose version`.

## Start the app and watch for changes

From the directory containing `compose.yaml`, run:

~~~sh
docker compose up --watch app
~~~

Compose builds and starts the app, then watches the files selected by the rules. Edit and save `server.mjs`; Compose syncs it and restarts the process. Open `http://localhost:8080` to see the response.

Stop the attached process with `Ctrl+C`, or start in detached mode:

~~~sh
docker compose up --detach --watch app
~~~

When running detached, follow the service output separately:

~~~sh
docker compose logs --follow app
~~~

## Run a repeatable service check

Start the application before running the check service:

~~~sh
docker compose up --detach app
docker compose run --rm check
~~~

The check container exits with a non-zero status if the HTTP request fails or receives a non-success response. `--rm` removes the one-off container when it exits. This is useful locally and in a continuous integration job because the same command can run after building the application.

The service check depends on the app being ready before it makes the request. `depends_on` controls startup order, but a running process is not always ready to receive requests. In this workflow, start the app first and confirm it is healthy if startup takes time. For an automated wait, add a real health check and a bounded retry to the check command.

## Keep host files and container files separate

Bind mounts and Compose Watch solve different development needs. A bind mount makes a host path visible inside a container. Compose Watch copies only the paths selected by its rules.

Do not sync host `node_modules` into a container. Packages can contain native files built for the host operating system or CPU architecture. Let the image install dependencies for its own runtime.

Be aware that a bind mount over `/app` hides the files copied into `/app` when the image was built. If the container suddenly cannot find its files, check the mount destination and host directory contents.

## Use profiles for optional tools

Services without a profile start by default. Services with a profile start only when you enable that profile or explicitly target the service.

Start the check service directly:

~~~sh
docker compose run --rm check
~~~

Or start all services assigned to the `test` profile:

~~~sh
docker compose --profile test up
~~~

Use profiles for optional test runners, debuggers, or local administration tools. Keep essential services such as the app and database unprofiled when they should always start with the project.

## Stop the development environment

Stop and remove the Compose containers and network:

~~~sh
docker compose down
~~~

This does not remove named volumes unless you also pass `--volumes`. Treat volume removal as data deletion and check what the project stores before using that option.

## Sources

- [Use Compose Watch](https://docs.docker.com/compose/how-tos/file-watch/)
- [Using profiles with Compose](https://docs.docker.com/compose/how-tos/profiles/)
- [docker compose run](https://docs.docker.com/reference/cli/docker/compose/run/)