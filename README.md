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
RUN chmod +x exo.sh

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