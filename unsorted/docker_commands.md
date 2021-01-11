## build
docker build -t [name:tag] [path]

- tag an image
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]


## Container commands

- list all containers
docker container ls -a

- stop a running container
docker container stop [container_id]

- stop all containers
docker container stop $(docker container ls -aq)

- remove a container
docker container rm [container_id]


## image commands

- list all images
docker images
or 
docker image ls

- remove container(s)

docker image rm [image_id1] [image_id1]



