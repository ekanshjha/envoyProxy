# EnvoyProxy

## Demo Overview

All traffic is routed by the `front envoy` to the `service containers`. Internally the traffic is routed to the service envoys, then the service envoys route the request to the flask app via the loopback address. In this demo, all traffic is routed to the service envoys like this:

- A request (path `/service/blue` & port `8000`) is routed to service_blue
- A request (path `/service/green` & port `8000`) is routed to service_green
- A request (path `/service/red` & port `8000`) is routed to service_red

![alt text](https://github.com/ekanshjha/envoyProxy/blob/master/assets/demo-httproute-simple-match.png)

## Deploying EnvoyProxy as a simple http routing

Pre-requisite:

- Set up a [docker](https://docs.docker.com/get-started/).
- Install [docker-compose](https://docs.docker.com/compose/install/).

### Step 1: Install Docker

Ensure that you have a recent versions of docker and docker-compose installed.

A simple way to achieve this is via the Docker Toolbox.

### Step 2: Clone the repo, and start all of the containers.

```
$ git clone https://github.com/ekansh/envoyProxy.git
$ cd envoyProxy/httproute-simple-match
```

### Step 3: Build and Run containers

```
$ docker-compose up --build -d
```

### check all services are up
```
$ docker-compose ps --service

front-envoy
service_blue
service_green
service_red

### List containers

$ docker-compose ps

                 Name                               Command               State                            Ports
-----------------------------------------------------------------------------------------------------------------------------------------
httproute-simple-match_front-envoy_1     /usr/bin/dumb-init -- /bin ...   Up      10000/tcp, 0.0.0.0:8000->80/tcp, 0.0.0.0:8001->8001/tcp
httproute-simple-match_service_blue_1    /bin/sh -c /usr/local/bin/ ...   Up      10000/tcp, 80/tcp
httproute-simple-match_service_green_1   /bin/sh -c /usr/local/bin/ ...   Up      10000/tcp, 80/tcp
httproute-simple-match_service_red_1     /bin/sh -c /usr/local/bin/ ...   Up      10000/tcp, 80/tcp
```

The above setup *might* not run on windows.

## In case containers are not up:

```
$ docker-compose ps
                 Name                               Command                State                              Ports
--------------------------------------------------------------------------------------------------------------------------------------------
httproute-simple-match_front-envoy_1     /docker-entrypoint.sh /bin ...   Up         10000/tcp, 0.0.0.0:8000->80/tcp, 0.0.0.0:8001->8001/tcp
httproute-simple-match_service_blue_1    /bin/sh -c /usr/local/bin/ ...   Exit 127
httproute-simple-match_service_green_1   /bin/sh -c /usr/local/bin/ ...   Exit 127
httproute-simple-match_service_red_1     /bin/sh -c /usr/local/bin/ ...   Exit 127
``` 

If it shows the same as above:

Then, if you check the `logs` of the exit container like next, you will find the error:

```
C:\test\envoy-proxy-demos\httproute-simple-match>docker logs httproute-simple-match_service_red_1
/bin/sh: /usr/local/bin/start_service.sh: not found
```

> When you see this, most probably it's because the file format on windows not compatible with linux. 
For your scenario, to make all things work, you need do next:

> After `git clone` the source code, modify `envoy-proxy-demos/apps/service.py`, add `#!/usr/bin/python3` to 
the start of this file(In this file it's already added).

With powershell's help, enter into `envoyProxy/apps`, change following files format from `dos to unix` as next:

```
$ dos2unix start_service.sh service.py
dos2unix: converting file start_service.sh to Unix format...
dos2unix: converting file service.py to Unix format...
```

Finally, build & start:
```
docker-compose up --build -d
```

### Access each services

Access serivce_blue and check if blue background page is displayed
```
$ open http://localhost:8000/service/blue
# or
$ curl -s -v http://localhost:8000/service/blue
```

Access serivce_green and check if green background page is displayed
```
$ open http://localhost:8000/service/green
# or
$ curl -s -v http://localhost:8000/service/green
```

Access serivce_red and check if red background page is displayed
```
$ open http://localhost:8000/service/green
# or
$ curl -s -v http://localhost:8000/service/red
```
