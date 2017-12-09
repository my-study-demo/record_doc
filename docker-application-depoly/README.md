docker日常维护
===

移除所有的容器和镜像（大扫除）
---
```bash
docker kill $(docker ps -q)
docker rm $(docker ps -a -q)
dockerrmi $(docker images -q -a)
```