# Docker

Docker uses container ie an isolated environment for running applications

## Basic Commands

### Images:

- docker pull <image name | image_url> : command to pull images from docker hub or any cloud service
- docker push <image name | image_url> : command to push images to docker hub or any cloud service
- docker run <image name | image_url> : this command combines the pull and start i.e it first checks locally if found or else looks in docker_hub pulls it and starts a new container every time.
- docker start <image name | imageId> : starts a container
- docker stop <image name | imageId> : stops a container
- docker images : displays all images
- docker image ls
- docker rmi #imageId: deletes the image

Examples with docker run and various options:

- docker run -d: starts a container in detached mode
- docker run -p<local_port>:<image_port>: assign a port to the container
- docker run --name <image_name>: assign a name to the container
- docker run --net <network_name>: assign a network name to the container
- docker run -e SECRET=abcd: env config

### Containers

- docker ps : displays running containers
- docker ps -a : all container stopped and running
- docker ps -a | grep <container_name> : easily find
- docker rm #containerId : deletes the container

### TroubleShooting containers

- docker logs <containerId | containerName>
- docker exec -it <containerId | containerName> (bash or sh)

### Networks

- docker network create <newtwork_name>
- docker network ls: display list of network

### Volumes

Typically when a container is deleted or stopped the data will be gone its like we are just temporarily storing the file/data so volumes help us store files/data locally and sync every time we start a container.

- docker volume create <volume_name>
- docker volume ls: display list of volumes

### Example with all the options used together

```
docker run -d \ <!-- detached mode --->
-p8081:8081 \ <!-- assigned a port>
-e ME_CONFIG_USERNAME=aniket \ <!-- env configs --->
-e ME_CONFIG_PASSWORD=password \
--net mongo-network \ <!-- newtork assigned -->
--name mongo-express \ <!-- custom name -->
-e ME_CONFIG_SERVER=mongo \ (container name for mongo)
-v /mongo/db:/data/db <!-- volume -->
mongo <!-- image name -->
```

### Dockerfile

Some basics for creating a Dockerfile

FROM (use to import all images we want)
ENV :environment
RUN can exedcute linux commands (eg cd into some direct)
COPY : executes on host machine
CMD: it is the entrypoint command

Example:

```
FROM node:14-slim

WORKDIR /usr/src/app

COPY ./package.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

RUN vs CMD we can have multiple run command but only single cmd

### Build image using Dockerfile

docker build -t <image_name> <dockerfile_location>
eg: docker build -t my-app:1.0.0 ./

Pusing to private repository

- docker login (new window will popup for authentication)
- docker tag <image_name>:<image_tag> <domain_name>/<image_name>:<image_tag>
  eg: docker tag my-app:1.0.0 https.xyz/my-app:1.0.0
- Now here a new image will be created just the copy of previous
  with same files and structure but with push domain
- docker push <image_name>:<image_tag>

### docker-compose

Docker compose helps to compose different images into a single file

- docker-compose up (make sure to be on the directory where your compose.yml is located)
- docker-compose down

Example of compose.yml file

```
version: '3'
services:
  todo-api-server:
    image: todo-api-server
    build: ./backend/
    ports:
      - "4000:4000"
    networks:
      - todo-app
    depends_on:
      - mongo
    volumes:
      - ./backend/:/usr/src/app
      - /usr/src/app/node_modules

  todo-client:
    image: todo-client
    build: ./client/
    ports:
      - "3000:3000"
    stdin_open: true
    networks:
      - todo-app
    volumes:
      - ./client/:/usr/src/app
      - /usr/src/app/node_modules

  mongo:
    image: mongo
    ports:
      - "27017:27017"
    networks:
      - todo-app
    volumes:
      - todo-data:/data/db

networks:
  todo-app:
    driver: bridge

volumes:
  todo-data:
    driver: local
```

Simply run up/down commands to start and stop the container
