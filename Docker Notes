1. Downloading Image from Docker Hub.
    docker image pull <image-name>
eg: docker image pull hello-world

2. Listing images
docker images

Image is the final artifact of the application. It is similar to WAR/EAR files.
There will be docker-file which will define the details that application needs like server,databases,load balancer,environment variables etc.
we can run the image and a container will be created for the image.
container is just an instance of an image.
we can run multiple containers for the same image.

3. Listing containers
    docker container ls

4. Running container
    docker container run <image-name>
    eg: docker container run hello-world

    Generally our web application will be running in a port that is defined in the docker-file, that port is not visible
    outside the container i.e., browsers. so to publish that port outside the containers we need use -p argument while
    running the container (-p means publish)
    This is a nice feature where we can hide services running internal to docker as these will not be visible unti
    port mapping is done.

    docker container run -p <external-port:internal-port> run <image-name>
    eg: docker container run -p 8081:8080 run webapp1
    Now in browser we can use 8081 and that will be routed to docker container internal port 8080.

5. Stopping Container
    docker container stop <image-id>

Where these Images are coming from?
Docker by default checks https://hub.docker.com (Docker Hub) with the image name, and then pulls the image.

Naming Convention for Images in Docker Hub
OwnerName/ImageName
eg: virtualpairprogrammers/fleetman-webapp

There are some images with Offical tag, these are images created by Docker team for most popular softwares.
These images are fully tested by Docker team, and these images will not have owner name
eg: docker image pull ubuntu

Classic Vs Modern Docker commands:
==================================
Operation : Pull image
Classic : docker pull <image-id>
Modern : docker image pull <image-id>

Operation : List containers
Classic : docker ps
Modern : docker container ls

Operation : List All Active and Inactive containers
Classic : docker ps -a
Modern : docker container ls -a

Classic commands are some what confusing as it not having Management command like image,container,service etc.
so it will confusing whether that specific docker command is running for containers or images, so docker team
restructured all the commands to include this piece of information.
Always use Modern commands.

**We cannot modify Docker Images
Instead we can extend these images to build a new image, means we can put our application to create another image which
is again immutable.

When we run ubuntu image a new container will be launched and exited immediately because running ubuntu image will run
/bin/bash i.e., bash shell. since there is no script to run it will exit immediately.
To run bash shell interactively we need to command line arguments.
docker container run -it ubuntu
Here it is two flags -i -t
i - interactive
t - connect it to our terminal

How to stop a container
docker container stop <container-id>
If we don't remove the containers which are stopped state then it will take up some space in our machine, but it will not harm anything.

How to remove a container
docker container rm <container-id>

How to remove all stopped containers
docker container prune

How to start a stopped container
docker container start <container-id>
This command will start the container in background and logs will not be shown to the console.
so we have to wait for some time to get the container up and running, and we don't the application status as well.

How to run an image as a demon.
Generally spring boot applications will run as a foreground applications, so to run these applications as a background
process we need to pass a control flag -d
d - means detach logs from console.

How to check logs of a container.
docker container logs <container-id>

How to check logs of a running container.
docker container logs -f <container-id>

How to do ssh into a container
docker container exec -it <container-id> bash


Container Lifecycle
===================
Every time we start an ubuntu container and made some changes (like installing git,maven) and once this container
is stopped,
When we run a container from a fresh ubuntu image, made some changes(like installing git,maven) those changes
will be there in that container ONLY.
If we run another container from a fresh ubuntu image then changes like git,maven will not be available in the
present container.
For this we need to take snapshot of the container where our changes are present so that this snapshot will
be created as a image and we can run container from this image(which will contain our updates like git,maven).

eg1: docker container run -it ubuntu
container id : container1
Install maven in container1

Run again a container from fresh ubuntu image
docker container run -it ubuntu
container id : container2
Now this container2 will NOT haven maven installed.

so for this we will create snapshot of the container i.e. creating an image with updated changes.
docker container commit -a "Leela Prasad leela.jagu@gmail.com" container1 ubuntu-with-maven

Now if we see docker images
this image(ubuntu-with-maven) will be present.

Run a new container from this image.
docker container run -it ubuntu-with-maven.
container id : container3
Now this container3 will have maven :).

Generally we will NOT build/create images from containers snapshots, instead we will build images from dockerfile.
because we can modify docker files as required and built same images with updated content. The steps in this
docker file are generally cached so when we run multiple times the steps are taken from cache.
When we build images from containers snapshots and we have to update the image then again the image is built from
the scratch and run a container on the new image.
So images are not repetable from container snapshots, whereas images are repetable from docker files as the steps
are cached at each step. This step caching is done by using container snapshot method as discussed above.

