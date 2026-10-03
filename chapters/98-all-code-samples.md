# 98. All code samples

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Production deployment and operations](./16-production-deployment-and-operations.md) | [Notes index](../README.md) | [Next: Complete questions and answers](./99-complete-q-and-a.md) |

This reference gathers every fenced code, command, and configuration example from the 16 core chapters. Each sample links back to the chapter where its explanation appears.

## [01. Docker and containers](./01-containers-and-docker.md)

[Read the chapter](./01-containers-and-docker.md)

### Sample 1 (text)

Source: [Chapter 01. Docker and containers](./01-containers-and-docker.md)

~~~~text
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
~~~~

### Sample 2 (sh)

Source: [Chapter 01. Docker and containers](./01-containers-and-docker.md)

~~~~sh
docker --version
docker compose version
docker version
docker info
~~~~

### Sample 3 (sh)

Source: [Chapter 01. Docker and containers](./01-containers-and-docker.md)

~~~~sh
docker run --rm hello-world
~~~~

### Sample 4 (sh)

Source: [Chapter 01. Docker and containers](./01-containers-and-docker.md)

~~~~sh
docker run --rm -it alpine:3 sh
~~~~

### Sample 5 (sh)

Source: [Chapter 01. Docker and containers](./01-containers-and-docker.md)

~~~~sh
cat /etc/os-release
exit
~~~~

### Sample 6 (sh)

Source: [Chapter 01. Docker and containers](./01-containers-and-docker.md)

~~~~sh
docker context ls
docker context show
~~~~

## [02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

[Read the chapter](./02-images-containers-and-lifecycle.md)

### Sample 1 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
~~~~

### Sample 2 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker run --name web-demo --detach --publish 127.0.0.1:8080:80 nginx:alpine
~~~~

### Sample 3 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker container ls
docker container port web-demo
docker container logs web-demo
~~~~

### Sample 4 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker container inspect web-demo
~~~~

### Sample 5 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker container inspect --format '{{.State.Status}}' web-demo
~~~~

### Sample 6 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker container stop web-demo
docker container ls -a
docker container start web-demo
~~~~

### Sample 7 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker container stop web-demo
docker container rm web-demo
~~~~

### Sample 8 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker run --rm alpine:3 echo "Hello from a container"
~~~~

### Sample 9 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker run --rm --interactive --tty alpine:3 sh
~~~~

### Sample 10 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker container exec --interactive --tty web-demo sh
~~~~

### Sample 11 (text)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~text
registry.example.com/team/application:version
~~~~

### Sample 12 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker container stop web-demo
docker container rm web-demo
~~~~

### Sample 13 (sh)

Source: [Chapter 02. Images, containers, and lifecycle](./02-images-containers-and-lifecycle.md)

~~~~sh
docker container ls -a
docker image ls
~~~~

## [03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

[Read the chapter](./03-dockerfile-fundamentals.md)

### Sample 1 (js)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~js
const http = require("node:http");

const server = http.createServer((request, response) => {
    response.writeHead(200, { "content-type": "text/plain" });
    response.end("Hello from a container\n");
});

server.listen(3000, "0.0.0.0");
~~~~

### Sample 2 (dockerfile)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~dockerfile
FROM node:22-alpine

WORKDIR /app
COPY server.js ./

USER node
EXPOSE 3000
CMD ["node", "server.js"]
~~~~

### Sample 3 (sh)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~sh
docker build --tag node-notes-app:dev .
~~~~

### Sample 4 (sh)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~sh
docker run --rm --publish 127.0.0.1:3000:3000 node-notes-app:dev
~~~~

### Sample 5 (sh)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~sh
curl http://127.0.0.1:3000
~~~~

### Sample 6 (dockerfile)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~dockerfile
FROM node:22-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
~~~~

### Sample 7 (dockerfile)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~dockerfile
CMD ["node", "server.js"]
~~~~

### Sample 8 (dockerfile)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~dockerfile
ENTRYPOINT ["node"]
CMD ["server.js"]
~~~~

