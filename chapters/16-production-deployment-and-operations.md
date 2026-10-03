# 16. Production deployment and operations

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Local development and test workflows](./15-local-development-and-test-workflows.md) | [Notes index](../README.md) | [Next: All code samples](./98-all-code-samples.md) |

## Know what Compose can manage

Docker Compose can run an application on a single Docker host. It can also be used in build and deployment workflows. A single host Compose setup does not provide the scheduling and automatic failover of a cluster orchestrator, so plan maintenance and recovery around that host.

Keep the production image self-contained. Production containers should run code from the image, not from a bind mount of a developer's source directory.

## Define production settings

Use a production Compose file that selects a published image and production runtime settings:

~~~yaml
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
~~~

This example assumes the image provides Node and the `/health` route from chapter 14. The read-only root filesystem is suitable only if the app writes no other files. Add a specific writable volume or temporary directory only when the application needs one.

The host port is bound to `127.0.0.1`, so it is reachable from the Docker host. A reverse proxy on that host can forward public HTTPS traffic to it. Do not expose an application port publicly without deciding how TLS, access rules, and request limits will be handled.

The restart policy asks Docker to restart the container after it exits, subject to the policy's rules. It does not repair a broken release or provide failover to another server. A health check records whether the probe succeeds; it does not automatically restart an unhealthy container by itself.

## Set resource limits from measurements

Containers have no CPU or memory limit by default. Measure the application under representative load, then choose limits with enough headroom for normal traffic and startup.

For a container started directly with Docker:

~~~sh
docker run --detach \
  --name notes-api \
  --memory 512m \
  --cpus 1.5 \
  registry.example.com/team/notes-api:1.0.0
~~~

These sample values are not universal recommendations. An undersized memory limit can cause the process to be killed. Check usage with `docker stats` and check `OOMKilled` in the container state after a failure. Resource control support depends on the host kernel and Docker environment.

## Publish and deploy a release

Build and push an explicit release tag from a trusted build environment:

~~~sh
docker build --tag registry.example.com/team/notes-api:1.0.1 .
docker push registry.example.com/team/notes-api:1.0.1
~~~

On the deployment host, pull the new image and recreate the service:

~~~sh
docker compose -f compose.production.yaml pull
docker compose -f compose.production.yaml up --detach
docker compose -f compose.production.yaml ps
docker compose -f compose.production.yaml logs --tail 100 app
~~~

Use an intentional version tag for each release. Tags are convenient names but can be moved to different image content. For deployments that require an exact image identity, record and deploy the image digest, then update it through the release process.

Before changing the database schema or removing old files, confirm that the new application can work with the current data. Take a backup before a migration that could alter or discard data.

## Roll back deliberately

Keep the previous known-good image available. To roll back, change the production image reference to its previous version, then recreate the service:

~~~sh
docker compose -f compose.production.yaml pull
docker compose -f compose.production.yaml up --detach
~~~

A rollback is only safe if database changes remain compatible with the old application. Plan schema changes so the old and new versions can coexist during a release, or prepare a tested data recovery procedure.

## Preserve and test application data

Containers are replaceable. Data written only into a container's writable layer is lost when that container is removed. Store persistent data in a named volume or an external data service, and back it up using a method appropriate for that application.

List and inspect Docker-managed volumes:

~~~sh
docker volume ls
docker volume inspect app-data
~~~

A volume remains after a normal `docker compose down`. Passing `--volumes` removes Compose-managed volumes and can delete application data. Verify backups and practice restoring them before relying on them.

A backup is useful only if the application can restore it. Schedule backups, protect them from unauthorized access, and record how to test a restore.

## Track logs, image contents, and health

Use a logging destination with a retention plan. The `local` logging driver rotates container logs by default. For services that need centralized searching or longer retention, send application logs to a managed logging system.

Review what is running and how much it uses:

~~~sh
docker compose -f compose.production.yaml ps
docker stats
docker image inspect registry.example.com/team/notes-api:1.0.1
~~~

A software bill of materials (SBOM) lists software components in an image. Build provenance describes how the image was produced. These records help teams review image contents and build origin, but they do not by themselves prove that an image is safe. Generate and review them as part of a controlled release process.

## Production checklist

- Use a maintained base image and rebuild when security updates are available.
- Run application processes as an unprivileged user.
- Publish only the ports that need to be reachable.
- Keep credentials outside the image and source repository.
- Configure health checks, restart behavior, and log retention.
- Measure the service and choose suitable resource limits.
- Keep persistent data outside disposable containers.
- Test backups, release updates, and rollback steps.
- Keep a known-good image reference for recovery.

## Sources

- [Use Compose in production](https://docs.docker.com/compose/how-tos/production/)
- [Resource constraints](https://docs.docker.com/engine/containers/resource_constraints/)
- [Volumes](https://docs.docker.com/engine/storage/volumes/)
- [Build attestations](https://docs.docker.com/build/metadata/attestations/)