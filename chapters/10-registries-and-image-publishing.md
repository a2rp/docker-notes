# 10. Registries and image publishing

[Back to notes index](../README.md)

| Previous | Notes index | Next |
| --- | --- | --- |
| [Previous: Compose configuration and services](./09-compose-configuration-and-services.md) | [Notes index](../README.md) | [Next: Multi-stage builds and image optimization](./11-multi-stage-builds-and-image-optimization.md) |

## Understand an image reference

A registry stores images so they can be pulled by another machine or deployment system. An image reference can include a registry host, namespace, repository, and tag:

~~~text
registry.example.com/team/service:1.4.2
~~~

If the registry host is omitted, Docker uses Docker Hub. The namespace identifies an account or organization. The repository identifies an image, and the tag gives a human-readable reference to a version.

Tags are names, not immutable identifiers. A tag can be moved to different image content. If a tag is omitted, Docker uses `latest`. The word `latest` has no special guarantee that it is the newest stable or secure release.

## Build and tag an image for Docker Hub

Build a local image and tag it with the namespace and repository that you own:

~~~sh
docker build --tag my-namespace/notes-api:1.0.0 .
~~~

You can apply another tag to an existing local image:

~~~sh
docker tag notes-api:dev my-namespace/notes-api:1.0.0
~~~

Check which local image will be uploaded:

~~~sh
docker image ls my-namespace/notes-api
docker image inspect my-namespace/notes-api:1.0.0
~~~

Use an account or organization namespace where you have push permission. Private images require the appropriate access for every user or service that pulls them.

## Authenticate without exposing a token

For interactive use, Docker can prompt for credentials:

~~~sh
docker login
~~~

For a script or CI job, use a short-lived access token stored in the platform's secret manager and pass it through standard input. In PowerShell:

~~~powershell
$env:DOCKER_TOKEN | docker login --username $env:DOCKER_USER --password-stdin
~~~

Do not put a token directly in a command, Compose file, Dockerfile, or committed environment file. Docker may save authentication through the configured credential store. Use a narrowly scoped token and remove local credentials with `docker logout` when they are no longer needed.

## Push and pull a version

Push the tagged image:

~~~sh
docker push my-namespace/notes-api:1.0.0
~~~

The output includes a digest for the content that was uploaded. Another machine can pull the tag:

~~~sh
docker pull my-namespace/notes-api:1.0.0
~~~

For a repeatable deployment, record the digest printed by the registry and pull by digest when exact content must be fixed:

~~~sh
docker pull my-namespace/notes-api@sha256:replace-with-the-actual-digest
~~~

Replace the example text with the complete digest from the registry. A pinned digest continues to identify the same content, while a tag can be updated to point to another image.

## Use a private registry

For a registry other than Docker Hub, include its host and optional port in the image reference:

~~~sh
docker login registry.example.com
docker tag notes-api:dev registry.example.com/team/notes-api:1.0.0
docker push registry.example.com/team/notes-api:1.0.0
docker pull registry.example.com/team/notes-api:1.0.0
~~~

Registry access must be granted separately from access to the application that runs the image. Protect credentials and use a read-only pull credential in deployment systems when they do not need permission to publish.

## Choose a tagging policy

Use tags that help a team identify what was built, for example:

- A release version such as `1.4.2`.
- A commit identifier such as `a1b2c3d`.
- A testing label such as `staging`, if it is allowed to move.

Use a new version tag for each release when releases need to stay easy to identify. Treat tags such as `latest`, `staging`, or `production` as movable labels unless the registry enforces immutable tags.

For critical deployments, record both the readable release tag and the image digest. This keeps the release name useful while identifying the exact image content that was deployed.

## Publish through a repeatable build

A reliable release flow builds the image from a reviewed source revision, runs the relevant checks, tags the resulting image, and pushes that image to a registry with explicit permissions.

Do not rely on a developer's untracked local files to create a release image. Build from the expected commit and record the tag or digest used by each environment.

## Registry troubleshooting

- **Authentication is denied:** Confirm the registry host, account, token, and repository permission.
- **The repository path is wrong:** Check the namespace and repository name in the full image reference.
- **Pull access is denied:** Confirm whether the image is private and whether the deployment has pull access.
- **A tag points to unexpected content:** Inspect the registry tag or use a recorded digest.
- **A push is rejected:** Check the target architecture, repository policy, token scope, and registry storage limits.

## Chapter summary

- Image references can include a registry, namespace, repository, and tag.
- Tag an image with the destination namespace before pushing it.
- Use `docker login` and a protected token for private registries.
- Tags can move; `latest` is just a tag name.
- A digest identifies exact image content.
- Give deployment systems only the registry permissions they require.

## Further reading

- [Push an image to Docker Hub](https://docs.docker.com/docker-hub/repos/manage/hub-images/push/)
- [Docker Hub tags](https://docs.docker.com/docker-hub/repos/manage/hub-images/tags/)
- [docker login](https://docs.docker.com/reference/cli/docker/login/)
- [docker image push](https://docs.docker.com/reference/cli/docker/image/push/)
- [docker image pull](https://docs.docker.com/reference/cli/docker/image/pull/)
- [Image digests](https://docs.docker.com/dhi/core-concepts/digests/)