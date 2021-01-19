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



