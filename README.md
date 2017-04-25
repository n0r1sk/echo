# n0r1sk echo

"echo.py" is a python3 based script which starts a threaded tcp/udp-server to display informations about itself (hostname, interfaces) and about the connecting client (IP, HTTP headers,...).

We built this docker container for testing of docker swarm, overlay networks and so on.

## Running the docker container
### single docker container
```
docker run -d -P n0r1skcom/echo
```

### docker swarm service
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
