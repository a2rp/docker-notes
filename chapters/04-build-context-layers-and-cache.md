# 04. Build context, layers, and cache

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Dockerfile fundamentals](./03-dockerfile-fundamentals.md) | [Notes index](../README.md) | [Next: Container commands and health](./05-container-commands-and-health.md) |

## Choose the build context

The build context is the set of files the builder can use during an image build. The final argument to `docker build` selects that context.

~~~sh
docker build --tag node-notes-app:dev .
~~~

Here, `.` means the current directory. A Dockerfile instruction such as `COPY server.js ./` reads from that context. A file outside the context cannot be copied by using a relative path that climbs to a parent directory.

Keep the context limited to the application files that the build needs. A smaller context transfers faster, especially when using a remote builder, and reduces the chance of sending private or irrelevant files.

## Exclude unnecessary files with `.dockerignore`

Create a `.dockerignore` file in the root of the build context:

~~~text
node_modules
.git
.env
.env.*
!.env.example
coverage
dist
~~~

Docker removes matching files from the build context before sending it to the builder. The file does not delete anything from the host. The negation rule keeps a safe example environment file while excluding local environment files.

Review ignore rules when the build cannot find a file. If `package-lock.json` is excluded, for example, a later `COPY package-lock.json` instruction will fail.

Never include private keys, production data, or personal credentials in the build context. A `.dockerignore` rule helps, but reviewing what is sent to the build is still part of the build process.

## Understand image layers and cache

Docker processes Dockerfile instructions in order. The builder can reuse a previous result when an instruction and its inputs match a cached result. When a layer changes, later steps may need to run again.

Consider this order for a Node.js application:

~~~dockerfile
FROM node:22-alpine
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .
CMD ["node", "server.js"]
~~~

When only `server.js` changes, the package files remain the same, so Docker can reuse the dependency installation layer. If all application files are copied before installing dependencies, changing one source file can invalidate the install step.

Cache behavior depends on the instruction and its inputs. A cached package installation is not a check for newer packages in a remote registry. Use a lockfile for repeatable dependency versions and update it deliberately.

## See which steps use the cache

Build with plain progress output:

~~~sh
docker buildx build --progress=plain --tag node-notes-app:dev .
~~~

Build again without changing any inputs. The output shows which build steps were reused. Then change `server.js` and build again. The source copy and later steps may run again, while the dependency installation can remain cached.

Use `--no-cache` to rebuild instructions without using the build cache. Use `--pull` when you also want Docker to check for a newer version of the base image.

~~~sh
docker buildx build --pull --no-cache --tag node-notes-app:test .
~~~

This is useful for diagnosis or a deliberate clean build. It is usually slower than a normal cached build.

## Reuse package downloads with a cache mount

BuildKit supports cache mounts for package-manager data. The cache can speed up a later build without adding the package download cache to the final image layer.

~~~dockerfile
# syntax=docker/dockerfile:1
FROM node:22-alpine
WORKDIR /app

COPY package.json package-lock.json ./
RUN --mount=type=cache,target=/root/.npm npm ci

COPY . .
CMD ["node", "server.js"]
~~~

The cache mount is available during the `RUN` instruction. The application still needs the installed dependencies in the build stage. Cache contents are an optimization, not a required part of the image, so the build must work when the cache starts empty.

## Use the smallest useful context

A project can contain source files, tests, generated output, local dependencies, editor settings, and Git history. The build context should include only what the Dockerfile needs.

Before adding an ignore pattern, check whether an instruction must copy that file. For example, an application build might need its source and lockfile but not local `node_modules`, coverage output, or `.git` data.

When one project contains several images, consider a Dockerfile-specific ignore file beside each Dockerfile. The file name uses the Dockerfile name followed by `.dockerignore`.

## Keep secrets out of build arguments

Do not copy a secret file into an image and remove it in a later instruction. It can remain in an earlier image layer. Do not pass credentials through ordinary build arguments or `ENV` values.

When a build step needs a private package credential, use BuildKit secret mounts so the value is available only during that step. Secret handling is covered further in chapter 12.

## Build context and cache checklist

- Pass the intended directory as the build context.
- Keep the context small and review `.dockerignore`.
- Copy stable dependency manifests before frequently changing source files.
- Use a lockfile for application dependencies.
- Inspect build output before changing cache settings.
- Use `--no-cache` and `--pull` for different purposes.
- Use a cache mount only as an optimization, not as required application state.
- Keep secrets out of the context, image layers, and ordinary build arguments.

## Further reading

- [Build context](https://docs.docker.com/build/concepts/context/)
- [Docker build cache](https://docs.docker.com/build/cache/)
- [Cache invalidation](https://docs.docker.com/build/cache/invalidation/)
- [Cache optimization](https://docs.docker.com/build/cache/optimize/)
- [BuildKit](https://docs.docker.com/build/buildkit/)
- [Build secrets](https://docs.docker.com/build/building/secrets/)