### Sample 9 (dockerfile)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~dockerfile
EXPOSE 3000
~~~~

### Sample 10 (sh)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~sh
docker run --rm --publish 127.0.0.1:3000:3000 node-notes-app:dev
~~~~

### Sample 11 (dockerfile)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~dockerfile
WORKDIR /app
COPY server.js ./
USER node
CMD ["node", "server.js"]
~~~~

### Sample 12 (dockerfile)

Source: [Chapter 03. Dockerfile fundamentals](./03-dockerfile-fundamentals.md)

~~~~dockerfile
ARG APP_VERSION=dev
ENV NODE_ENV=production
~~~~

## [04. Build context, layers, and cache](./04-build-context-layers-and-cache.md)

[Read the chapter](./04-build-context-layers-and-cache.md)

### Sample 1 (sh)

Source: [Chapter 04. Build context, layers, and cache](./04-build-context-layers-and-cache.md)

~~~~sh
docker build --tag node-notes-app:dev .
~~~~

### Sample 2 (text)

Source: [Chapter 04. Build context, layers, and cache](./04-build-context-layers-and-cache.md)

~~~~text
node_modules
.git
.env
.env.*
!.env.example
coverage
dist
~~~~

### Sample 3 (dockerfile)

Source: [Chapter 04. Build context, layers, and cache](./04-build-context-layers-and-cache.md)

~~~~dockerfile
FROM node:22-alpine
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .
CMD ["node", "server.js"]
~~~~

### Sample 4 (sh)

Source: [Chapter 04. Build context, layers, and cache](./04-build-context-layers-and-cache.md)

~~~~sh
docker buildx build --progress=plain --tag node-notes-app:dev .
~~~~

### Sample 5 (sh)

Source: [Chapter 04. Build context, layers, and cache](./04-build-context-layers-and-cache.md)

~~~~sh
docker buildx build --pull --no-cache --tag node-notes-app:test .
~~~~

### Sample 6 (dockerfile)

Source: [Chapter 04. Build context, layers, and cache](./04-build-context-layers-and-cache.md)

~~~~dockerfile
# syntax=docker/dockerfile:1
FROM node:22-alpine
WORKDIR /app

COPY package.json package-lock.json ./
RUN --mount=type=cache,target=/root/.npm npm ci

COPY . .
CMD ["node", "server.js"]
~~~~

## [05. Container commands and health](./05-container-commands-and-health.md)

[Read the chapter](./05-container-commands-and-health.md)

### Sample 1 (sh)

Source: [Chapter 05. Container commands and health](./05-container-commands-and-health.md)

~~~~sh
docker container ls
docker container ls --all
docker container logs --tail 100 web-demo
docker container stats --no-stream web-demo
docker container top web-demo
docker container inspect web-demo
~~~~

### Sample 2 (sh)

Source: [Chapter 05. Container commands and health](./05-container-commands-and-health.md)

~~~~sh
docker container logs --follow --since 10m web-demo
~~~~

### Sample 3 (sh)

Source: [Chapter 05. Container commands and health](./05-container-commands-and-health.md)

~~~~sh
docker container exec --interactive --tty web-demo sh
~~~~

### Sample 4 (sh)

Source: [Chapter 05. Container commands and health](./05-container-commands-and-health.md)

~~~~sh
docker container inspect --format '{{.State.Status}}' web-demo
docker container inspect --format '{{.State.ExitCode}}' web-demo
~~~~

### Sample 5 (dockerfile)

Source: [Chapter 05. Container commands and health](./05-container-commands-and-health.md)

~~~~dockerfile
FROM nginx:alpine

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD wget --quiet --tries=1 --spider http://127.0.0.1/ || exit 1
~~~~

### Sample 6 (sh)

Source: [Chapter 05. Container commands and health](./05-container-commands-and-health.md)

~~~~sh
docker container inspect --format '{{.State.Health.Status}}' web-demo
docker container inspect --format '{{json .State.Health}}' web-demo
~~~~

