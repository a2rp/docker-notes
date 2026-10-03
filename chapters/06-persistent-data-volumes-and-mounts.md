# 06. Persistent data with volumes and mounts

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Container commands and health](./05-container-commands-and-health.md) | [Notes index](../README.md) | [Next: Container networking](./07-container-networking.md) |

## Where container files live

A container has a writable layer above its image. It is useful for temporary runtime changes, but it belongs to that container. Removing the container removes files stored only in that layer.

Use a mount when data needs a different lifetime or location:

| Storage type | Location | Common use |
| --- | --- | --- |
| Container writable layer | Managed with the container | Temporary files that can disappear with the container. |
| Named volume | Managed by Docker on the Engine host | Database files and data that should outlive a container. |
| Bind mount | A specific host file or directory | Source code, configuration, or files shared with the host. |
| `tmpfs` mount | Host memory | Temporary Linux container data that should not be written to disk. |

Volumes and bind mounts are separate from the container's writable layer. Removing a container does not normally remove a named volume.

## Create a named volume

Create a volume and write a file into it from a temporary container:

~~~sh
docker volume create notes-data
docker run --rm --mount type=volume,source=notes-data,target=/data alpine:3 sh -c 'echo "saved value" > /data/message.txt'
~~~

The first container exits and is removed. Start a new container with the same volume and read the file:

~~~sh
docker run --rm --mount type=volume,source=notes-data,target=/data,readonly alpine:3 cat /data/message.txt
~~~

The value remains because it is in the volume, not in the removed container. The `readonly` option prevents this second container from modifying the mounted data.

Inspect the volume and its users:

~~~sh
docker volume inspect notes-data
docker container ls --all
~~~

The volume is managed by the Engine host. Its data does not automatically move to another machine or create a backup.

## Use a bind mount for host files

A bind mount connects an existing host path to a path inside the container. It is useful during local development when edits on the host should be visible to the container.

First create a small site directory and a file. In PowerShell:

~~~powershell
New-Item -ItemType Directory -Force .\site | Out-Null
Set-Content -Path .\site\index.html -Value '<h1>Mounted from the host</h1>'
$sitePath = (Resolve-Path .\site).Path
~~~

Run Nginx with that directory mounted read-only:

~~~powershell
docker run --rm --publish 127.0.0.1:8081:80 --mount "type=bind,source=$sitePath,target=/usr/share/nginx/html,readonly" nginx:alpine
~~~

Open `http://127.0.0.1:8081`. The page comes from the host directory. Change `site/index.html` on the host and refresh the page.

The source path must exist on the Docker daemon host. Docker Desktop handles native Windows paths for its Linux containers. With a remote Engine, the path must exist on the remote host instead of only on the client machine.

Bind mounts are read-write by default. Use `readonly` when a container only needs to read host files. Avoid mounting broad or sensitive host directories into a container.

## Mounting over a directory hides its image files

When a mount targets a directory that already has files in the image, the mounted content hides those image files while the container runs. It does not delete the files from the image.

This is a common reason an application seems to lose its installed dependencies after a development bind mount. Compare the container path and host source, then remove or narrow the mount if it hides required image files.

## Use temporary memory-backed storage

A `tmpfs` mount is temporary storage for Linux containers. The data does not persist after the container stops and is not stored in a Docker volume.

~~~sh
docker run --rm --mount type=tmpfs,target=/run/cache alpine:3 sh -c 'echo temporary > /run/cache/value.txt && cat /run/cache/value.txt'
~~~

Use it only for data that may disappear when the container stops. Platform support and behavior depend on the container and host configuration.

## Choose between `--mount` and `--volume`

Both options can create common mount types. The `--mount` form uses named fields, so it is easier to read and extend:

~~~sh
docker run --rm --mount type=volume,source=notes-data,target=/data alpine:3
~~~

The shorter `--volume` form uses colon-separated fields:

~~~sh
docker run --rm --volume notes-data:/data alpine:3
~~~

For a bind mount, the shorter form is:

~~~sh
docker run --rm --volume /host/path:/container/path:ro nginx:alpine
~~~

Use `--mount` in new examples when clarity matters. It fails if a bind source path is missing, which helps catch a misspelled host path early.

## Remove the exercise data deliberately

Stop the Nginx process with Ctrl+C if it is still running. Remove the volume only after you are sure the contents are no longer needed:

~~~sh
docker volume rm notes-data
docker volume ls
~~~

Removing a named volume deletes its data. Review the volume name before running the command. Prune commands can remove multiple unused volumes, so avoid them when a single volume removal is intended.

## Storage checklist

- Use a named volume for application data that must outlive a container.
- Use a bind mount for a specific host path that a container needs to read or change.
- Mark bind mounts read-only when writes are not required.
- Use `tmpfs` only for data that can disappear when the container stops.
- Confirm a mount does not hide files the application expects from the image.
- Keep a backup and restore plan for important volume data.
- Check the Docker context when a mounted source path is not found.

## Further reading

- [Docker storage](https://docs.docker.com/engine/storage/)
- [Volumes](https://docs.docker.com/engine/storage/volumes/)
- [Bind mounts](https://docs.docker.com/engine/storage/bind-mounts/)
- [tmpfs mounts](https://docs.docker.com/engine/storage/tmpfs/)
- [Run a container with mounts](https://docs.docker.com/reference/cli/docker/container/run/)