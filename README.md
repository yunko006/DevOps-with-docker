# [DevOps with docker course](https://devopswithdocker.com/)

# Part 1

# Exercise 1.1: Getting started

Since we already did "Hello, World!" in the material let's do something else.

Start 3 containers from an image that does not automatically exit (such as nginx) in detached mode.

Stop two of the containers and leave one container running.

Submit the output for docker ps -a which shows 2 stopped containers and one running.

Solutions : 
![1.1](image-1.png)


# Exercise 1.2: Cleanup

We have containers and an image that are no longer in use and are taking up space. Running docker ps -as and docker images will confirm this.

Clean the Docker daemon by removing all images and containers.

Submit the output for docker ps -a and docker images

Solution :
![1.2.1](image.png)
![1.2.2](image-2.png)


# Exercise 1.3 : Secret message
Now that we've warmed up it's time to get inside a container while it's running!

Image devopsdockeruh/simple-web-service:ubuntu will start a container that outputs logs into a file. Go inside the container and use tail -f ./text.log to follow the logs. Every 10 seconds the clock will send you a "secret message".

Submit the secret message and command(s) given as your answer.

Shell 
```bash
docker run -d -it --name ubuntu devopsdockeruh/simple-web-service:ubuntu

docker exec -it  ubuntu
```

Reponse : Secret message is: 'You can find the source code here: https://github.com/docker-hy'


# Exercise 1.4 : 

Solution :
Shell 
```bash
docker run -d -it --name script ubuntu sh -c "while true; do echo 'Input website:'; read website; echo 'Searching..'; sleep 1; curl http://$website; done"
* note : "" car sous windows sinon ''
docker exec -it script

apt-get update
apt-get -y install curl
```
Ouvrir nouveau terminal 
shell 
```bash
docker attach script
helsinki.fi
```

# Exercise 1.5 : 

Part 1 : 
![1.51](image-6.png)

l'image ubuntu est bcp plus grosse que l'image alpine

Part 2 : 
Shell
```bash
docker run -d --name alpine devopsdockeruh/simple-web-service:alpine
docker exec -it alpine
```

Output
```
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
```


# Exercise 1.6 

go to : https://hub.docker.com/r/devopsdockeruh/pull_exercise

Shell 
```bash
docker run -it devopsdockeruh/pull_exercise
basics
```

Output 
```
You found the correct password. Secret message is:
"This is the secret message"
```
# Exercise 1.7: Image for script

Solution : 

1-7.sh
```bash
#!/bin/sh
while true
do
  echo "Input website:"
  read website; echo "Searching.."
  sleep 1; curl http://$website
done
```

> Sous windows les fichiers sont save avec le format de fin de ligne CRLF alors que dans notre cas dans les systeme UNIX les fichers doivent etre save avec le format de fin de ligne LF. On peut le faire avec VScode : ouvrir le fichier -> ctrl + shift + p (ouvrir la palette de commande) -> change end of line sequence -> choisir LF

Dockerfile 
```bash
FROM ubuntu:20.04

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# Copy the hello.sh file from this location to /usr/src/app/ creating /usr/src/app/hello.sh
COPY 1-7.sh .

# Execute a command with `/bin/sh -c` prefix.
RUN apt-get update
RUN apt-get -y install curl

# Alternatively, if we skipped chmod earlier, we can add execution permissions during the build.
RUN chmod +x 1-7.sh

# When running docker run the command will be ./hello.sh
CMD ./1-7.sh
```

Shell 
```bash
docker build . -t curler
docker run -it curler
helsinki.fi
```


# Exercise 1.8 : Two line Dockerfile
By default our devopsdockeruh/simple-web-service:alpine doesn't have a CMD. It instead uses ENTRYPOINT to declare which application is run.

We'll talk more about ENTRYPOINT in the next section, but you already know that the last argument in docker run can be used to give a command or an argument.

As you might've noticed it doesn't start the web service even though the name is "simple-web-service". A suitable argument is needed to start the server!

Try docker run devopsdockeruh/simple-web-service:alpine hello. The application reads the argument "hello" but will inform that hello isn't accepted.

In this exercise create a Dockerfile and use FROM and CMD to create a brand new image that automatically runs server.

The Docker documentation CMD says a bit indirectly that if a image has ENTRYPOINT defined, CMD is used to define it the default arguments.

