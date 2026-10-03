# 12. Container security

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Multi-stage builds and image optimization](./11-multi-stage-builds-and-image-optimization.md) | [Notes index](../README.md) | [Next: Logs, debugging, and troubleshooting](./13-logs-debugging-and-troubleshooting.md) |

## Understand the security boundary

A container gives a process isolation through Linux features such as namespaces and control groups. Containers still share the host kernel, so treat the host, Docker daemon, images, mounted files, and container configuration as parts of one security boundary.

A container is not a replacement for a virtual machine when a workload needs a separate kernel boundary. Choose isolation to match the sensitivity of the workload and the environment where it runs.

## Run the application as an unprivileged user

Processes inside an image often start as root unless the image or Dockerfile selects another user. Create or use an application user and switch to it before the startup command:

~~~dockerfile
FROM node:22-alpine
WORKDIR /app
COPY --chown=node:node package.json package-lock.json ./
RUN npm ci --omit=dev
COPY --chown=node:node dist ./dist

ENV NODE_ENV=production
USER node
CMD ["node", "dist/server.js"]
~~~

The official Node image includes a `node` user. For other base images, check which users exist or create one. Ensure that any directory the application must write to is owned by or writable by that user.

You can also select a numeric user when starting a container:

~~~sh
docker run --rm --user 10001:10001 notes-api:1.0.0
~~~

A numeric user is useful when an image has no matching account entry, but file permissions still need to allow that UID to read the application and write only to intended locations.

## Reduce privileges and writable locations

Start with the default container restrictions. Avoid `--privileged`, host namespaces, and extra capabilities unless a specific requirement calls for them.

For an application that can run with a read-only root filesystem, combine a read-only filesystem with a temporary writable directory:

~~~sh
docker run --rm \
  --read-only \
  --tmpfs /tmp:rw,noexec,nosuid,size=64m \
  --cap-drop=ALL \
  --security-opt=no-new-privileges \
  --publish 127.0.0.1:8080:3000 \
  notes-api:1.0.0
~~~

This command assumes the application needs no other writable path or Linux capability. If it needs one, grant only that specific access and document why. A failed write can also reveal that the application depends on local state that should be stored in a named volume or external service.

Capabilities divide some traditional root privileges into smaller units. `--cap-drop=ALL` removes all capabilities from the container process. Add a capability back only when the application requires it. `no-new-privileges` prevents a process from gaining additional privileges through mechanisms such as set-user-ID executables.

Docker also applies security profiles such as seccomp on supported Linux systems. Do not disable profiles as a routine fix for a permission error. First identify the system call or operation the application needs.

## Keep credentials out of images

Do not put passwords, tokens, private keys, or cloud credentials in any of these places:

- A Dockerfile `ENV` instruction.
- A Dockerfile `ARG` instruction.
- A copied `.env` file.
- A command that writes a credential into an image layer.

Image history, metadata, build logs, or build attestations can reveal values even if a later layer deletes the file. Use runtime secrets or a secret manager for application credentials. For a build that needs a private package registry credential, use a BuildKit secret mount:

~~~dockerfile
# syntax=docker/dockerfile:1
FROM node:22-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN --mount=type=secret,id=npmrc,target=/root/.npmrc npm ci
~~~

Pass the secret file at build time:

~~~sh
docker build --secret id=npmrc,src=.npmrc .
~~~

The secret is mounted only for that build instruction. Do not commit the credential file. A secret mount prevents the value from being automatically copied into the image, but the build command could still leak it if a script prints or copies the secret.

## Protect access to the Docker daemon

Only trusted users and services should control a rootful Docker daemon. A user who can control it may be able to create containers with broad access to host files. Treat access to the Docker socket like elevated host access.

Do not expose the Docker API on an unauthenticated network port. For remote administration, use a protected connection such as SSH or correctly configured TLS, and restrict access to trusted users and networks.

Rootless mode runs both the daemon and containers without root privileges, when the host meets its requirements. It reduces some risks from daemon or runtime vulnerabilities, but it does not replace application permissions, image review, or host security updates.

## Review images and configuration

Before running an image, check where it came from, which version or digest it uses, and whether it matches the target architecture. Prefer maintained images from sources you trust. Rebuild and update images when the base image or application dependencies receive security fixes.

Review the effective container settings, mounts, and published ports:

~~~sh
docker image inspect notes-api:1.0.0
docker container inspect notes-api
docker port notes-api
~~~

Check that production containers do not mount sensitive host paths, expose debug ports, or publish services publicly without a reason. Limit each service to the host paths, network access, and credentials it needs.

## Sources

- [Docker Engine security](https://docs.docker.com/engine/security/)
- [Rootless mode](https://docs.docker.com/engine/security/rootless/)
- [Protect the Docker daemon socket](https://docs.docker.com/engine/security/protect-access/)
- [Build secrets](https://docs.docker.com/build/building/secrets/)
- [Seccomp security profiles](https://docs.docker.com/engine/security/seccomp/)