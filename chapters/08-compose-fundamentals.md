# 08. Docker Compose fundamentals

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Container networking](./07-container-networking.md) | [Notes index](../README.md) | [Next: Compose configuration and services](./09-compose-configuration-and-services.md) |

## What Compose provides

Docker Compose describes a multi-container application in a YAML file. It lets you keep the services, ports, networks, and other runtime settings together, then manage the application as one project.

Compose is a CLI that uses the Docker Engine. A Compose file is a declarative description of the containers to create, not a replacement for images or the Engine.

The default file name is `compose.yaml`. Compose also accepts `compose.yml` and legacy `docker-compose.yaml` or `docker-compose.yml` names. The top-level `version` field is obsolete in the current Compose Specification, so new files can omit it.

## Define two services

Create `compose.yaml`:

~~~yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "127.0.0.1:8080:80"

  cache:
    image: redis:7-alpine
~~~

The file declares two services:

- `web` runs Nginx and publishes its container port 80 on local host port 8080.
- `cache` runs Redis without publishing its port to the host.

Compose creates a project network by default. The services can reach one another by service name on that network. For example, an application in the `web` service can connect to Redis at host `cache` and container port `6379`.

## Start and inspect the project

Run these commands from the directory that contains `compose.yaml`:

~~~sh
docker compose config --quiet
docker compose up --detach
docker compose ps
~~~

`config --quiet` checks and resolves the Compose configuration without printing the expanded file. `up --detach` creates and starts the services in the background. If a service has a `build` section, Compose builds the image when needed.

Open `http://127.0.0.1:8080` to see the Nginx welcome page. The Redis service is on the Compose network but is not published on a host port.

Check the service logs:

~~~sh
docker compose logs
docker compose logs --follow cache
~~~

Use `exec` to run a command in a running service container:

~~~sh
docker compose exec web sh
~~~

Exit the shell to return to the host terminal. The Nginx container keeps running because its main process is separate from the shell.

Use `run` for a one-off container based on a service definition. This Redis client resolves the `cache` service name through the project network:

~~~sh
docker compose run --rm cache redis-cli -h cache ping
~~~

The command should print `PONG`. `run` creates a new temporary service container for the command. `exec` runs a command inside an existing running container.

## Stop the project and clean up

Stop and remove the project containers and its default network:

~~~sh
docker compose down
~~~

Named volumes declared by the project are kept by default. Add `--volumes` only when you intend to delete the Compose-managed volume data:

~~~sh
docker compose down --volumes
~~~

Review the command before using `--volumes` on any project with important data.

## Run one service or choose a file

Start a single service and any services it depends on:

~~~sh
docker compose up --detach web
~~~

Pass a specific Compose file with `--file`:

~~~sh
docker compose --file compose.yaml up --detach
~~~

Compose paths and relative file references are interpreted from the project directory. Keep the Compose file and its related configuration together so the paths are clear.

## Read a Compose file from top to bottom

The top-level `services` map defines the parts of the application. Each service describes an image or a build, and optional runtime configuration such as ports, environment, storage, and networks.

Compose uses the service name for DNS on its default project network. Container IP addresses can change when a service is recreated, so configure applications to connect by service name.

Use the modern `docker compose` command with a space. The hyphenated `docker-compose` command belongs to older installations.

## Common first errors

- **Compose cannot find a file:** Run the command in the directory with `compose.yaml`, or pass its path with `--file`.
- **Invalid YAML:** Check indentation and ensure list items align under their keys.
- **Port is already allocated:** Choose a free host port or stop the other service using it.
- **A service cannot reach another:** Confirm both services share a network and use the service name with the container port.
- **The page is not reachable from another device:** A `127.0.0.1` host binding is intentionally limited to the Docker host.
- **Data disappeared after `down`:** Check whether the service used a named volume and whether it was removed with `--volumes`.

## Chapter summary

- Compose defines related services in a single YAML file.
- The current file format uses the Compose Specification and does not need a top-level `version`.
- `docker compose up --detach` creates and starts the project.
- Services on the default project network can reach each other by service name.
- `exec` runs inside an existing service container; `run` creates a one-off container.
- `docker compose down` removes containers and networks; `--volumes` also deletes named data.

## Further reading

- [Docker Compose overview](https://docs.docker.com/compose/)
- [How Compose works](https://docs.docker.com/compose/intro/compose-application-model/)
- [Compose file reference](https://docs.docker.com/reference/compose-file/)
- [Compose networking](https://docs.docker.com/compose/how-tos/networking/)
- [docker compose CLI](https://docs.docker.com/reference/cli/docker/compose/)
- [docker compose down](https://docs.docker.com/reference/cli/docker/compose/down/)