Tag the new image as "web-server"

Return the Dockerfile and the command you used to run the container.

Running the built "web-server" image should look like this:
```bash
$ docker run web-server
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
- using env:   export GIN_MODE=release
- using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /*path                    --> server.Start.func1 (3 handlers)
[GIN-debug] Listening and serving HTTP on :8080

```

    - We don't have any method of accessing the web service yet. As such confirming that the console output is the same will suffice.

    - The exercise title may be a useful hint here.

Solution : 

Dockerfile 
```bash
FROM devopsdockeruh/simple-web-service:alpine
CMD server
```

Shell 
```bash
docker build . -t web-server
docker run -it web-server
```

# Exercise 1.9: Volumes

In this exercise we won't create a new Dockerfile.

Image devopsdockeruh/simple-web-service creates a timestamp every two seconds to /usr/src/app/text.log when it's not given a command. Start the container with bind mount so that the logs are created into your filesystem.

Submit the command you used to complete the exercise.

Hint: read the note that was made just before this exercise!

Solution :
Shell (windows)
```bash
ni log.txt

docker run -v "$(pwd)/log.txt:/usr/src/app/text.log" devopsdockeruh/simple-web-service
```

Check log.txt pour voir si ca écrit bien dedans.


# Exercise 1.10: Ports open

In this exercise, we won't create a new Dockerfile.

The image devopsdockeruh/simple-web-service will start a web service in port 8080 when given the argument "server". In Exercise 1.8 you already did a image that can be used to run the web service without any argument.

