## Activity

#### 简介

- Activiti5是由Alfresco软件在2010年5月17日发布的**业务流程管理（BPM）框架**，它是覆盖了业务流程管理、工作流、服务协作等领域的一个开源的、灵活的、易扩展的可执行流程语言框架，一般称作为工作流框架

- Activiti基于Apache许可的开源BPM平台，创始人Tom Baeyens是JBoss jBPM的项目架构师，它特色是提供了eclipse插件，开发人员可以通过插件直接绘画出业务流程图



#### 优点

- 提高系统的柔性，适应业务流程的变化
- 实现更好的业务过程控制，提高顾客服务质量
- 降低系统开发和维护成本



#### 数据库相关

- 在执行工作流步骤的时候会涉及到很多数据，执行该流程的人是谁、所需要的参数是什么、包括想查看之前流程执行的记录等等，因此需要用到数据库的表来保存数据
- Activiti框架会自动帮我们把对应的数据库表创建起来，它涉及的表有23个

```
Activiti的后台是有数据库的支持，所有的表都以ACT_开头。 第二部分是表示表的用途的两个字母标识。 用途也和服务的API对应。
ACT_RE: 'RE'表示repository。 这个前缀的表包含了流程定义和流程静态资源 （图片，规则，等等）。
ACT_RU_*: 'RU'表示runtime。 这些运行时的表，包含流程实例，任务，变量，异步任务，等运行中的数据。 Activiti只在流程实例执行过程中保存这些数据， 在流程结束时就会删除这些记录。 这样运行时表可以一直很小速度很快。
ACT_ID_*: 'ID'表示identity。 这些表包含身份信息，比如用户，组等等。
ACT_HI_*: 'HI'表示history。 这些表包含历史数据，比如历史流程实例， 变量，任务等等。
ACT_GE_*: 通用数据， 用于不同场景下，如存放资源文件。
```



#### Activiti的开发步骤

- 首先就要把这个工作流定义出来，也就是工作流的步骤的怎么样的，Activiti支持以“图”的方式来定义工作流
- 定义完工作流，就要部署到起来，可以联想到 Tomcat，我们光下载了 Tomcat 是没有用的，要把它部署起来
- 随后就执行该工作流，该工作流就随着我们定义的步骤来一一执行



1. 工作流引擎

```java
@Test
public void createActivitiEngine(){

  /**1.通过代码形式创建
    *  - 取得ProcessEngineConfiguration对象
    *  - 设置数据库连接属性
    *  - 设置创建表的策略 （当没有表时，自动创建表）
    *  - 通过ProcessEngineConfiguration对象创建 ProcessEngine 对象
    */

  //取得ProcessEngineConfiguration对象
  ProcessEngineConfiguration engineConfiguration = ProcessEngineConfiguration.
  createStandaloneProcessEngineConfiguration();
  //设置数据库连接属性
  engineConfiguration.setJdbcDriver("com.mysql.jdbc.Driver");
  engineConfiguration.setJdbcUrl("jdbc:mysql://localhost:3306/activitiDB?createDatabaseIfNotExist=true" + "&useUnicode=true&characterEncoding=utf8");
  engineConfiguration.setJdbcUsername("root");
  engineConfiguration.setJdbcPassword("root");


  // 设置创建表的策略 （当没有表时，自动创建表）
  // public static final java.lang.String DB_SCHEMA_UPDATE_FALSE = "false";//不会自动创建表，没有表，则抛异常
  // public static final java.lang.String DB_SCHEMA_UPDATE_CREATE_DROP = "create-drop";//先删除，再创建表
  // public static final java.lang.String DB_SCHEMA_UPDATE_TRUE = "true";//假如没有表，则自动创建
  engineConfiguration.setDatabaseSchemaUpdate("true");
  //通过ProcessEngineConfiguration对象创建 ProcessEngine 对象
  ProcessEngine processEngine = engineConfiguration.buildProcessEngine();
  System.out.println("流程引擎创建成功!");

}
```

2. 定义工作流（插件）

3. 部署工作流
   - 把工作流部署到工作流引擎中

