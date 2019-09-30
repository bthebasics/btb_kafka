#Install Landoop Kafka Docker 

Download the latest from below :

https://hub.docker.com/r/landoop/fast-data-dev

```shell
docker-machine create --driver virtualbox --virtualbox-memory 4096 lensesio

eval $(docker-machine env lensesio)
docker-machine env lensesio
#use port mentioned in above commnad for below - 

docker run --rm -p 2181:2181 -p 3030:3030 -p 8081-8083:8081-8083 -p 9581-9585:9581-9585 -p 9092:9092 -e  ADV_HOST=192.168.99.101 lensesio/fast-data-dev:latest

```



**how to login to docker container :** 

```shell
$ docker ps 
# take the container name

$ docker exec -it <container-name> /bin/bash
```