Use now the -p flag to access the contents with your browser. The output to your browser should be something like: { message: "You connected to the following path: ...

Submit your used commands for this exercise.

Solution : 

Shell
```bash
docker run -p 8080:8080 web-server
```

ouvrir 127.0.0.1:8080/ il y a normalement : message	"You connected to the following path: /"


# Exercise 1.11: Spring

Create a Dockerfile for an old Java Spring project that can be found from the course repository.

The setup should be straightforward with the README instructions. Tips to get you started:

Use openjdk image FROM openjdk:_tag_ to get Java instead of installing it manually. Pick the tag by using the README and Docker Hub page.

You've completed the exercise when you see a 'Success' message in your browser.

Submit the Dockerfile you used to run the container.

Solution : 

Dockerfile 
```bash
# get java 8
FROM openjdk:8

EXPOSE 8080

WORKDIR usr/src

COPY . .

# build the project
RUN ./mvnw package

# run
CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]
```

Shell 
```bash
docker build . -t spring-project

docker run -p 8080:8080 spring-project
```

Ouvrir 127.0.0.1:8080 cliquer sur le button, voir success!


# Mandatory Exercise 1.12: Hello, frontend!

A good developer creates well-written READMEs. Such that they can be used to create Dockerfiles with ease.

Clone, fork or download the project from https://github.com/docker-hy/material-applications/tree/main/example-frontend.

Create a Dockerfile for the project (example-frontend) and give a command so that the project runs in a Docker container with port 5000 exposed and published so when you start the container and navigate to http://localhost:5000 you will see message if you're successful.

    note that the port 5000 is reserved in the more recent OSX versions (Monterey, Big Sur), so you have to use some other host port

Submit the Dockerfile.

As in other exercises, do not alter the code of the project

    TIP: The project has install instructions in README.

    TIP: Note that the app starts to accept connections when "Accepting connections at http://localhost:5000" has been printed to the screen, this takes a few seconds

    TIP: You do not have to install anything new outside containers.

## Solution : 

### Readme : 
```md
Prerequisites

Install node.

Example node install instructions for LTS node 16.x:

curl -sL https://deb.nodesource.com/setup_16.x | bash
sudo apt install -y nodejs

Check your install with node -v && npm -v

Install all packages with npm install
Starting in production mode
Exercise 1.12 -> to run the project

First you need to build the static files with npm run build

This will generate them into build folder.

An example for serving static files:

Use npm package called serve to serve the project in port 5000:

    install: npm install -g serve
    serve: serve -s -l 5000 build

Test that the project is running by going to http://localhost:5000
```

### Dockerfile :

Dockerfile 
```bash
FROM node:16

WORKDIR /usr/src/app

EXPOSE 5000

COPY . .

RUN npm install
RUN npm run build
RUN npm install -g serve

CMD ["serve", "-s", "-l", "5000", "build"]
```

### Shell 
```bash
docker build . -t example-frontend

docker run -p 5000:5000 example-frontend
```


# Mandatory Exercise 1.13: Hello, backend!

Clone, fork or download a project from https://github.com/docker-hy/material-applications/tree/main/example-backend.

Create a Dockerfile for the project (example-backend). Start the container with port 8080 published.

When you start the container and navigate to http://localhost:8080/ping you should get a "pong" as response.

Submit the Dockerfile and the command used.

Do not alter the code of the project

## Solution : 

Dockerfile 
```bash
FROM golang:1.16

EXPOSE 8080

WORKDIR /app

COPY . .

RUN go build

RUN go test

CMD ["./server"]

```

Shell 
```bash
docker build . -t example-backend

docker run -p 8080:8080 example-backend
```


# Mandatory Exercise 1.14: Environment

## Solution : 

Dockerfile (frontend)
```bash
FROM node:16

WORKDIR /usr/src/app

EXPOSE 5000

COPY . .

ENV REACT_APP_BACKEND_URL="http://localhost:8080"

RUN npm install
RUN npm run build
RUN npm install -g serve

CMD ["serve", "-s", "-l", "5000", "build"]

```

Dockerfile (back end)
```bash
FROM golang:1.16

EXPOSE 8080

WORKDIR /app

COPY . .

ENV REQUEST_ORIGIN="http://localhost:5000"

RUN go build

# RUN go test

CMD ["./server"]

```
>surtout ne pas mettre ENV REQUEST_ORIGIN="http://localhost:5000/" le / a la fin nique tout car c'est une url pur ! Alors que ENV REQUEST_ORIGIN="http://localhost:5000" permet de prendre toutes les url du site.

Shell 
```bash
docker build . -t frontend
docker build . -t backend

docker run -p 5000:5000 frontend
docker run -p 8080:8080 backend
```

# Part 2 

# Exercise 2.1

Let us now leverage the Docker Compose with the simple webservice that we used in the Exercise 1.3

Without a command devopsdockeruh/simple-web-service will create logs into its /usr/src/app/text.log.

Create a docker-compose.yml file that starts devopsdockeruh/simple-web-service and saves the logs into your filesystem.

Submit the docker-compose.yml, make sure that it works simply by running docker compose up if the log file exists.

## Solution 

créer un fichier text.log et un fichier docker-compose.yml

docker-compose.yml
```bash
version: '3.8'

services:
  logger:
    image: devopsdockeruh/simple-web-service
    build: .
    volumes:
      - ./text.log:/usr/src/app/text.log
    container_name: logger
```
verifier que le ficher text.log a ete rempli

Shell (windows)
```bash
ni text.log

docker compose up 
```

# Exercise 2.2

Read about how to add command to docker-compose.yml from the documentation.

The familiar image devopsdockeruh/simple-web-service can be used to start a web service.

Create a docker-compose.yml and use it to start the service so that you can use it with your browser.

Submit the docker-compose.yml, make sure that it works simply by running docker compose up

## Solution

docker-compose.yml
```bash
version: '3.8'

services:
  web-server:
    image: devopsdockeruh/simple-web-service
    ports:
      - 8080:8080
    command: [ "server" ]
    container_name: web-server
```

# Mandatory Exercise 2.3

As we saw previously, starting an application with two programs was not trivial and the commands got a bit long.

In the previous part we created Dockerfiles for both frontend and backend of the example application. Next, simplify the usage into one docker-compose.yml.

Configure the backend and frontend from part 1 to work in Docker Compose.

Submit the docker-compose.yml

## Solution 

mettre les dossiers example-backend et example-frontend dans le meme dossier (ex: example-project) et créer docker-compose.yml
donc structure tel que : 
example-projet/
-- example-backend/
-- example-frontend/
-- docker-compose.yml

docker-compose.yml
```bash
version: '3.8'

services:
  backend:
    build: ./example-backend
    # environment:
    #   - REQUEST_ORIGIN="http://localhost:5000"
    ports:
      - 8080:8080
    container_name: backend

  frontend:
    build: ./example-frontend
    # environment:
    #   - REACT_APP_BACKEND_URL="http://localhost:8080"
    ports:
      - 5000:5000
    container_name: frontend
```

