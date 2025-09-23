# docker

### docker-hub
`https://www.docker.com/products/docker-hub/`

### 进入容器
`docker exec -it <container_id> /bin/bash`

### 查看日志
`docker logs -f --tail 100 <container_id>`

### 查看容器详情
`docker inspect <container_id>`

### 查看网桥
`docker network ls`

### 查看容器进程
`docker top <container_id>`

### 搜索删除
`docker images | grep 'graphql' | grep 'none' | awk '{print $3}' | xargs -r docker rmi`

### 删除镜像、容器
`docker rmi <image_id>` `docker rm <container_id>`

### docker images 位置
`docker info | grep -i 'docker root dir'`
