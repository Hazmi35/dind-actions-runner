# dind-actions-runner
This is an example of GitHub Actions runner setup with real DinD

## How To Setup

1. Clone this repository

2. Copy `.env.example` to `.env`, and fill out the required variables as you like

    2.1 Available environment variables are [here](https://github.com/myoung34/docker-github-actions-runner#environment-variables)

3. Modify docker-compose file as you like

    3.1 For example, setting the runner image tag, which exists [here](https://github.com/myoung34/docker-github-actions-runner#docker-artifacts)

    3.2 You can disable container auto-update by removing `com.centurylinklabs.watchtower.scope=actions-runner` label

    3.3 You can modify how many actions runners to spawn by editing deploy.replicas option

4. Run `dind-actions-runner` with `$ docker compose up -d` (Compose v2) or `$ docker-compose up -d`
5. You can see container logs with `$ docker compose logs -f` (Compose v2) or `$ docker-compose logs -f`
6. Turn off `dind-actions-runner` with `$ docker compose stop` (Compose v2) or `$ docker-compose stop`
7. Remove/destroy `dind-actions-runner` with `$ docker compose down` (Compose v2) or `$ docker-compose down`
8. That's all you need to know. 🎉

## Differences with using Docker host's sock

Most setup or example on the Internet, uses something like this:
```sh
$ docker run -d \
    -v /var/run/docker.sock:/var/run/docker.sock:ro
    runner-image:latest
```

This is fine, and runner container can do Docker stuffs, but the Docker Daemon would be shared with Host machine. Some people like me doesn't like to do this because it clutters the Host's Docker Daemon.

Many people called this Docker-in-Docker (DinD), but in fact it is not true DinD, and I called it Docker-from-Docker or Docker socket binding

### The solution

Docker itself provides a container image that implements [Docker-in-Docker hack](https://github.com/moby/moby/blob/master/hack/dind): [docker:dind](https://hub.docker.com/_/docker)

Using that container image, we can spin up a new Docker daemon inside a container, which would be used for GitHub Actions Runner containers.

There are two ways to do this:
1. Share Docker socket from DinD container to GitHub Actions Runner containers (Prone to breakdown, and unix permissions issues)
2. Make GitHub Actions Runner containers communicate with Docker daemon from DinD container via TCP (Recommended way of controlling remote docker daemon)

This repository use number 2.

_* Trivia: Docker-in-Docker also exists in VSCode's Remote - Containers (devcontainer), as a `Additional features`: [Reference](https://github.com/microsoft/vscode-dev-containers/tree/main/containers/docker-in-docker)_

## Credits

This setup uses myoung34's runner image ([myoung34/docker-github-actions-runner](https://github.com/myoung34/docker-github-actions-runner))

I do not provide container images that are used here. They had their own licenses.