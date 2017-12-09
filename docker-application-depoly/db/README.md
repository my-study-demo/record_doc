数据库（mysql）
===

拉取镜像
---
```bash
docker pull mysql:5.7
```

构建容器
---
```bash
docker run --name mysql -p 3306:3306 --restart=always -v /etc/localtime:/etc/localtime:ro -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7 --lower_case_table_names=1
```
--lower_case_table_names=1:忽略大小写

启动容器
---
```bash
docker mysql start
docker mysql stop
docker rm mysql
```