Docker File:
============
FROM ubuntu:latest
MAINTAINER Leela "leela.jagu@gmail.com"
RUN apt-get update && apt-get install -y openjdk-8-jdk
CMD ["/bin/bash"]

FROM tells us the base image.
MAINTAINER is used for documentation, who builts this image.
RUN - will run the commands on the base image.
CMD - will be triggered when we run container from the image built from dockerfile.
means if user runs docker container run -it <image-name> then CMD command will be triggered.

How to build image from Docker file
===================================
docker image build -t <tag-name> <path-where-image-related-files-exist>

for building an image we might need some files so "path-where-image-related-files-exist" will indicate files are
present in the mentioned path or it subdirectories.

eg: docker image build -t jdk-image-from-docker-file .

***Image is built in different steps by firing containers at each step.
each step container snapshot is cached.
next time when that image is built then it will take the step result from the cache instead of downloading.

Docker File :
FROM ubuntu:latest
MAINTAINER Leela "leela.jagu@gmail.com"
RUN apt-get update && apt-get install -y openjdk-8-jdk
WORKDIR /usr/local/bin
COPY test-program.jar .
CMD ["java", "-jar", "test-program.jar"]

WORKDIR will specify the directory so that next commands will be relative to that workdir.
WORKDIR - working directory.

COPY <local-path> <container-path>
COPY test-program.jar .

test-program.jar path is searched from the metioned path during image build step.
docker image build -t jdk-image-from-docker-file .        -> "."

here container-path is "." , so it is WORKDIR.
because all commands below are relative to WORKDIR.

CMD ["java -jar test-program.jar"]
Here the jar file location is also relative to the WORKDIR i.e., WORKDIR/test-program.jar

Difference between CMD and ENTRYPOINT?
CMD and ENTRYPOINT will do the same thing i.e., it will run the command specified when container is ran.
but CMD will allow us to override the command during docker container run, whereas ENTRYPOINT will allow this override.

i.e., docker container run -it jdk-image-from-docker-file /bin/bash
if we have CMD in the docker file it will run /bin/bash
if we have ENTRYPOINT in the docker file it will run java -jar test-program.jar.


Docker file for running war file in tomcat
==========================================
FROM tomcat:latest

MAINTAINER Leela "leela.jagu@gmail.com"

ENV JAVA_OPTS="-Dspring.profiles.active=development"

WORKDIR /usr/local/tomcat/

