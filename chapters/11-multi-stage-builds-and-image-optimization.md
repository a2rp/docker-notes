# 11. Multi-stage builds and image optimization

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Registries and image publishing](./10-registries-and-image-publishing.md) | [Notes index](../README.md) | [Next: Container security](./12-container-security.md) |

## Why use more than one build stage?

A Dockerfile can contain multiple `FROM` instructions. Each one starts a new stage. A build stage can install compilers, download development packages, run tests, and produce application files. A later runtime stage can copy only the files needed to run the application.

The final stage is the default image output. Build tools and files from earlier stages do not appear in it unless a later stage copies them.

This reduces the amount of software shipped with the application. It can also reduce image size and make it easier to inspect what the runtime actually needs.

## Name stages and copy only required files

Give stages names so `COPY --from` remains clear if the Dockerfile changes:

~~~dockerfile
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
~~~

This example assumes the project has a lock file, a build script that creates `dist`, and a server entry point at `dist/server.js`. Change those paths to match the application.

The first stage installs all dependencies and creates the production files. The final stage installs only production dependencies, then copies the build output from the named stage. It does not copy the source tree, compiler, or development dependencies.

Both stages use the same Node base variant. This matters when dependencies include native binaries, because those binaries must match the operating system libraries and CPU architecture in the runtime image.

## Keep dependency installation in a reusable layer

Copy dependency manifests before frequently changing source files:

~~~dockerfile
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
~~~

Docker can reuse the dependency installation layer when only application source changes. If the manifest or lock file changes, dependency installation must run again.

A `.dockerignore` file keeps local files out of the build context:

~~~text
.git
node_modules
coverage
.env
npm-debug.log
~~~

Do not ignore a file that a `COPY` instruction needs. Excluding `.env` also helps prevent local environment values from being sent with the build context. Pass runtime configuration through the deployment environment instead.

## Build a particular stage

Build only through the `build` stage when you want to inspect or test its output:

~~~sh
docker build --target build --tag notes-api:build .
~~~

Build the default final stage for the runtime image:

~~~sh
docker build --tag notes-api:1.0.0 .
docker image ls notes-api
docker image inspect notes-api:1.0.0
docker image history notes-api:1.0.0
~~~

`--target` selects a named stage. It is useful for checking an intermediate result or creating a separate test stage. A target does not automatically mean that the stage is a safe production image.

## Optimize with a measured goal

Start by removing files and packages that the runtime does not need. Then compare the resulting images and build times. A smaller image can download faster, but the smallest possible image is not always the best choice if it becomes difficult to debug or maintain.

Use a base image that matches the application's runtime requirements. Alpine uses musl rather than glibc, so some native packages built for a Debian-based image may not work on Alpine without changes. Test the actual production build on the intended base.

Base tags can move over time. Choose a maintained version and update it deliberately. A digest can identify exact image content, while a version tag is easier to read and update. Follow the project's release and security update process either way.

Docker's BuildKit can reuse build cache and supports cache mounts for package managers. Cache mounts speed up builds, but their contents are not part of the final image. Keep the ordinary Dockerfile understandable first, then add cache mounts when build time is a measured problem.

## Common mistakes

- Copying the entire source tree into the final stage when only compiled output is needed.
- Installing development dependencies in the runtime stage.
- Copying a `node_modules` directory built for a different operating system or architecture.
- Forgetting a file required by the runtime, such as a migration, template, or static asset.
- Choosing a base image only because its download is small, then discovering that a required native package cannot run on it.
- Assuming a multi-stage build automatically removes secrets. Do not pass credentials as ordinary build arguments or copy them into any stage.

## Sources

- [Multi-stage builds](https://docs.docker.com/build/building/multi-stage/)
- [Build best practices](https://docs.docker.com/build/building/best-practices/)
- [Optimize cache usage in builds](https://docs.docker.com/build/cache/optimize/)