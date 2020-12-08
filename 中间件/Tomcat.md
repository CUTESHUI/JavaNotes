## Tomcat8

#### 启动关闭

- 目录
  - Library/Tomcat8
- 开启
  1. cd Library/Tomcat8/bin
  2. sudo sh ./startup.sh
- 关闭
  1. cd /usr/local/Tomcat8/bin
  2. sh ./shutdown.sh
- 目录简单介绍

  - bin：启动和关闭tomcat的bat文件
  - conf：配置文件
  - - `server.xml`该文件用于配置server相关的信息，比如 tomcat 启动的端口号，配置主机 (Host)
    - `web.xml`文件配置与web应用（web应用相当于一个web站点）
    - `tomcat-user.xml`配置用户名密码和相关权限.
  - lib：该目录放置运行 tomcat 运行需要的 jar包
  - logs：存放日志，当我们需要查看日志的时候，可以查询信息
  - webapps：放置我们的web应用

    1. docs
       - tomcat的介绍和操作文档等
2. examples
       - 小程序示例
    3. host-manager
       - host 管理
    4. manager（重点）
   - 进行 Server Status 和 Applications 管理
       - 对服务器和其他应用进行启动、重启、关闭等操作
       - 对 Session、JVM 性能参数等进行监听并管理
    5. ROOT
       - 根目录
- work工作目录：该目录用于存放 jsp 被访问后生成对应的 server文件和.class文件

- Tomcat部署方式

  1. 直接把Web项目放在 webapps下，Tomcat 会自动将其部署

  2. 在server.xml 文件上配置  <Context> 节点，设置相关的属性即可

  3. 通过Catalina来进行配置:进入到conf\Catalina\localhost文件下，创建一个xml文件，该文件的名字就是站点的名字。编写XML的方式来进行设置

  - 第2点的步骤

    - 找到Tomcat目录下 /conf/server.xml 文件

    - 在server.xml中的节点下添加如下代码

      path 表示的是访问时输入的web项目名

      docBase 表示的是站点目录的绝对路径

    ```
    <Context  path="TomcatTest" 
    docBase="/Users/mac/IdeaProjects/TomcatTest"
    />
    ```

    - 启动 Tomcat
    - 在浏览器中输入 http://localhost:8080/TomcatTest/web/index.jsp 

- Tomcat8/webapps/ 目录下可以放资源，并输入具体路径访问

  - 比如：

    - 1.jpg放在 webapps/examples/ 下，启动 Tomcat后，通过 localhost://8080/examples/1.jpg 显示1.jpg

    - 1.jpg放在 webapps/test/examples/ 下，启动 Tomcat后，通过 localhost://8080/test/1.jpg 显示1.jpg、

      其中test是自己建的文件夹

  - 默认从 webapps/ 开始，输入路径的时候不用从 webapps/... 开始 
  

---





- 问题

  启动tomcat时，一直卡在Deploying web application directory这块的解决方案

  本来今天正常往服务器上扔一个tomcat 部署一个项目的，最后再启动tomcat 的时候 发现项目一直都访问不了

  始终都卡在这个目录上，然后我就等，一直等了差不多有7、8分钟才完全启动起来，这就让我很诧异，

  webapps下没有自己的项目光跑tomcat自己的项目都卡成这样，这还了得，看来tomcat想上天啊这是，我就查阅了一下资料，

  发现网上说了很多，大部分是废话，不过在这其中发现了一篇文章说的非常好，有效的解决了当前的问题。

  http://blog.csdn.net/njchenyi/article/details/46641141

  这篇文章的大意就是下面这句话：

  linux或者部分unix系统提供随机数设备是/dev/random 和/dev/urandom ，

  两个有区别，urandom安全性没有random高，但random需要时间间隔生成随机数。jdk默认调用random。

  然后就很简单啦，找到对应的配置文件去修改就好了

  解决：

  - 找到jdk1.x.x_xx/jre/lib/security/[Java](http://lib.csdn.net/base/java).security文件，在文件中找到securerandom.source这个设置项，将其改为：

  securerandom.source=file:/dev/./urandom

  securerandom.source=file:/dev/random（原来的）

  

  

  

  