RUN rm -rf webapps/*

COPY fleetman-0.0.1-SNAPSHOT.war webapps/ROOT.war

EXPOSE 8080

CMD ["catalina.sh","run"]

ENV -> is used to define Environment variables for that container.
RUN -> is used to run commands inside the container.
EXPOSE -> indicate which port we are exposing in the container to run the service. It is recommended to mention
this line so that anyone can understood which port this service is running from docker file.
even thought we are exposing still we need pass parameter to publish port -p 80:8080
so EXPOSE is only for Maintainence perspective.


Docker file for Running Spring Boot Application
===============================================
FROM jdk:latest

MAINTAINER Leela "leela.jagu@gmail.com"

WORKDIR /usr/local/bin/

COPY fleetman-0.0.1-SNAPSHOT.jar webapp.jar

EXPOSE 8080

CMD ["java", "-Dspring.profiles.active=development", "-jar", "webapp.jar"]

How to create alias images?
docker image tag <image-id> <alias-name>
This will be useful we want to tag our final image with docker userid prefix.

docker image tag 3587g7 leela239/ubuntu-image.

**Here both images have same image id.It is just an alias.
If you want separate images then run docker image build command to create a new image.

How to push image to docker?
First login to docker hub
  docker login
Enter credentials, After successful Login.
execute below command.
  docker image push <image-id/image-name>
  Here the image name should be prefixed with docker login id with "/".


Networks and Services
=====================
By Design Docker containers are supposed to run only one service. i.e., it is not recommended to run spring boot
application and mysql in one container, we need to run these 2 services in 2 containers and we need to
communicate between containers.
It is not like "NOT possible to run multiple services in one container", but it is a best practice to run
one service in one container so that we will know the health of the container if one service goes down.
this health check of container will give a false alerts if we run multiple services in one container.
i.e., mysql + spring boot if mysql service goes down still the container health is green. to avoid these
confusions generally we will run one service in one container and we will communicate between containers.

If we have multiple CMD commands in docker file then the last CMD command will be taken when user runs docker
container from the image. so don't write multiple CMD in docker files.


We can pass environment variables while running the containers.
eg: docker container run -e MYSQL_ROOT_PASSWORD=password -d mysql:8

How Docker container network with each other?
Docker takes the name of the container and add this name to the DNS Service so that other docker containers
know about this new container through DNS without using IP address.
i.e., we can connect to remote containers from local container by simply using the container name and this name is
passed to the DNS Service which will give the IP address of remote container where it is running.
By using this technique if we start a brand new container, this container will be visible to all other containers
through the container name, as other containers will pass new container name to DNS Service and get the IP address.

Docker Network commands
=======================
How to list available networks in a particular Docker installation?
docker network ls

Bridge Network
==============
Bridge Network is a default network in docker. This Bridge Network allows to connect to outbound traffic
like google.com,facebook.com
All our containers will be connected to this Bridge Network, but with this Bridge Network doesn't allow
container to container networking. If we want to allow container to container networking we need add a new
network to the list.

How to add a network
docker network create <network-name>
eg: docker network create my-network

 Now run database and spring boot app attached to the same network.

 docker container run --network my-network -d -p 80:8080 --name fleetman-webapp fleetman-webapp

 docker container run --network my-network --name database -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=fleetman -d mysql:8

 now we can ssh into spring boot app and execute below command
 ping database

 Docker Volumes
 ==============
 Generally when we run a container from a image, do some operations which need to be persisted even though container
 is stopped is not the default behaviour of docker containers.
 This is because we doesn't mention any volume where the data needs to be stored.

 In mysql database image they defined volume in the docker file.
 volume /usr/lib/mysql
 so when a container is started modifications will be stored in the new volume that container creates during startup.

 To List volumes in Docker
 docker volume ls

 How to know the Mount point of the volume that is created at the container startup, execute below command.
 docker container inspect <container-id>
 Go to the mounts section there we will find the real path where our data will be saved.

 How to mention custom volume names
 docker container -v fleetmanDB:/usr/lib/mysql -d -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=fleetman mysql:8

 This will create or use the fleetmanDB volume to store any persistent data when container is running.


How to manually mount the volume to some location in our system.
docker container -v ~/Leela/Docker:/usr/lib/mysql -d -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=fleetman mysql:8
Note this manual mounting will not create a new volume and we need to refer to the path instead of volume name
when starting the docker container.


Fabric8 Plugin
==============
With Fabric8 Plugin we can create images from maven and push it to docker hub from maven.
Below is the sample code of this plugin.

<plugin>
  <groupId>io.fabric8</groupId>
  <artifactId>docker-maven-plugin</artifactId>
  <version>0.21.0</version>

  <configuration>
    <!-- <dockerHost>unix:///var/run/docker.sock</dockerHost> -->
    <verbose>true</verbose>

    <images>
      <image>
        <name>leela239/fleetman-webapp</name>
        <build>
          <dockerFileDir>${project.basedir}/src/main/docker</dockerFileDir>

          <assembly>
            <descriptorRef>artifact</descriptorRef>
          </assembly>
          <tags>
            <tag>latest</tag>
          </tags>
        </build>
      </image>
    </images>
  </configuration>

  <executions>
    <execution>
      <phase>package</phase>
      <goals>
        <goal>build</goal>
      </goals>
    </execution>

    <execution>
      <id>mydeploy</id>
      <phase>deploy</phase>
      <goals>
        <goal>push</goal>
      </goals>
    </execution>
  </executions>
</plugin>

Docker Compose:
===============
why we need docker-compose

let us consider we need to start our fleetman webapp and database service then below are commands that we need
to execute.

docker container run -d --network fleetman-network --name database mysql:8
docker container run -d --network fleetman-network --name fleetman-webapp -p 80:8080 leela239/fleetman-webapp

Running containers with long listing command line flags is painful, so we will define all these command line
flags in docker-compose.yaml file so that we need to run only below command
docker-compose up (or)
docker-compose up -d    this is to run in detached mode.
This command will start all the containers which are defined in the docker-compose.yaml file.

Sample docker-compose.yaml file

version: "3"

services:

  fleetman-webapp:
    image: leela239/fleetman-webapp
    networks:
      - fleetman-network
    ports:
      - 80:8080
    depends_on:
      - database

  database:
    image: mysql:8
    networks:
      - fleetman-network
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=fleetman

networks:
  fleetman-network:


**In YAML "-" indicates an array.

In the above example we need to run database service first and then fleetman-webapp we will define this startup
order by using "depends_on".
But this depends_on will not wait until database service is completely started.
it will run the containers in the order defined by depends_on, so this leads to a problem that in most real world
services may take good amount of time and if one service(in this case fleetman-webapp) is dependent on the service
which is not completely started then this service will also fail.
So as a workaround what we will do is we will run "docker-compose up" again and this time it will only start the
containers which are in stopped (or) it will start a new container if a image is updated.
so this will solve our service dependency issue.
But because of this rerun of docker-compose 2 times docker-compose is generally not recommended approach in
production applications.

We can create a new image for the service which is running and we can run "docker-compose up" so that it will start
new container with this new image or I can say it will start containers whose images are updated.

docker-compose down
will stop all the services defined in the docker-compose.yaml file and remove containers and networks as well.

docker-compose logs -f <service-name>
will show you log file for the service mentioned.

Tools for different commands:
=============================

For creating image
instead of using below command
docker image build -t <image-name>:<version> .
we can use fabric8 maven plugin to create images automatically.

For Running containers
instead of using below command
docker container run -d --network fleetman-network --name fleetman-webapp -p 80:8080 leela239/fleetman-webapp
we can put all these command line flags in docker-compose.yaml file and run this file.

For Running sequence of steps for building step by step image
we can use docker-file so that all the steps will be ran and image is built.
this will avoid to take the container snapshot and building new image on top of that.

Docker Swarm
============
Docker Swarm is an Orchestration tool for containers.
Docker Swarm will run containers on different nodes that exist in the swarm i.e., swarm will load balance containers
on different nodes and will spin up another container service if one container goes down.
Since containers are running on different nodes, bridge network will not work to communicate between containers.
So we will create "overlay" network to communicate between containers.

How to initiate a Swarm
docker swarm init

This will create swarm and another nodes can join this swarm using below command
docker swarm join --token tokenId ManagerIp:port

In swarm we have 2 types of nodes.
1. Manager nodes
2. Worker nodes
Manager nodes are eligible for running swarm commands
Both Manager and worker nodes can run containers, but Manager nodes need more processing power, so it not
recommended to make all nodes as Manger nodes.
Recommended Manager nodes should be odd number like 1,3,5 but in production we will have minimum of 3 Manager nodes.

Creating overlay network
docker network create --driver overlay fleetman-network

In Swarm we will use servie instead of container.
so for running a container/creating a service in swarm we will use below command.
docker service create --network fleetman-network --name database -d -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=fleetman mysql:5

Listing services in Swarm
docker service ls

Listing nodes in swarm
docker node ls

docker service create -d --network fleetman-network --name fleetman-webapp -p 80:8080 virtualpairprogrammers/fleetman-production

How to check logs for a service
docker service logs -f <service-name>
docker service logs -f database

Stacks
======
stacks is similar to docker-compose.yaml file.

How to create a stack/ How to run docker-compose.yaml file in swarm
docker stack deploy -c <docker-compose.yaml> stack-name

docker stack deploy -c docker-compose.yaml fleetman-stack

In docker-compose.yaml file we no need to mention network as overlay docker will automatically create
overlay network when it is in swarm mode
bridge network when it is in non-swarm mode

Here in swarm mode depends_on will be ignored and it will not work, but what docker swarm will do is it
run services on different nodes and if any container is failed then it will spin up another service.
In this way we no need run docker-compose up command twice that we did in non-swarm mode.
docker swarm will take care of running failed containers.

How to know which service is running on which node?
docker service ps <service-name>

docker service ps fleetman-stack_fleetman-webapp

How to stop a container manually
docker container stop <container-name>
docker container kill <container-name>

Restart Policy:
This flag will specify when swarm needs to restart container.
this flag has 3 options none,on-failure, any
any is default
swarm will restart container only when container exit with error code for on-failure flag
swarm will restart container when container exit with success/error code for any flag
container is running a command so it can exit with success/error code.
In our case we are running spring boot or database application application which is a deamon and the command will never end

Upate Config:
This will be used to have rolling updates.
This will have an option to delay rolling updates from one container to another container.

replicas:
This will be used to mention how many instances are needed for a service.

Deploy node is only applicable for swarmm mode.
It will ignored for non-swarm mode.


Routing Mesh
============
If a http request came to a container which is not running any web application, then what swarm will do
is it routes this request to node which expose that port in the background, this is called Routing Mesh.
so we can issue a web request to any node in docker swarm irrespective whether web app is running in that node
or not, Routing Mesh will route to the appropriate node in the background using Routing Mesh.

There can be many same internal docker ports. eg: same internal port for mysql 3306
but external ports which we are publishing should be unique.

Docker Swarm Visualizer
======================
Docker Swarm is a GUI Utility which will be used to check which containers are running on which nodes
and container state as well.

docker container run -d -p 5000:8080 -v /var/run/docker.sock:/var/run/docker.sock dockersamples/visualizer

Docker swarm will use this docker.sock, so this container also need to access this docker.sock to show where
containers are distributed across nodes.

Rolling Update:
===============
While doing rolling update docker container will be changed with these updates with some delay.
during container restart if request routed to this container then it will give you error because docker treats
a running container as healthy(even it is still in startup), for this purpose we still need to depend upon
Load balancer where it will run some steps to check whether the port is reachable or not.
