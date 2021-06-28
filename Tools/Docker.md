## Docker

#### 简介





#### Dockerfile

- ADD

  - 用于复制文件，格式：

  ```shell
  ADD <src> <dest>Copy to clipboardErrorCopied
  ```

  - 例子

  ```shell
  # 将当前目录下的mall-tiny-docker-file.jar包复制到docker容器的/目录下
  ADD mall-tiny-docker-file.jar /mall-tiny-docker-file.jar
  ```

- ENTRYPOINT

  - 指定 docker 容器启动时执行的命令，格式：

  ```shell
  ENTRYPOINT ["executable", "param1","param2"...]Copy to clipboardErrorCopied
  ```

  - 例子

  ```shell
  # 指定docker容器启动时运行jar包
  ENTRYPOINT ["java", "-jar","/mall-tiny-docker-file.jar"]Copy to clipboardErrorCopied
  ```

- ENV

  - 用于设置环境变量，格式：

  ```shell
  ENV <key> <value>Copy to clipboardErrorCopied
  ```

  - 例子
  
  ```shell
  # mysql运行时设置root密码
  ENV MYSQL_ROOT_PASSWORD rootCopy to clipboardErrorCopied
  ```

- EXPOSE

  - 声明需要暴露的端口（只声明不会打开端口），格式：

  ```shell
  EXPOSE <port1> <port2> ...Copy to clipboardErrorCopied
  ```

  - 例子
  
  ```shell
  # 声明服务运行在8080端口
  EXPOSE 8080Copy to clipboardErrorCopied
  ```

- FROM

  - 指定所需依赖的基础镜像，格式：

  ```shell
  FROM <image>:<tag>Copy to clipboardErrorCopied
  ```

  - 例子
  
  ```shell
  # 该镜像需要依赖的java8的镜像
  FROM java:8Copy to clipboardErrorCopied
  ```

- MAINTAINER

  - 指定维护者的名字，格式：

  ```shell
  MAINTAINER <name>Copy to clipboardErrorCopied
  ```

  - 例子
  
  ```shell
  MAINTAINER macrozhengCopy to clipboardErrorCopied
  ```


- RUN

  - 在容器构建过程中执行的命令，我们可以用该命令自定义容器的行为，比如安装一些软件，创建一些文件等，格式：

  ```shell
  RUN <command>
  RUN ["executable", "param1","param2"...]Copy to clipboardErrorCopied
  ```

  - 例子

  ```shell
  # 在容器构建过程中需要在/目录下创建一个mall-tiny-docker-file.jar文件
  RUN bash -c 'touch /mall-tiny-docker-file.jar'Copy to clipboardErrorCopied
  ```



#### 例子

```
docker run -d --name prometheus \
-p 9090:9090  \
-v ~/docker/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
prom/prometheus
```

```
docker run -d --name mysql \
-p 3306:3306 \
-v ~/docker/mysql5.7/config/mysqld.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf \
-v ~/docker/mysql5.7/data/mysql:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:5.7
```

```
docker run -d --name nginx \
-p 80:80 \
-v ~/docker/nginx/nginx.conf:/etc/nginx/nginx.conf \
-v ~/docker/nginx/conf.d:/etc/nginx/conf.d \
-v ~/docker/nginx/logs:/var/log/nginx \
nginx
```

```
docker run -itd --name redis \
-p 6379:6379 \
redis
 
 docker exec -it redis-test /bin/bash
 redis-cli
```

```
docker run -d --name postgres12.1 -p 5432:5432 \
-v ~/docker/postgres:/var/lib/postgresql/data \
-e POSTGRES_PASSWORD=chimanloo1004 \
postgres:12.1

默认用户名postgres
docker exec -it postgres12.1 /bin/bash
su postgres 										# 切换用户
createuser  -p -s -e username 	# 创建用户
psql -U username 								# 连接数据库
```

```
docker run -d --name=es7 \
-p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" elasticsearch:7.5.1
docker cp es7:/usr/share/elasticsearch/data ~/docker/elasticsearch/
docker cp es7:/usr/share/elasticsearch/logs ~/docker/elasticsearch/
docker rm -f es7

docker run -d --name=es7 \
-p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" \
-v ~/docker/elasticsearch/data:/usr/share/elasticsearch/data \
-v ~/docker/elasticsearch/logs:/usr/share/elasticsearch/logs \
elasticsearch:7.5.1
```

```
docker run --name oap -d \
-e TZ=Asia/Shanghai \
-p 12800:12800 \
-p 11800:11800 \
--link es7:es7 \
-e SW_STORAGE=elasticsearch7 \
-e SW_STORAGE_ES_CLUSTER_NODES=es7:9200 \
apache/skywalking-oap-server:8.3.0-es7


docker run -d --name skywalking-ui \
-e TZ=Asia/Shanghai \
-p 8088:8080 \
--link oap:oap \
-e SW_OAP_ADDRESS=oap:12800 \
apache/skywalking-ui:8.3.0

-javaagent:/Users/mac/docker/skywalking/apache-skywalking-apm-bin-es7/agent/skywalking-agent.jar
-Dskywalking.agent.service_name=CommonDemo
-Dskywalking.collector.backend_service=127.0.0.1:11800
```

```
docker run -d --name sentinel-dashboard \
-p 8858:8858 \
bladex/sentinel-dashboard
```

```
docker run -d --name minio \
-p 9000:9000 \
-e "MINIO_ACCESS_KEY=admin" \
-e "MINIO_SECRET_KEY=chimanloo1004" \
-v ~/docker/minio/data:/data \
-v ~/docker/minio/config:/root/.minio \
minio/minio server /data
```

```
docker run -d --name test \
-p 7474:7474 \
-p 7687:7687 \
-v ~/docker/neo4j/data:/data \
-v ~/docker/neo4j/logs:/logs \
-v ~/docker/neo4j/conf:/var/lib/neo4j/conf  \
-v ~/docker/neo4j/import:/var/lib/neo4j/import \
-e NEO4J_AUTH=neo4j/admin \
neo4j
```