### Sample 7 (sh)

Source: [Chapter 05. Container commands and health](./05-container-commands-and-health.md)

~~~~sh
docker run --detach --name api --restart unless-stopped api-image:1.0
~~~~

### Sample 8 (sh)

Source: [Chapter 05. Container commands and health](./05-container-commands-and-health.md)

~~~~sh
docker container stop web-demo
~~~~

## [06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

[Read the chapter](./06-persistent-data-volumes-and-mounts.md)

### Sample 1 (sh)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~sh
docker volume create notes-data
docker run --rm --mount type=volume,source=notes-data,target=/data alpine:3 sh -c 'echo "saved value" > /data/message.txt'
~~~~

### Sample 2 (sh)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~sh
docker run --rm --mount type=volume,source=notes-data,target=/data,readonly alpine:3 cat /data/message.txt
~~~~

### Sample 3 (sh)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~sh
docker volume inspect notes-data
docker container ls --all
~~~~

### Sample 4 (powershell)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~powershell
New-Item -ItemType Directory -Force .\site | Out-Null
Set-Content -Path .\site\index.html -Value '<h1>Mounted from the host</h1>'
$sitePath = (Resolve-Path .\site).Path
~~~~

### Sample 5 (powershell)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~powershell
docker run --rm --publish 127.0.0.1:8081:80 --mount "type=bind,source=$sitePath,target=/usr/share/nginx/html,readonly" nginx:alpine
~~~~

### Sample 6 (sh)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~sh
docker run --rm --mount type=tmpfs,target=/run/cache alpine:3 sh -c 'echo temporary > /run/cache/value.txt && cat /run/cache/value.txt'
~~~~

### Sample 7 (sh)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~sh
docker run --rm --mount type=volume,source=notes-data,target=/data alpine:3
~~~~

### Sample 8 (sh)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~sh
docker run --rm --volume notes-data:/data alpine:3
~~~~

### Sample 9 (sh)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~sh
docker run --rm --volume /host/path:/container/path:ro nginx:alpine
~~~~

### Sample 10 (sh)

Source: [Chapter 06. Persistent data with volumes and mounts](./06-persistent-data-volumes-and-mounts.md)

~~~~sh
docker volume rm notes-data
docker volume ls
~~~~

## [07. Container networking](./07-container-networking.md)

[Read the chapter](./07-container-networking.md)

### Sample 1 (sh)

Source: [Chapter 07. Container networking](./07-container-networking.md)

~~~~sh
docker network create app-net
docker run --detach --name cache --network app-net redis:7-alpine
docker run --rm --network app-net redis:7-alpine redis-cli -h cache ping
~~~~

### Sample 2 (sh)

Source: [Chapter 07. Container networking](./07-container-networking.md)

~~~~sh
docker network ls
docker network inspect app-net
~~~~

### Sample 3 (sh)

Source: [Chapter 07. Container networking](./07-container-networking.md)

~~~~sh
docker container rm --force cache
docker network rm app-net
~~~~

### Sample 4 (sh)

Source: [Chapter 07. Container networking](./07-container-networking.md)

~~~~sh
docker network connect app-net cache
docker network disconnect app-net cache
~~~~

### Sample 5 (sh)

Source: [Chapter 07. Container networking](./07-container-networking.md)

~~~~sh
docker run --detach --name web --publish 127.0.0.1:8080:80 nginx:alpine
~~~~

### Sample 6 (text)

Source: [Chapter 07. Container networking](./07-container-networking.md)

~~~~text
HOST_IP:HOST_PORT:CONTAINER_PORT
127.0.0.1:8080:80
~~~~

### Sample 7 (sh)

Source: [Chapter 07. Container networking](./07-container-networking.md)

~~~~sh
docker network create app-net
docker run --detach --name cache --network app-net redis:7-alpine
docker run --detach --name web --network app-net --publish 127.0.0.1:8080:80 nginx:alpine
~~~~

### Sample 8 (sh)

