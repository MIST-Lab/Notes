# docker

 copy a local file to the docker container

```bash
# look for all containers

docker ps -a

# use docker container name to get container LONG-ID 

docker inspect -f '{{.ID}}' CONTAINERNAME

docker cp LOCALPATH LONG-ID:DOCKERPATH
```

