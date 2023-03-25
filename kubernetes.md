# Docker

Name spacing and control groups: specific to linux operating system. When you install docker on mac it's running inside a linux virtual machine.

## Commands

- docker run `` : creating and running a container
- docker ps: list all running containers
  - --all : show all the containers that are ever created.
docker run = docker create(copy the file system) + docker start(run the startup command)
- docker system prune: delete all the containers and their images.
- docker stop : issues a sygterm command, gives time to clean up before killing
- docker kill : kills immediately.
- docker exec(run another command) -it(provide input to container) <container_id> <command>

Every process has three channels(STDIN,STDOUT,STDERR). `-i` flag attaches the stdin of the process to my terminal, `-t` just gives some formatting.

#get shell inside the container

- docker exec -it <container_id> sh

- docker run -it busybox sh: runs shell out of busybox image and skips any startup command.
Lifecycle of a container:

every container that's created(`run`) has a unique id that can be used to restart it again.

Note on flags:

-a : pipe the output to the command line.

## creating a dockerfile

specify a base image -> install some software -> give a startup command.

Dockerfile + docker build

#docker build: what's it doing



Commands:

- kubectl create -f db.yml : Create pod using 'db.yml'

- kubectl get pods : get list of all pods

Components:
a. API SERVER(events) b. scheduler(assigs pods to nodes) c.kubelet(manages resources of a node)
Mandatory: apiVersion , kind, metadata


------------

Steps for creating a multi-container application

<img width="542" alt="Screen Shot 2023-03-25 at 12 39 04 PM" src="https://user-images.githubusercontent.com/21222766/227731304-d9cf04f0-2670-4c2d-abbe-bb70f479b047.png">

1. Make a docker-dev file, build it(`docker build -f Dockerfile.dev .`)

why kubernetes: multiple different types of containers on diff quantities on various nodes.