Source: [Chapter 07. Container networking](./07-container-networking.md)

~~~~sh
docker container ls
docker network inspect app-net
docker container logs cache
docker container exec cache redis-cli ping
~~~~

## [08. Docker Compose fundamentals](./08-compose-fundamentals.md)

[Read the chapter](./08-compose-fundamentals.md)

### Sample 1 (yaml)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "127.0.0.1:8080:80"

  cache:
    image: redis:7-alpine
~~~~

### Sample 2 (sh)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~sh
docker compose config --quiet
docker compose up --detach
docker compose ps
~~~~

### Sample 3 (sh)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~sh
docker compose logs
docker compose logs --follow cache
~~~~

### Sample 4 (sh)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~sh
docker compose exec web sh
~~~~

### Sample 5 (sh)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~sh
docker compose run --rm cache redis-cli -h cache ping
~~~~

### Sample 6 (sh)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~sh
docker compose down
~~~~

### Sample 7 (sh)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~sh
docker compose down --volumes
~~~~

### Sample 8 (sh)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~sh
docker compose up --detach web
~~~~

### Sample 9 (sh)

Source: [Chapter 08. Docker Compose fundamentals](./08-compose-fundamentals.md)

~~~~sh
docker compose --file compose.yaml up --detach
~~~~

## [09. Compose configuration and services](./09-compose-configuration-and-services.md)

[Read the chapter](./09-compose-configuration-and-services.md)

### Sample 1 (text)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~text
WEB_PORT=8080
NGINX_TAG=alpine
APP_ENV=development
~~~~

### Sample 2 (yaml)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~yaml
services:
  web:
    image: nginx:${NGINX_TAG:-alpine}
    ports:
      - "127.0.0.1:${WEB_PORT:-8080}:80"
    environment:
      APP_ENV: ${APP_ENV:-development}
~~~~

### Sample 3 (yaml)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~yaml
services:
  api:
    image: example-api:1.0
    env_file:
      - ./api.env
~~~~

### Sample 4 (yaml)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~yaml
services:
  api:
    image: example-api:1.0
    environment:
      LOG_LEVEL: info
      APP_ENV: development
~~~~

### Sample 5 (yaml)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~yaml
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
~~~~

### Sample 6 (yaml)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~yaml
services:
  db:
    image: postgres:17-alpine
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
~~~~

### Sample 7 (yaml)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~yaml
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
~~~~

### Sample 8 (yaml)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~yaml
services:
  db-viewer:
    image: adminer
    profiles:
      - debug
    ports:
      - "127.0.0.1:8081:8080"
~~~~

### Sample 9 (sh)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~sh
docker compose up --detach
~~~~

### Sample 10 (sh)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~sh
docker compose --profile debug up --detach
~~~~

### Sample 11 (yaml)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~yaml
services:
  api:
    image: example-api:1.0
    secrets:
      - db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
~~~~

### Sample 12 (sh)

Source: [Chapter 09. Compose configuration and services](./09-compose-configuration-and-services.md)

~~~~sh
docker compose config
~~~~

## [10. Registries and image publishing](./10-registries-and-image-publishing.md)

[Read the chapter](./10-registries-and-image-publishing.md)

### Sample 1 (text)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~text
registry.example.com/team/service:1.4.2
~~~~

### Sample 2 (sh)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~sh
docker build --tag my-namespace/notes-api:1.0.0 .
~~~~

### Sample 3 (sh)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~sh
docker tag notes-api:dev my-namespace/notes-api:1.0.0
~~~~

### Sample 4 (sh)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~sh
docker image ls my-namespace/notes-api
docker image inspect my-namespace/notes-api:1.0.0
~~~~

### Sample 5 (sh)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~sh
docker login
~~~~

### Sample 6 (powershell)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~powershell
$env:DOCKER_TOKEN | docker login --username $env:DOCKER_USER --password-stdin
~~~~

### Sample 7 (sh)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~sh
docker push my-namespace/notes-api:1.0.0
~~~~

