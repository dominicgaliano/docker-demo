# Docker Notes

- To containerize an application:
  - add Dockerfile to root directory with build instructions. [ref](https://docs.docker.com/engine/reference/builder/)
  - build image `docker build -t your-tag-name-here .`

- To run a container:
  - `docker run -dp 127.0.0.1:3000:3000 getting-started`
  - `-d` tag runs container in background
  - `-p` tag creates port mapping between the host and container (use 0.0.0.0 when not running locally)
  - for more run tags, see `docker run --help`

- Other useful commands
  - `docker ps` list running docker containers
  - `docker stop <container-id>` stop container
  - `docker rm <container-id>` remove container
  - `docker logs -f <container-id>` watch logs

## Data Persistence

- Volumes
  - A docker managed opaque data store
  - create volume `docker volume create <name>`
  - example volume mount:
    - `docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started`

## Bind Mounts

- When working on an application, you can use a bind mount to mount source code into the container.
- Very useful to prevent needing to stop and start instances when source code changes are made.
- Example bind mount
  - `docker run -it --mount type=bind,src="$(pwd)",target=/src ubuntu bash`
  - runs docker container with interactive terminal
  - all files within /src are bound to container
- Another example, this mounts source code into container, installs dependencies, and then runs development script (with nodemon)

```bash
docker run -dp 127.0.0.1:3000:3000 \
    -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"
```

## Multi-container apps

- In general, it is good practice to run each process in it's own container
- Containers talk to each other using container networking
  - create a network `docker network create <network name>`
  - container running SQL database mount example:
    - `-e` tag sets env variables
    - `-v` tag bind mount volume

```bash
docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:8.0
```
 
- Hint: For debugging network issues, the nicolaka/netshoot container has useful tools.
  - `docker run -it --network todo-app nicolaka/netshoot`
- Note: While using env vars to set connection settings is generally accepted for development, it's highly discouraged when running applications in production. [Diogo Monica,](https://blog.diogomonica.com//2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/) a former lead of security at Docker, wrote a fantastic blog post explaining why.

### Docker Compose

- Tool to define and share multi-container applications
- Create a YAML file to define services and spin up everything with a single command 
- Note to self: I created a good example `compose.yaml` file in the docker getting-started-app tutorial project.
- `docker compose up -d` compose
- `docker compose down` shut down

## Image Building Best Practices

- `docker image history <image-name>` shows the command that was used to create each layer within an image.
