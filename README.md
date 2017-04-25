# n0r1sk echo

"echo.py" is a python3 based script which starts a threaded TCP/UDP-server to display informations about itself (hostname, interfaces) and about the connecting client (IP, HTTP headers,...).

We built this Docker container for testing of docker swarm, overlay networks and so on.

# Why
In the Docker environment it is often useful to have a simple container which provides a UDP, TCP and HTTP interface that you can pull from a client and that provides some helpful information like the client ip the request originates from. Furthermore if you are dealing with Docker Swarm or internal/external loadbalancers for your service it is often helpful to test your idea with a simple Docker container to check if the correct ip address is reaching your backend service.

# What
This Docker image is based on Ubuntu:16.04 because we need Python3 to power our echo.py script. We have included some additional but useful tools like netcat, telnet, curl, nslookup and netstat. These are utilities which are helpful if you need to get more information on why something is not working as expected.

## Running the Docker container
### Single Docker container
Starting a new container based on the n0r1skcom/echo image on a dynamic port
```
docker run -d -P n0r1skcom/echo
```
If you want to specify the exposed port you have to use the following command
```
docker run -d -p 22222:3333 -p 22222:3333/udp n0r1sk/echo
```

### Docker Swarm service
At first you have to create a new overlay network if you have no existing overlay network you want to use.
If you have one, just skip this step.
```
docker network create --driver overlay overlay1
```
Create a new docker swarm service where 22222 is the port you want to expose the echo service to your network
```
docker service create --name echo --network overlay1 --replicas 2 -p 22222:3333 -p 22222:3333/udp n0r1skcom/echo
```
To see if your service is running
```
docker service ps echo
```
If you want to deploy a newer image of your running docker swarm service
```
docker service update echo --image n0r1skcom/echo:latest
```

### Build the docker container on your own
You can simply build the docker container on your own. You only have to clone this github repository on a docker host and build the docker container via the included "Dockerfile"
```
git clone https://github.com/n0r1sk/echo.git
cd echo
docker build .
```

## Start to use the echo tcp/udp-server
### TCP
You can simply connect to the server via telnet
```
telnet IP_OF_DOCKER_HOST PORT_OF_DOCKER_CONTAINER
```
Or, if you want to see some http headers, just use curl
```
curl --header "X-Forwarded-For: 192.168.0.2" http://IP_OF_DOCKER_HOST:PORT_OF_DOCKER_CONTAINER
```
### UDP
For testing udp connections
```
netcat -u IP_OF_DOCKER_HOST PORT_OF_DOCKER_CONTAINER
```
Or just use echo (WARNING -> no further output on client -> one way communication)
```
echo -n "test" > /dev/udp/IP_OF_DOCKER_HOST/PORT_OF_DOCKER_CONTAINER
```
## FAQ
No questions so far...  :)

If you have any questions please don't hesitate to contact us via github-comment or by mail.