### Sample 8 (sh)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~sh
docker pull my-namespace/notes-api:1.0.0
~~~~

### Sample 9 (sh)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~sh
docker pull my-namespace/notes-api@sha256:replace-with-the-actual-digest
~~~~

### Sample 10 (sh)

Source: [Chapter 10. Registries and image publishing](./10-registries-and-image-publishing.md)

~~~~sh
docker login registry.example.com
docker tag notes-api:dev registry.example.com/team/notes-api:1.0.0
docker push registry.example.com/team/notes-api:1.0.0
docker pull registry.example.com/team/notes-api:1.0.0
~~~~

## [11. Multi-stage builds and image optimization](./11-multi-stage-builds-and-image-optimization.md)

[Read the chapter](./11-multi-stage-builds-and-image-optimization.md)

### Sample 1 (dockerfile)

Source: [Chapter 11. Multi-stage builds and image optimization](./11-multi-stage-builds-and-image-optimization.md)

~~~~dockerfile
FROM node:22-alpine AS build
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM node:22-alpine AS runtime
WORKDIR /app
ENV NODE_ENV=production

COPY package.json package-lock.json ./
RUN npm ci --omit=dev && npm cache clean --force
COPY --from=build /app/dist ./dist

USER node
EXPOSE 3000
CMD ["node", "dist/server.js"]
~~~~

### Sample 2 (dockerfile)

Source: [Chapter 11. Multi-stage builds and image optimization](./11-multi-stage-builds-and-image-optimization.md)

~~~~dockerfile
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
~~~~

### Sample 3 (text)

Source: [Chapter 11. Multi-stage builds and image optimization](./11-multi-stage-builds-and-image-optimization.md)

~~~~text
.git
node_modules
coverage
.env
npm-debug.log
~~~~

### Sample 4 (sh)

Source: [Chapter 11. Multi-stage builds and image optimization](./11-multi-stage-builds-and-image-optimization.md)

~~~~sh
docker build --target build --tag notes-api:build .
~~~~

### Sample 5 (sh)

Source: [Chapter 11. Multi-stage builds and image optimization](./11-multi-stage-builds-and-image-optimization.md)

~~~~sh
docker build --tag notes-api:1.0.0 .
docker image ls notes-api
docker image inspect notes-api:1.0.0
docker image history notes-api:1.0.0
~~~~

## [12. Container security](./12-container-security.md)

[Read the chapter](./12-container-security.md)

### Sample 1 (dockerfile)

Source: [Chapter 12. Container security](./12-container-security.md)

~~~~dockerfile
FROM node:22-alpine
WORKDIR /app
COPY --chown=node:node package.json package-lock.json ./
RUN npm ci --omit=dev
COPY --chown=node:node dist ./dist

ENV NODE_ENV=production
USER node
CMD ["node", "dist/server.js"]
~~~~

### Sample 2 (sh)

Source: [Chapter 12. Container security](./12-container-security.md)

~~~~sh
docker run --rm --user 10001:10001 notes-api:1.0.0
~~~~

### Sample 3 (sh)

Source: [Chapter 12. Container security](./12-container-security.md)

~~~~sh
docker run --rm \
  --read-only \
  --tmpfs /tmp:rw,noexec,nosuid,size=64m \
  --cap-drop=ALL \
  --security-opt=no-new-privileges \
  --publish 127.0.0.1:8080:3000 \
  notes-api:1.0.0
~~~~

### Sample 4 (dockerfile)

Source: [Chapter 12. Container security](./12-container-security.md)

~~~~dockerfile
# syntax=docker/dockerfile:1
FROM node:22-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN --mount=type=secret,id=npmrc,target=/root/.npmrc npm ci
~~~~

### Sample 5 (sh)

Source: [Chapter 12. Container security](./12-container-security.md)

~~~~sh
docker build --secret id=npmrc,src=.npmrc .
~~~~

### Sample 6 (sh)

Source: [Chapter 12. Container security](./12-container-security.md)

