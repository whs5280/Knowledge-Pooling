# docker

### docker-hub
`https://www.docker.com/products/docker-hub/`

### ��������
`docker exec -it <container_id> /bin/bash`

### �鿴��־
`docker logs -f --tail 100 <container_id>`

### �鿴��������
`docker inspect <container_id>`

### �鿴����
`docker network ls`

### �鿴��������
`docker top <container_id>`

### ����ɾ��
`docker images | grep 'graphql' | grep 'none' | awk '{print $3}' | xargs -r docker rmi`

### ɾ����������
`docker rmi <image_id>` `docker rm <container_id>`

### docker images λ��
`docker info | grep -i 'docker root dir'`
