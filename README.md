### Dockerfile Example

``` bash
FROM debian:buster

# Add debian backports repo for wireguard packages
RUN echo "deb http://deb.debian.org/debian/ buster-backports main" > /etc/apt/sources.list.d/buster-backports.list

# Install wireguard packges
RUN apt-get update && \
 apt-get install -y --no-install-recommends wireguard-tools iptables nano net-tools procps openresolv inotify-tools && \
 apt-get clean

# Add main work dir to PATH
WORKDIR /scripts
ENV PATH="/scripts:${PATH}"

# Use iptables masquerade NAT rule
ENV IPTABLES_MASQ=1

# Watch for changes to interface conf files (default off)
ENV WATCH_CHANGES=0

# Copy scripts to containers
COPY install-module /scripts
COPY run /scripts
COPY genkeys /scripts
RUN chmod 755 /scripts/*

# Wirguard interface configs go in /etc/wireguard
VOLUME /etc/wireguard

# Normal behavior is just to run wireguard with existing configs
CMD ["run"]
```

### Frequently used Dockerfile commands -
`FROM` - Defines a base image, it can be pulled from docker hub  
(for example- if we want to create a javascript application with node as backend then we need to have node as a base image, so it can run node application.)
`RUN` - Executes command in a new image layer( we can have multiple run commands )  
`CMD` - Command to be executed when running a container( It is asked to have one CMD command, If a Dockerfile has multiple CMDs, it only applies the instructions from the last one.  
`EXPOSE` - Documents which ports are exposed (It is only used for documentation)  
`ENV` - Sets environment variables inside the image  
`COPY` - It is used to copy your local files/directories to Docker Container.  
`ADD` - It is more feature-rich version of the COPY instruction. COPY is preferred over ADD. Major difference b/w ADD and COPY is that ADD allows you to copy from URL that is the source can be URL but in COPY it can only have local ones.  
`ENTRYPOINT` - Define a container's executable (You cannot override and ENTRYPOINT when starting a container unless you add the --entrypoint flag.)  
`VOLUME` - It defines which directory in an image should be treated as a volume. The volume will be given a random name which can be found using docker inspect command.  
`WORKDIR` - Defines the working directory for subsequent instructions in the Dockerfile(Important point to remember that it doesn't create a new intermediate layer in Image)  
 
### DOCKER COMMAND CHEATSHEET

### DOCKER LOGIN 
```
docker login --help -  (Use this to see the options for logging in)
docker login -u your_user_name  (The -u option allows us to pass our user name.)
docker logout (logout
```

### DOCKER IMAGES 
```
docker info
docker images
docker images <image-name>
docker images --no-trunc  (list full image id) 
docker pull <image-name>:<tag-name> (pull image)
docker search shiny  
docker pull <imager_name> (rocker/shiny-verse)
docker push <username>/<image_name>
docker build -t <image_name>   (Build an Image from a Dockerfile)
docker build -t <image_name> --no-cache  (Build an Image from a Dockerfile without the cache)
docker images (List local images)
docker rmi <image_name>  (Delete an Image)
docker image ls -f dangling=true
docker image tag <image_name> <tag-path> (tagging image)
docker container commit <container-name>  (create image from running container)
curl example.com/remote/Dockerfile | docker build -f – .  (Building from a Remote Dockerfile URI). —-> Done
docker image save <image_name>  >  imagesavetemp.tar     (Saving an Image to a Tar Archive)
docker image load <  imagesavetemp.tar  (Loading a Tarred Repository from a File or the Standard Input Stream)
docker image history <image_name>  (Showing the History of an Image)
docker image prune (Remove all unused images)
docker system prune -af (Remove all unused images (dangling and unreferenced), containers, networks, and volumes.)
docker save -o <file-name.tar> <image-name>
docker load -i <file-name.tar>
```

### DOCKER CONTAINER
```
docker run --name <container_name> <image_name>. (Create and run a container from an image, with a custom name:)
docker run -p <host_port>:<container_port> <image_name>. (Run a container with and publish a container's port(s) to the host.)
docker run -d <image_name>.  (Run a container in the background)
docker start/stop <container_name> (or <container-id>). (stop an existing container:)
docker rm <container_name> (Remove a stopped container:)
docker exec -it <container_name> sh  (Open a shell inside a running container:)
docker logs -f <container_name>  (Fetch and follow the logs of a container:)
docker inspect <container_name> (or <container_id>) (To inspect a running container:)
docker ps   (To list currently running containers:)
docker stats <container_id> (check cpu memory uses)
docker system df  (Display information about disk space being used by your containers)
docker ps --all  (List all docker containers (running and stopped):)
docker container stats (View resource usage stats)
docker kill <container> – Send a SIGKILL signal to the foreground process running in a container, to force it to stop.
docker rename <container> my-container – Rename a specified container to my-container.
docker pause <container> and docker unpause <container> – Pause and unpause the processes running within a specific container.
docker stop <container> – Stop a running container.
docker start <container> – Start a previously stopped container.
docker rm <container> – Delete a container by its ID or name. Use the -f (force) flag to delete a container that’s currently running.
docker container top <image_name>  (check container’s running process)
```

### COPY FILES
```
docker cp example.txt my-container:/data  # Copy example.txt from your host to /data inside the my-container container.
docker cp my-container:/data/example.txt /demo/example.txt   # Copy /data/example.txt out of the my-container container, to /demo/example.txt on your host.
docker diff [container] (show changes to files or directories on the filesystem)
```

### CLEANUP
docker system prune # Removes unused data, including dangling image layers (images with no tags).
docker system prune -a # Extends the prune process by deleting all unused images, instead of only dangling ones.
docker system prune --volumes # Includes volume data in the prune process. This will delete any volumes that aren’t used by a container.
docker image prune -a # Removes all unused images.
docker network prune # Removes unused networks.
docker volume prune #Removes unused volumes.
docker system df # Reports your Docker installation’s total disk usage.
Docker run with Options

### DOCKER RUN with Multiple Arguments
```
docker run -it -d  -m 512m --cpus=2 -h container_name —-rm v mount_dir:/tmp -p 8088:80 -e MYSQL_PASSWORD=abc123 -e MYSQL_DATABASE=mydb --name mycontainer ubuntu
```

-it  :- The interactive mode in Docker allows us to execute commands while the container is in a running state
-d :- Run container in background and print container ID
-m :- Memory Assignment
—cpus :- Numbers of cores
—name :- Name of container
—hostname :- hostname in container

### Docker Volumes:
```
docker volume create volume_name
docker volume inspect volume_name
docker volume ls.      (List volume)
docker volume ls -f name=data.     (Filter with name)
docker volume inspect <volume_id>
docker volume rm hello (remove volume)
docker volume prune  (Delete all unused volume)
docker system df -v (tracking and controlling volume consumption)
```


### REUSE VOLUME
# Create the first container
`$ docker run -d --name db -v app_data:/data database-image:latest`
# Create the second container
`docker run -d --name backup --volumes-from db backup-image:latest`

### Bind mounts
```
docker run -d \
  -it \
  --name devtest1 \
  --mount type=bind,source="$(pwd)"/target,target=/app \
  nginx:latest
```
```
docker run -d \
  -it \
  --name devtest2 \
  --mount type=bind,source="$(pwd)"/target,target=/app,readonly \
  nginx:latest
```

### Container Communication Example:
```
docker network create mongo-network
docker network ls
```
# 1- Mongo DB
```
docker run -d --network mongo-network --name mongo \
   -p 27017:27017 \
	-e MONGO_INITDB_ROOT_USERNAME=“admin”\
	-e MONGO_INITDB_ROOT_PASSWORD=“admin”\
	mongo
```
# 2- Mongo Express
```
docker run -d -it --rm \
    --network mongo-network \
    --name mongo-express \
    -p 8081:8081 \
    -e ME_CONFIG_OPTIONS_EDITORTHEME="ambiance" \
    -e ME_CONFIG_MONGODB_SERVER=“mongo” \
    -e ME_CONFIG_MONGODB_ADMINUSERNAME=“admin” \
    -e ME_CONFIG_MONGODB_ADMINPASSWORD=“admin” \
    mongo-express
```
# Mongo Command
```
>  mongosh -u admin -p
> show dbs
> use blog

> db.createCollection("posts")

> db.dockerlearn.insertOne({
  title: "Post Title 1",
  body: "Body of post.",
  category: "News",
  likes: 1,
  tags: ["news", "events"],
  date: Date()
})
```