~~~~sh
docker image inspect notes-api:1.0.0
docker container inspect notes-api
docker port notes-api
~~~~

## [13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

[Read the chapter](./13-logs-debugging-and-troubleshooting.md)

### Sample 1 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker logs --tail 100 api
docker logs --since 10m --follow api
docker logs --timestamps api
~~~~

### Sample 2 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker compose logs --tail 100 api
docker compose logs --follow
~~~~

### Sample 3 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker ps --all
docker compose ps --all
~~~~

### Sample 4 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker inspect --format '{{.State.Status}} exit={{.State.ExitCode}} oom={{.State.OOMKilled}} error={{.State.Error}}' api
~~~~

### Sample 5 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker inspect --format '{{.State.Health.Status}}' api
~~~~

### Sample 6 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker events --since 10m
~~~~

### Sample 7 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker stats api
~~~~

### Sample 8 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker port api
~~~~

### Sample 9 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker inspect --format '{{json .Mounts}}' api
~~~~

### Sample 10 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker context show
docker version
docker info
~~~~

### Sample 11 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
sudo systemctl status docker
sudo journalctl -u docker.service --since "10 minutes ago"
~~~~

### Sample 12 (sh)

Source: [Chapter 13. Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md)

~~~~sh
docker info --format '{{.LoggingDriver}}'
~~~~

## [14. Containerizing an application](./14-containerizing-an-application.md)

[Read the chapter](./14-containerizing-an-application.md)