```java
@Test
public void deploy() {

  //获取仓库服务 ：管理流程定义
  RepositoryService repositoryService = processEngine.getRepositoryService();
  Deployment deploy = repositoryService.createDeployment()//创建一个部署的构建器
    .addClasspathResource("LeaveActiviti.bpmn")//从类路径中添加资源,一次只能添加一个资源
    .name("请求单流程")//设置部署的名称
    .category("办公类别")//设置部署的类别
    .deploy();

  System.out.println("部署的id"+deploy.getId());
  System.out.println("部署的名称"+deploy.getName());
}
```

4. 执行工作流
   - 指定工作流就是定义时工作流程表的id

```java
@Test
public void startProcess(){

  // 指定执行我们刚才部署的工作流程
  String processDefiKey="leaveBill";
  // 取运行时服务
  RuntimeService runtimeService = processEngine.getRuntimeService();
  // 取得流程实例
  // 通过流程定义的key 来执行流程
  ProcessInstance pi = runtimeService.startProcessInstanceByKey(processDefiKey);
  System.out.println("流程实例id:"+pi.getId());//流程实例id
  System.out.println("流程定义id:"+pi.getProcessDefinitionId());//输出流程定义的id
}
```

5. 任务办理人查询当前任务的信息

```java
//查询任务
@Test
public void queryTask(){
  //任务的办理人
  String assignee="tom";
  //取得任务服务
  TaskService taskService = processEngine.getTaskService();
  //创建一个任务查询对象
  TaskQuery taskQuery = taskService.createTaskQuery();
  //办理人的任务列表
  List<Task> list = taskQuery.taskAssignee(assignee)//指定办理人
    .list();
  //遍历任务列表
  if(list!=null&&list.size()>0){
    for(Task task:list){
      System.out.println("任务的办理人："+task.getAssignee());
      System.out.println("任务的id："+task.getId()); // 查出304
      System.out.println("任务的名称："+task.getName());
    }
  }
}
```

6. 处理任务
   - 查出任务id，就根据此id继续流程

```java
//完成任务
@Test
public void compileTask(){
  String taskId="304";
  //taskId：任务id
  processEngine.getTaskService().complete(taskId);
  System.out.println("当前任务执行完毕");
}
```



#### 总结

- 如果一个业务需要多方面角色进行处理的话，那最好用工作流框架。因为如果其中一个环节的需求发生了变化的话，不用工作流，那就需要修改很多的代码，十分麻烦

- Activiti 工作流框架快速入门

  - 定义工作流，使用插件来把我们的流程图画出来。这个流程图就是我们定义的工作流
  - 工作流引擎是工作流的核心，能够让我们定义出来的工作流部署起来
  - 由于我们使用工作流的时候是有很多数据产生的，因此 Activiti 是将数据保存到数据库表中的。这些数据库表由 Actitviti 创建，由 Activiti 维护
  - 部署完的工作流是需要手动去执行该工作流的
  - 根据由谁处理当前任务，我们就可以查询出具体的任务信息
  - 根据任务的id，我们就可以执行任务了

- 流程定义涉及到了四张数据库表

  - 我们可以通过 API 把我们的流程定义图读取出来
  - 可以根据查询最新版本的流程定义
  - 删除流程定义
  - 部署流程定义的时候也可以是 ZIP文件

- 流程在运行中，涉及到两个对象，四张数据库表

  - 获取流程实例和任务的历史信息
  - 判断流程实例是否为空来判断流程是否结束了
  - 查看正在运行服务的详细信息
  - 通过流程实例来开启流程
  - 如果流程没有分支的话，那么流程实例就等于流程对象
  - 流程实例
  - 流程任务
  - 流程实例可以有多个，流程对象只能有一个
  - 基于这么两个对象，我们就可以做很多事情了

- 流程变量

  - 它涉及到两张表，流程变量实际上就是我们的条件

- - 流程变量的作用域只在流程实例中
  - 我们可以在流程开始的时候设置流程变量，在任务完成的时候设置流程变量
  - 运行时服务和流程任务都可以设置流程变量

- 通过连线我们可以在其中设置条件，根据不同的条件流程走不同的分支

- 如果没有设置默认的条件，当条件不吻合的时候，那么流程就走不下去了，因此需要排他网关来设置一条默认的路径

