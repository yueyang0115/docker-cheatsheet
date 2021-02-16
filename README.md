# docker-cheatsheet

Reference: https://github.com/wsargent/docker-cheat-sheet  
Youtube Tutorial: [Docker Tutorial for Beginners](https://www.youtube.com/watch?v=fqMOX6JJhGo&list=RDCMUC8butISFwT-Wl7EV0hUK0BQ&start_radio=1&t=18)  

## Image
Images are just templates for docker containers.
* [`docker build`](https://docs.docker.com/engine/reference/commandline/build) creates image from Dockerfile.
* [`docker images`](https://docs.docker.com/engine/reference/commandline/images) shows all images.
* [`docker commit`](https://docs.docker.com/engine/reference/commandline/commit) creates image from a container, pausing it temporarily if it is running.
* [`docker rmi`](https://docs.docker.com/engine/reference/commandline/rmi) removes an image.
* [`docker tag`](https://docs.docker.com/engine/reference/commandline/tag) tags an image to a name (local or registry).

## Container
Your basic isolated Docker process. Containers are to Virtual Machines as threads are to processes. Or you can think of them as chroots on steroids.
List container

## Registry
A [Docker registry](https://docs.docker.com/get-started/overview/#docker-registries) stores Docker images. Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default.   
When you use the docker pull or docker run commands, the required images are pulled from your configured registry. When you use the docker push command, your image is pushed to your configured registry.

* [`docker login`](https://docs.docker.com/engine/reference/commandline/login) to login to a registry, use `--username` or `-u` to specify username.
* [`docker logout`](https://docs.docker.com/engine/reference/commandline/logout) to logout from a registry.
* [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull) pulls an image from registry. If no tag is provided, Docker Engine uses the :latest tag as a default.
* [`docker push`](https://docs.docker.com/engine/reference/commandline/push) pushes an image to the registry from local machine.

### Example: [Pull an image from Docker Hub](https://docs.docker.com/engine/reference/commandline/pull/#pull-an-image-from-docker-hub)
This command pulls the debian:latest image:
```
$ docker pull debian

Using default tag: latest
latest: Pulling from library/debian
fdd5d7827f33: Pull complete
a3ed95caeb02: Pull complete
Digest: sha256:e7d38b3517548a1c71e41bffe9c8ae6d6d29546ce46bf62159837aad072c90aa
Status: Downloaded newer image for debian:latest
```  
Docker images can consist of multiple layers. In the example above, the image consists of two layers; fdd5d7827f33 and a3ed95caeb02.  
Layers can be reused by images. For example, the debian:jessie image shares both layers with debian:latest. Pulling the debian:jessie image therefore only pulls its metadata, but not its layers, because all layers are already present locally:  
```
$ docker pull debian:jessie

jessie: Pulling from library/debian
fdd5d7827f33: Already exists
a3ed95caeb02: Already exists
Digest: sha256:a9c958be96d7d40df920e7041608f2f017af81800ca5ad23e327bc402626b58e
Status: Downloaded newer image for debian:jessie
```
To see which images are present locally, use the docker images command:  
```
$ docker images

REPOSITORY   TAG      IMAGE ID        CREATED      SIZE
debian       jessie   f50f9524513f    5 days ago   125.1 MB
debian       latest   f50f9524513f    5 days ago   125.1 MB
```
In the example above, debian:jessie and debian:latest have the same image ID because they are actually the same image tagged with different names. Because they are the same image, their layers are stored only once and do not consume extra disk space.  

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
