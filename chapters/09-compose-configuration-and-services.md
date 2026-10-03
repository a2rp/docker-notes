# 09. Compose configuration and services

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Docker Compose fundamentals](./08-compose-fundamentals.md) | [Notes index](../README.md) | [Next: Registries and image publishing](./10-registries-and-image-publishing.md) |

## Configure a service from environment values

Compose can substitute variables into the YAML configuration. A project-level `.env` file supplies values for that substitution:

~~~text
WEB_PORT=8080
NGINX_TAG=alpine
APP_ENV=development
~~~

Reference those values in `compose.yaml`:

~~~yaml
services:
  web:
    image: nginx:${NGINX_TAG:-alpine}
    ports:
      - "127.0.0.1:${WEB_PORT:-8080}:80"
    environment:
      APP_ENV: ${APP_ENV:-development}
~~~

The expressions after the colon provide defaults. A value from the shell or `.env` file can override them.

A project-level `.env` file is used for Compose interpolation. It does not automatically place every variable inside each container. Use `environment` to set selected values in the container.

## Understand `env_file` and `environment`

Use `env_file` when a service needs several variables from a file:

~~~yaml
services:
  api:
    image: example-api:1.0
    env_file:
      - ./api.env
~~~

Use `environment` for values that are visible and clear in the Compose file:

~~~yaml
services:
  api:
    image: example-api:1.0
    environment:
      LOG_LEVEL: info
      APP_ENV: development
~~~

When both set the same variable, `environment` takes precedence over `env_file`. Values interpolated from the shell or project `.env` file have their own precedence rules. Use `docker compose config` to inspect the resolved configuration, and remember that its output can include sensitive values.

Do not commit local credentials. Add `.env` and any private environment files to `.gitignore`. A checked-in `.env.example` can document variable names with safe sample values.

## Wait for a dependency to become healthy

Short-form `depends_on` controls startup order, but it does not wait for a dependency to be ready to accept requests. Give the dependency a health check and use the `service_healthy` condition when the dependent service must wait for it.

~~~yaml
services:
  api:
    build: .
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:17-alpine
    environment:
      POSTGRES_USER: app
      POSTGRES_DB: app
      POSTGRES_PASSWORD: ${DB_PASSWORD:?set DB_PASSWORD locally}
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER} -d $${POSTGRES_DB}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 20s
~~~

Compose waits for the database health check to pass before creating the dependent service. The doubled dollar signs defer variable expansion to the health-check shell inside the container.

`depends_on` is a startup rule, not an ongoing connection manager. Applications should retry database connections because a dependency can become unavailable after startup.

## Persist service data with a named volume

Declare a named volume at the top level, then mount it into the service that needs it:

~~~yaml
services:
  db:
    image: postgres:17-alpine
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
~~~

Compose creates and manages the volume. It remains after the container is replaced. Removing it with `docker compose down --volumes` deletes the stored data, so use that option only when deletion is intended.

## Separate services with networks

A service can join one or more named networks. Use separate networks to limit which services can reach each other:

~~~yaml
services:
  web:
    image: nginx:alpine
    networks:
      - edge
      - app

  api:
    image: example-api:1.0
    networks:
      - app
      - data

  db:
    image: postgres:17-alpine
    networks:
      - data

networks:
  edge:
  app:
  data:
~~~

Here, the web service can reach the API, and the API can reach the database. The web service is not attached to the database network.

Compose still gives services DNS names on a shared network. The API can connect to the database using host `db` and the database's container port.

## Add an optional service profile

Profiles are useful for tools that are needed only in some workflows, such as a database viewer or local debugging service.

~~~yaml
services:
  db-viewer:
    image: adminer
    profiles:
      - debug
    ports:
      - "127.0.0.1:8081:8080"
~~~

Start the normal project without the optional tool:

~~~sh
docker compose up --detach
~~~

Enable the optional profile when it is needed:

~~~sh
docker compose --profile debug up --detach
~~~

Services without a profile are included by default. A service with a profile is included only when that profile is enabled or the service is explicitly targeted.

## Grant a service access to a secret

A Compose secret is mounted as a file only into services that request it:

~~~yaml
services:
  api:
    image: example-api:1.0
    secrets:
      - db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
~~~

The application reads the secret at `/run/secrets/db_password`. Keep the source file out of version control and grant access only to the services that need it.

For local Compose use, a file-backed secret is a controlled mount from the host. It does not by itself provide an encrypted secret store or replace the secret management provided by a deployment platform.

## Check the resolved model before starting

Use `docker compose config` to validate the Compose file and see the resolved service model:

~~~sh
docker compose config
~~~

This can help find indentation errors, missing variables, invalid paths, and unexpected interpolation. The output may show resolved environment values, so do not paste it into a public issue or log without reviewing it first.

## Chapter summary

- `.env` supplies values for Compose interpolation; it is not automatically copied into every container.
- `environment` and `env_file` set variables inside a service container.
- Use a health check with `depends_on: condition: service_healthy` when readiness matters.
- Applications still need to recover if a dependency fails after startup.
- Named volumes preserve data outside a container.
- Networks control which services can communicate.
- Profiles make optional services available when requested.
- Compose secrets mount sensitive files only into explicitly granted services, but source files still need protection.

## Further reading

- [Compose interpolation](https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/)
- [Environment variable precedence](https://docs.docker.com/compose/how-tos/environment-variables/envvars-precedence/)
- [Startup order](https://docs.docker.com/compose/how-tos/startup-order/)
- [Compose secrets](https://docs.docker.com/reference/compose-file/secrets/)
- [Compose volumes](https://docs.docker.com/reference/compose-file/volumes/)
- [Compose profiles](https://docs.docker.com/reference/compose-file/profiles/)