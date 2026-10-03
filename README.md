# Docker Study Notes

These are my personal study notes from learning and working with Docker. I am collecting the concepts, commands, and practical patterns that help me understand how containers are built, connected, secured, and run.

The notes move from the Docker Engine and container lifecycle through Dockerfiles, image builds, storage, networking, Compose, registries, security, and production operations. Examples use Docker commands and configuration with JavaScript application examples where useful.

## About this collection

This repository is a working record of what I study and practice with Docker. Each chapter explains the purpose of a feature, the decisions behind it, and the commands or configuration needed to try it in a small project.

The focus is on core Docker knowledge that applies to everyday development and deployment. Keep the Docker Engine, Compose plugin, and image versions used by a project in mind, since some features vary by platform and release.

## Chapters

01. [Docker and containers](./chapters/01-containers-and-docker.md)  
   Understand images, containers, the Docker Engine, and where Docker fits in an application workflow.

02. [Images, containers, and lifecycle](./chapters/02-images-containers-and-lifecycle.md)  
   Pull, inspect, run, stop, remove, and name containers while distinguishing an image from a running container.

03. [Dockerfile fundamentals](./chapters/03-dockerfile-fundamentals.md)  
   Write Dockerfiles with FROM, WORKDIR, COPY, RUN, USER, EXPOSE, and CMD, and understand each instruction.

04. [Build context, layers, and cache](./chapters/04-build-context-layers-and-cache.md)  
   Choose a small build context, order instructions for useful caching, and use BuildKit safely.

05. [Container commands and health](./chapters/05-container-commands-and-health.md)  
   Use run options, environment variables, logs, exit codes, restart behavior, and health checks.

06. [Persistent data with volumes and mounts](./chapters/06-persistent-data-volumes-and-mounts.md)  
   Choose named volumes, bind mounts, and temporary storage based on ownership and lifetime.

07. [Container networking](./chapters/07-container-networking.md)  
   Connect services using bridge networks, published ports, DNS names, and network inspection.

08. [Docker Compose fundamentals](./chapters/08-compose-fundamentals.md)  
   Define a small multi-container application and manage it with the Compose CLI.

09. [Compose configuration and services](./chapters/09-compose-configuration-and-services.md)  
   Configure dependencies, health checks, profiles, environment, secrets, networks, and volumes.

10. [Registries and image publishing](./chapters/10-registries-and-image-publishing.md)  
   Tag, authenticate, push, pull, and identify images with immutable digests.

11. [Multi-stage builds and image optimization](./chapters/11-multi-stage-builds-and-image-optimization.md)  
   Separate build tools from runtime files and reduce image size without sacrificing clarity.

12. [Container security](./chapters/12-container-security.md)  
   Run with least privilege, avoid embedding secrets, select trusted images, and understand isolation limits.

13. [Logs, debugging, and troubleshooting](./chapters/13-logs-debugging-and-troubleshooting.md)  
   Use logs, inspect, events, exec, Compose status, and repeatable checks to find failures.

14. [Containerizing an application](./chapters/14-containerizing-an-application.md)  
   Package a small application with a health endpoint, a persistent data service, and repeatable configuration.

15. [Local development and test workflows](./chapters/15-local-development-and-test-workflows.md)  
   Use Compose for local dependencies, tests, profiles, rebuilds, and cleanup.

16. [Production deployment and operations](./chapters/16-production-deployment-and-operations.md)  
   Prepare images and runtime configuration for deployment, monitoring, updates, and recovery.

## Reference chapters

- [All code samples](./chapters/98-all-code-samples.md) collects examples from the core chapters.
- [Complete questions and answers](./chapters/99-complete-q-and-a.md) gathers review questions across the notes.

## How to use these notes

Follow the chapters in order when learning Docker, or open the section that matches a task you are working on. Try commands with disposable data first, inspect the resulting container or network, then adapt the configuration to the application.

## Main references

- [Docker documentation](https://docs.docker.com/)
- [Docker Engine](https://docs.docker.com/engine/)
- [Docker CLI reference](https://docs.docker.com/reference/cli/docker/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Build](https://docs.docker.com/build/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Docker security](https://docs.docker.com/engine/security/)

## License

These notes are available under the [MIT License](./LICENSE).

## Links

- Portfolio: https://www.ashishranjan.net
- GitHub: https://github.com/a2rp
- CodePen: https://codepen.io/ash1198
- LinkedIn: https://www.linkedin.com/in/aashishranjan
- Facebook: https://www.facebook.com/theash.ashish/
- YouTube: https://www.youtube.com/@ashishranjan-ashz?sub_confirmation=1
- Email: mailto:ash.ranjan09@gmail.com

## Support

- Support: https://a2rp-donation-page.netlify.app/
- Buy Me a Coffee: https://buymeacoffee.com/ashishranjan
- Patreon: https://www.patreon.com/ashishranjan
