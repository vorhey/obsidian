| Command                                               | Description                                                   | Example                                   |
| ----------------------------------------------------- | ------------------------------------------------------------- | ----------------------------------------- |
| `docker ps`                                           | List running containers                                       | `docker ps`                               |
| `docker ps -a`                                        | List all containers (including stopped ones)                  | `docker ps -a`                            |
| `docker images`                                       | List all local images                                         | `docker images`                           |
| `docker pull`                                         | Pull an image from a registry                                 | `docker pull ubuntu:latest`               |
| `docker build .`                                      | Build an image from a Dockerfile                              | `docker build .`                          |
| `docker build --progress=plain .`                     | Build an image from a Dockerfile with progress flag activated | `docker build --progress=plain .`         |
| `docker build -t <tag-name> .`                        | Build an image from a Dockerfile with a tag                   | `docker build jherrerau/redis:latest .`   |
| `docker exec -it <container-id> <command>`            | Executes an additional command in a container                 | `docker exec -it 1234abc sh`              |
| `docker run`                                          | Run a container from an image                                 | `docker run -d -p 80:80 nginx`            |
| `docker run -d`                                       | Run a container in detached mode                              | `docker run -d nginx`                     |
| `docker run -it <image> <command>`                    | Run a container from an image and executes a command          | `docker run -it ubuntu:latest sh`         |
| `docker run -p <local-port>:<container-port> <image>` | Run a container with port mapping                             | `docker run -p 8080:8080 nodejs-server`   |
| `docker run -v <working-dir>:<container-dir> <image>` | Run a container with a volume mapping                         | `docker run -v $(pwd):/app 28cdfn`        |
| `docker run -v <container-dir> <image>`               | Run a container without a volume mapping (anonymous)          | `docker run -v /app/node_modules 28cdfn`  |
| `docker stop`                                         | Stop a running container                                      | `docker stop container_id`                |
| `docker rm <container-id>`                            | Remove a container                                            | `docker rm container_id`                  |
| `docker rmi <image-id>`                               | Remove an image                                               | `docker rmi image_id`                     |
| `docker logs <container-id>`                          | View logs of a container                                      | `docker logs container_id`                |
| `docker commit -c <command>`                          | Creates an image from a container with an specific command    | `docker commit -c 'CMD ["redis-server"]'` |
| `docker-compose up`                                   | Start services defined in a docker-compose.yml file           | `docker-compose up -d`                    |
| `docker-compose down`                                 | Stop services defined in a docker-compose.yml file            | `docker-compose down`                     |
| `docker network ls`                                   | List Docker networks                                          | `docker network ls`                       |