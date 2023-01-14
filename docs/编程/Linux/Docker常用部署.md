---
sidebar_position: 3
---


### mysql

```sh
docker run \
    --name mysql \
    -p 3306:3306 \
    -v /root/mysql/data:/var/lib/mysql \
    -v /root/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf \
    -e MYSQL_ROOT_PASSWORD=1234 \
    -d \
    mysql:8.0.31
```

- hmy.cnf文件内容为：

  ```
  [mysqld]
  skip-name-resolve
  character_set_server=utf8
  datadir=/var/lib/mysql
  server-id=1000
  ```

### mongodb

```sh
docker run \
    --name mongodb \
    -p 27017:27017 \
    -v /root/mongodb/data:/data/db \
    -v /root/mongodb/conf:/etc/mongo \
    -e MONGO_INITDB_ROOT_USERNAME=admin \
    -e MONGO_INITDB_ROOT_PASSWORD=1234 \
    -d \
    mongo:4.0
```

