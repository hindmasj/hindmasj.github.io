# Push Your Images To Docker Hub

[Home](../index) | [Up](wsl-index)

## Build An Image

### In Docker

(TODO)

### In Docker Compose

In *docker-compose.yml*

```
  services:
    myservice:
      build: ./build-myservice
```      

In *build-myservice* put *Dockerfile*.

Then ``docker compose build``.

## Create Your Own Repository

You only need to do this once per project.

1. Go to [Docker Hub](https://hub.docker.com/)
1. Sign in.
1. Create repository.

### Push Your Image

```
docker login --username <username>
docker image tag my-image-name:latest <username>/<reponame>
docker push <username>/<reponame>
```

---
[Home](../index) | [Up](wsl-index)