### Sample 1 (text)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~text
notes-api/
|-- .dockerignore
|-- Dockerfile
`-- server.mjs
~~~~

### Sample 2 (js)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~js
import { createServer } from "node:http";

const port = Number(process.env.PORT ?? 3000);

const server = createServer((request, response) => {
  if (request.url === "/health") {
    response.writeHead(200, { "content-type": "application/json" });
    response.end(JSON.stringify({ status: "ok" }));
    return;
  }

  if (request.url === "/") {
    response.writeHead(200, { "content-type": "application/json" });
    response.end(JSON.stringify({ message: "Notes API is running" }));
    return;
  }

  response.writeHead(404, { "content-type": "application/json" });
  response.end(JSON.stringify({ error: "Not found" }));
});

server.listen(port, "0.0.0.0", () => {
  console.log(`Notes API listening on port ${port}`);
});
~~~~

### Sample 3 (dockerfile)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~dockerfile
FROM node:22-alpine

ENV NODE_ENV=production
WORKDIR /app

COPY --chown=node:node server.mjs ./server.mjs

USER node
EXPOSE 3000
CMD ["node", "server.mjs"]
~~~~

### Sample 4 (text)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~text
.git
.env
node_modules
coverage
~~~~

### Sample 5 (sh)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~sh
docker build --tag notes-api:1.0.0 .
docker image ls notes-api
~~~~

### Sample 6 (sh)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~sh
docker run --detach \
  --name notes-api \
  --publish 127.0.0.1:8080:3000 \
  notes-api:1.0.0
~~~~

### Sample 7 (text)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~text
host-address:host-port:container-port
~~~~

### Sample 8 (sh)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~sh
docker ps
docker port notes-api
~~~~

### Sample 9 (powershell)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~powershell
Invoke-RestMethod -Uri http://localhost:8080
Invoke-RestMethod -Uri http://localhost:8080/health
~~~~

### Sample 10 (sh)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~sh
curl http://localhost:8080/
curl http://localhost:8080/health
~~~~

### Sample 11 (sh)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~sh
docker logs notes-api
~~~~

### Sample 12 (sh)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~sh
docker stop notes-api
docker rm notes-api
~~~~

### Sample 13 (sh)

Source: [Chapter 14. Containerizing an application](./14-containerizing-an-application.md)

~~~~sh
docker image rm notes-api:1.0.0
~~~~

## [15. Local development and test workflows](./15-local-development-and-test-workflows.md)

[Read the chapter](./15-local-development-and-test-workflows.md)

### Sample 1 (yaml)

Source: [Chapter 15. Local development and test workflows](./15-local-development-and-test-workflows.md)

~~~~yaml
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
~~~~

### Sample 2 (sh)

Source: [Chapter 15. Local development and test workflows](./15-local-development-and-test-workflows.md)

~~~~sh
docker compose up --watch app
~~~~

### Sample 3 (sh)

Source: [Chapter 15. Local development and test workflows](./15-local-development-and-test-workflows.md)

~~~~sh
docker compose up --detach --watch app
~~~~

### Sample 4 (sh)

Source: [Chapter 15. Local development and test workflows](./15-local-development-and-test-workflows.md)

~~~~sh
docker compose logs --follow app
~~~~

### Sample 5 (sh)

Source: [Chapter 15. Local development and test workflows](./15-local-development-and-test-workflows.md)

~~~~sh
docker compose up --detach app
docker compose run --rm check
~~~~

### Sample 6 (sh)

Source: [Chapter 15. Local development and test workflows](./15-local-development-and-test-workflows.md)

~~~~sh
docker compose run --rm check
~~~~

### Sample 7 (sh)

Source: [Chapter 15. Local development and test workflows](./15-local-development-and-test-workflows.md)

~~~~sh
docker compose --profile test up
~~~~

### Sample 8 (sh)

Source: [Chapter 15. Local development and test workflows](./15-local-development-and-test-workflows.md)

~~~~sh
docker compose down
~~~~

## [16. Production deployment and operations](./16-production-deployment-and-operations.md)

[Read the chapter](./16-production-deployment-and-operations.md)

### Sample 1 (yaml)

Source: [Chapter 16. Production deployment and operations](./16-production-deployment-and-operations.md)

~~~~yaml
services:
  app:
    image: registry.example.com/team/notes-api:1.0.0
    ports:
      - "127.0.0.1:8080:3000"
    restart: unless-stopped
    read_only: true
    tmpfs:
      - /tmp
    cap_drop:
      - ALL
    security_opt:
      - no-new-privileges:true
    healthcheck:
      test:
        - CMD
        - node
        - -e
        - "fetch('http://127.0.0.1:3000/health').then(r => process.exit(r.ok ? 0 : 1)).catch(() => process.exit(1))"
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 10s
    logging:
      driver: local
~~~~

### Sample 2 (sh)

Source: [Chapter 16. Production deployment and operations](./16-production-deployment-and-operations.md)

~~~~sh
docker run --detach \
  --name notes-api \
  --memory 512m \
  --cpus 1.5 \
  registry.example.com/team/notes-api:1.0.0
~~~~

### Sample 3 (sh)

Source: [Chapter 16. Production deployment and operations](./16-production-deployment-and-operations.md)

~~~~sh
docker build --tag registry.example.com/team/notes-api:1.0.1 .
docker push registry.example.com/team/notes-api:1.0.1
~~~~

### Sample 4 (sh)

Source: [Chapter 16. Production deployment and operations](./16-production-deployment-and-operations.md)

~~~~sh
docker compose -f compose.production.yaml pull
docker compose -f compose.production.yaml up --detach
docker compose -f compose.production.yaml ps
docker compose -f compose.production.yaml logs --tail 100 app
~~~~

### Sample 5 (sh)

Source: [Chapter 16. Production deployment and operations](./16-production-deployment-and-operations.md)

~~~~sh
docker compose -f compose.production.yaml pull
docker compose -f compose.production.yaml up --detach
~~~~

### Sample 6 (sh)

Source: [Chapter 16. Production deployment and operations](./16-production-deployment-and-operations.md)

~~~~sh
docker volume ls
docker volume inspect app-data
~~~~

### Sample 7 (sh)

Source: [Chapter 16. Production deployment and operations](./16-production-deployment-and-operations.md)

~~~~sh
docker compose -f compose.production.yaml ps
docker stats
docker image inspect registry.example.com/team/notes-api:1.0.1
~~~~

---

[Back to notes index](../README.md)
