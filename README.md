# docker-cheatsheet

Reference: https://github.com/wsargent/docker-cheat-sheet  
Youtube Tutorial: [Docker Tutorial for Beginners](https://www.youtube.com/watch?v=fqMOX6JJhGo&list=RDCMUC8butISFwT-Wl7EV0hUK0BQ&start_radio=1&t=18)  
Qwiklab Walkthrough: [Introduction to Docker](https://www.qwiklabs.com/focuses/1029?parent=catalog)

## Image
Images are just templates for docker containers.
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build) creates image from Dockerfile.
```
docker build .                  (random name, default tag)
docker build node-app .         (default tag)
docker build -t node-app:0.1 .
```
The ```-t``` is to name and tag an image with the ```name:tag``` syntax. The ```name``` of the image is node-app and the ```tag``` is 0.1. If you don't specify a tag, the tag will default to ```latest```.  
The ```.``` means current directory so you need to run this command from within the directory that has the Dockerfile.

* [`docker images`](https://docs.docker.com/engine/reference/commandline/images) shows all images.
* [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi) removes an image.
* [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit) creates image from a container, pausing it temporarily if it is running.
* [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag) tags an image to a name (local or registry).

## Container
Your basic isolated Docker process.  
* [`docker run`](https://docs.docker.com/engine/reference/commandline/run) creates and starts a container in one operation.
```
docker run hello-world
docker run hello-world --name my-container
docker run node-app:0.1 --name my-container -p 4000:80
```
The ```--name``` flag allows you to name the container if you like. The container Names are also randomly generated but can be specified with ```docker run --name [container-name] hello-world```.  
The ```-p``` instructs Docker to map the host's port 4000 to the container's port 80.  
The ```-d``` flag makes the container run in the background (not tied to the terminal's session).  
 
* [`docker ps`](https://docs.docker.com/engine/reference/commandline/ps) shows running containers, use `-a` to show all running and stopped containers.
* [`docker rm`](https://docs.docker.com/engine/reference/commandline/rm) deletes a container.```docker rm my-container```
* [`docker create`](https://docs.docker.com/engine/reference/commandline/create) creates a container but does not start it.
* [`docker start`](https://docs.docker.com/engine/reference/commandline/start) starts a container so it is running.
* [`docker stop`](https://docs.docker.com/engine/reference/commandline/stop) stops a running container.```docker stop my-container```

## Registry
A [Docker registry](https://docs.docker.com/get-started/overview/#docker-registries) stores Docker images. Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default.   

* [`docker login`](https://docs.docker.com/engine/reference/commandline/login) to login to a registry, use `--username` or `-u` to specify username.
* [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout) to logout from a registry.
* [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull) pulls an image from registry. If no tag is provided, Docker Engine uses the :latest tag as a default.
* [`docker push`](https://docs.docker.com/engine/reference/commandline/push) pushes an image to the registry from local machine.

### Example: [Pull an image from Docker Hub](https://docs.docker.com/engine/reference/commandline/pull/#pull-an-image-from-docker-hub)
This command pulls the debian:latest image:
```
$ docker pull debian
```  
Docker images can consist of multiple layers and layers can be reused by images.  
In the example above, the image consists of two layers. And the debian:jessie image shares both layers with debian:latest. Pulling the debian:jessie image therefore only pulls its metadata, but not its layers:  
```
$ docker pull debian:jessie
```
You will find ```jessie: Pulling from library/debian; fdd5d7827f33: Already exists; a3ed95caeb02: Already exists ```in stdout.  
Use the ```docker images``` to see which images are present locally.  
In the example above, debian:jessie and debian:latest have the **same image ID** because they are actually the same image tagged with different names.   

### Example: [Push a new image to a registry](https://docs.docker.com/engine/reference/commandline/push/#push-a-new-image-to-a-registry)
First save the new image by finding the container ID (using docker container ls) and then committing it to a new image name.
```
$ docker container commit c16378f943fe rhel-httpd:latest
```
Now, push the image to the registry using the image ID. In this example the registry is on host named registry-host and listening on port 5000. To do this, tag the image with the host name or IP address, and the port of the registry:  
```
$ docker image tag rhel-httpd:latest registry-host:5000/myadmin/rhel-httpd:latest
$ docker image push registry-host:5000/myadmin/rhel-httpd:latest
```
Check that this worked by running:  
```
$ docker image ls
```
You should see both rhel-httpd and registry-host:5000/myadmin/rhel-httpd listed.  
