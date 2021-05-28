## Build

There  are two ways to build a docker image:

1) Commit changes made in a Docker container
   
    - Spin-up a container from a base image
      ```shell
        docker run -it debian:buster
        ```
      This will download the debian buster image and launch a shell into the new container.
      
    - Install Git package (as an example) in the container
      ```shell
        apt-get update && apt-get install -y git
        ```
      To exit the container shell and return to the host machine:
      ```shell
        exit
        ```
      
    - Commit changes made in the container
        
      First, find the ID of the container:
      ```shell
        docker ps -a
      ```
      then run the commit command
      ```shell
        docker commit f5dbaaf2b82e danstest/buster:1.00
      ```
2) Create a Dockerfile
    
    This is a text document that contains all instructions required to build an image. Each instruction will build a 
    new layer to the image and tell Docker what to do when building the image. Dockerfile [best practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/).
   
    - Create a new directory and touch a file called 'Dockerfile'
      ```dockerfile
        FROM debian:buster
        RUN apt-get update
        RUN apt-get install -y git
        RUN apt-get install -y vim
        ```
    - build the images using docker build
        ```shell
        docker build -t danstest/debian .
        ```
      The '-t' flag tags the build. See 'docker build --help' for more information.

### The Docker Build Context

The Docker build command takes the path of the build context as an argument. When the build starts, the docker client packs all
files in the build context into a tarball then transfers the tarball to the docker daemon. By default, Docker searches for
the Dockerfile in the build context path.

### Concepts

- Images are persistent and readonly.
- Containers are writable and ephemeral
- When building from a Dockerfile, the docker daemon creates a new container from the base image, executes the first 
  command, commits this change to a new image and discard the container. The newly created image is then used as the base 
  for the next instruction, this process continues for all instructions in the Dockerfile.



### Build command reference

- build an image
    ```shell
    docker build -t <name:tag> [path]
    ```

- tag an image
    ```shell
    docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
    ```

- commit changes made within a container to a new image
    ```shell
    docker commit <container_id> <name:tag>
    ```

## Container commands

- list all containers
    ```shell
    docker container ls -a
    ```

- stop a running container
    ```shell
    docker container ástop <container_id>
    ```

- stop all containers
    ```shell
    docker container stop $(docker container ls -aq)
    ```

- remove a container
    ```shell
    docker container rm <container_id>
    ```

- show detailed information about a container
    ```shell
    docker inspect <container_id>
    ```    

## image commands

- list all images
    ```shell
    docker images
    ```
    or 
    ```shell
    docker image ls
    ```

- remove container(s)
    ```shell
    docker image rm [image_id1] [image_id1]
    ```

## Layer commands

- list all layers in an image
    ```shell
    docker history <image_name>
    ```
    e.g.
    ```shell
    docker history busybox
    ```
  
# Udemy course: Docker crash course

