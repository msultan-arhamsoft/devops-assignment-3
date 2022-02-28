# Assignment 3 #
## Submitted by Mahmood Sultan ##

### Build the images for Microservices (remember to edit code for frontend) and push images to Dockerhub and write commands to run backedn & frontend and they should run fine ###

- Build the image for python (backend) app:

docker image build -t mahmoodsultan/devops-assignment-3:backend-app .

- Build the image for node (frontend) app: 

docker image build -t mahmoodsultan/devops-assignment-3:frontend-app .

- Create the network to run the both apps on:

docker netwok create assignment-3

- Start the backend: 

docker run -it --rm --name backend --network assignment-3 --init -p 9090:8080 mahmoodsultan/devops-assignment-3:backend-app

- Start the frontend: 

docker run -it --rm --name frontend  --network assignment-3 --init -p 9091:8080 mahmoodsultan/devops-assignment-3:frontend-app

- Accessing the frontend works well, because both containers are communicating through the same network:

localhost:9091/hello
localhost:9091/instructor/{id} =>  id=1/2
localhost:9091/student{id} => id=1/2/3/4/5/6/7

### How to leverage cache using Dockerfiles ###

While building an image, docker steps through all the instructions in the Dockerfile. The commands are executed in order of their placement in the file. At each Dockerfile line/command, Docker searches for the existing image in the cache to use the cached image instead of creating a new duplicate image. 

The basic flow that docker follows is:
- Starts with the parent image that is already in the cache.
- Next instruction is compared against all child images derived from that base image to see if one of them was built using the exact same instruction.
- If found, docker moves to the next instruction without making any change.
- If not found, the cache is invalidated and all the next instrutions are re built in new images.

This way leveraging the cache, enhances the images creation process.

### What are multi-stage builds ###

- We can create images which are derived from multiple bases. Which can help us reduce the size of the final build.
- For creating Multi-stage builds we write multiple FROM statements in the Dockerfile.
- We can split the Dockerfile into multiple sections while writing the multi-stage builds.
- In multi-stage builds, each stage consists of its own FROM statement.
- Using multiple FROM statements, we can involve more than one image in the builds.
- Every stage is build in the sequience it is defined in the file, so we can copy the output of the previous layer into the next.


### Explain containers vs Image ###

Images:
- Images are packaged form of files.
- Images consists of layers.

Containers:
- Containers are running form of an image.
- Can run multiple containers from one image.


### Explain RUN vs CMD vs Entrypoint ###

RUN:
- Is a build time command.
- Is used to run the commands while building an image.
- Can not be overwritten while running containers.

CMD:
- Is the run time command.
- Specifies which command should be executed when the container runs.
- Is passed as an argument to the ENTRYPOINT.
- If ENTRYPOINT is not defined, CMD is used as the whole command
- Can be overwritten while running containers.

ENTRYPOINT:
- Is the run time command.
- Specifies which command should be executed when the container runs.
- Any command defined as CMD, is passed as an argument to the ENTRYPOINT.
- If CMD is not defined, but ENTRYPOINT is defined, ENTRYPOINT is considered as the full comamnd.
- Can be overwritten while running containers.

### Improve the Dockerfile for python Application given in slides using the Dockerfile & then improve it and share image size & estimated build time for it ###

FROM python:3.6-alpine as msultan

RUN pip install flask

FROM msultan

LABEL name="Python Application" \   
     maintainer="Mahmodd Sultan <m.sultan@arhamsoft.com>" \
     summary="A Sample Python application"


WORKDIR /app

COPY app.py ./

EXPOSE 8080

CMD [ "python", "./app.py" ]

### Run mysql container using the official image, by persisting data and passing environment variables to set username & passwordâ€¦ You can see the information of how to persist and information here ### 

https://hub.docker.com/_/mysql

- Create volume named mysql, in order to persist the data using docker volume: 

docker volume create mysql

- Run mysql container with username = msultan and password = mahmoodSultanSecret, in the detached mode with the data persisted: 

docker container run --name mysql-server -v mysql:/var/lib/mysql -e "MYSQL_USER=msultan" -e "MYSQL_PASSWORD=mahmoodSultanSecret" -d mysql

- check docker container status: 

docker container ls

- check docker container logs: 

docker container logs mysql-server