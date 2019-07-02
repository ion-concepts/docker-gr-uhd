# Recepies to build useful Docker Containers.


### To Run an Intereactive Shell in a Docker Container.
docker run -ti  -v /dev/bus/usb:/dev/bus/usb -v /home/ianb/:/home/ianb --net host --privileged ionconcepts/armhf-gr-uaslink:HEAD bash -l

### Forwarding X from inside a docker container 
docker run -ti  -v /dev/bus/usb:/dev/bus/usb -v /home/ianb:/home/ianb  -e DISPLAY=$DISPLAY  -v /tmp/.X11-unix:/tmp/.X11-unix --net host --privileged ionconcepts/armhf-gr-oot:HEAD


###  running containers
sudo docker ps

### Connect to existing shell on running container
sudo docker attach 51cf26da1884

### Create new shell on running container
sudo docker exec -i -t 51cf26da1884 /bin/bash

### Building
docker build .

docker tag f0dd7a45614d ionconcepts/armhf-uhd:3.10.2

docker push

docker push  ionconcepts/armhf-uhd:3.10.2

Once you have a Dockerfile, you can build the image using docker build:

$ docker build .

You’ll see it grind for a while as it downloads prerequisite layers and builds the service. When it’s finished you’ll see a line like this:

Successfully built f0dd7a45614d

This UUID is a unique reference to the filesystem layer containing the service. To tag the image for deployment, do the following:

$ docker tag f0dd7a45614d ionconcepts/armhf-uhd:3.10.2

$  docker push  ionconcepts/armhf-uhd:3.10.2
This will create a universal tag called 3.10.2 for the ionconcepts/armhf-uhd Docker image in Docker Hub. Now, on any host which possesses login credentials for IonConcepts’s account on Docker Hub, you can type:

$ docker pull ionconcepts/armhf-uhd:3.10.2

...to download the image. To enter the environment contained in the image, you can invoke a Bash shell inside the environment as such:

$ docker run -ti ionconcepts/armhf-uhd:3.10.2 bash -l

$ docker exec -ti ionconcepts/armhf-uhd:3.10.2  bash -l

Now you’ll have a shell inside the running container described by the name hggateway. If you don’t name your container when you created it with “docker run” (the invocations in the /etc/init.d/ Upstart scripts name the containers as seen above), Docker will assign a unique name to your container using mnemonics. You can also refer to the container using the container ID (e.g. 695875e84337 above).

Exiting from a secondary invocation (docker exec) does not terminate the parent container. Exiting from the primary invocation (docker run) will terminate the parent container. Remember that changes are lost after termination!

### Other helpful Docker commands/options:
You can use -v [host_path:container_path] to share a path on the host with the container. For instance, -v /tmp:/tmp shares the /tmp folder on the host with the container, so you can move files there for persistent storage.

You can use --net host to provide access to the host networking adapters, rather than interacting through a virtual network interface. This will be necessary if you want to run a Groundpaq service, as they all bind to ports on the host adapters.

docker attach <container id> will attach a tty to the running service. This lets you interact with running services that are interactive. Ctrl-c is intercepted and will not send a SIGINT to the service, so you can’t exit the service this way -- it will only exit the docker attach command.
  
docker logs <container id> will provide the full stdout/stderr output of the container’s primary process.
  
docker kill <container id> will gracefully kill (SIGTERM, then SIGKILL after 10s) the running process and exit the container.

For more gory details, see the Docker invocations in the four services’ Upstart scripts, in for instance /etc/init.d/docker-rtnfpga.
The -ti tells Docker to 1) allocate a terminal, and 2) provide an interactive session. Now you will end up in a root shell inside the Docker environment. Remember that changes you make inside the Docker container are not preserved after you leave the shell!

To change the Docker tag being used in the Upstart invocations, edit the appropriate service startup file, e.g. /etc/init.d/docker-hggateway, and change the names of the Docker tags (for instance, “higherground2/gateway:addrtype” to the appropriate Docker tag. There are two instances of the Docker tag name in each Upstart script, one for pulling the appropriate image, and one for starting it.

