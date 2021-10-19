# Useful Docker command for development

## to stop all running container

```shell
 docker stop $(docker ps -a -q)
```

## To delete all container

```shell
docker rm $(docker ps -a -q)
```

