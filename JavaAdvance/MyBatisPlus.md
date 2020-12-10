## MybatisPLus

#### 简介

- MyBatis 最好的搭档，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生



#### 支持的数据库

- mysql 、mariadb 、oracle 、db2 、h2 、hsql 、sqlite 、postgresql 、sqlserver 、presto 、Gauss 、Firebird
- Phoenix 、clickhouse 、Sybase ASE 、 OceanBase 、达梦数据库 、虚谷数据库 、人大金仓数据库 、南大通用数据库



### CRUD

#### 准备

```mysql
CREATE TABLE `student`  (
  `id` int(0) NOT NULL AUTO_INCREMENT,
  `dept_id` int(0) NULL DEFAULT NULL,
  `name` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NULL DEFAULT NULL,
  `remark` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 7 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_bin ROW_FORMAT = Dynamic;

INSERT INTO `student` VALUES (1, 1, '小菜', '关注小菜不迷路！');
INSERT INTO `student` VALUES (2, 2, '小明', '好好学习，天天向上！');
```

```java
@Data
@Builder
@TableName("student")
public class User {

    private Integer id;

    private Integer deptId;

    private String name;

    private String remark;
}
```

```java
public interface UserMapper extends BaseMapper<User> {}
```



#### Insert、插入

```java
@Test
public void insert() {
    // 这里使用了 lombok 中的建造者模式构建对象
    User user = User.builder().deptId(1).name("小华").remark("小华爱学习").build();
    int insertFlag = userMapper.insert(user);
}
```



#### Update、更新

- 基本

```java
int updateById(Param("et") T entity);

int update(@Param("et") T entity, @Param("ew") Wrapper<T> updateWrapper);
```

- 实战：单个

```java
// 根据 ID 更新

@Test
public void update() {
    User user = User.builder().id(3).name("小华").remark("小华爱玩游戏").build();
    userMapper.updateById(user);
}

/** 
 * OUTPUT:
 * User(id=3, deptId=1, name=小华, remark=小华爱玩游戏)
**/

```

```java
// 根据条件更新单条

@Test
public void update() {
    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    updateWrapper.eq("name","小华").set("remark","小华爱下棋");
    userMapper.update(null, updateWrapper);
}

/** 
 * OUTPUT:
 * User(id=3, deptId=1, name=小华, remark=小华爱下棋)
**/
```

```java
// 根据条件更新单条，用user

@Test
public void update() {
    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    updateWrapper.eq("name","小华");
    User user = User.builder().remark("小华爱游泳").build();
    userMapper.update(user, updateWrapper);
}

/** 
 * OUTPUT:
 * User(id=3, deptId=1, name=小华, remark=小华爱游泳)
**/
```



#### Delete、删除

- 基本

```java
int deleteById(Serializable id);

int deleteByMap(@Param("cm") Map<String, Object> columnMap);

int delete(@Param("ew") Wrapper<T> wrapper);

int deleteBatchIds(@Param("coll") Collection<? extends Serializable> idList);
```

- 实战：单个

```java
// 根据 ID 删除

@Test
public void deleteById() {
    userMapper.deleteById(3);
}

/** 
 * SQL语句：
 * DELETE FROM student WHERE id = 3;
**/
```

```java
@Test
public void deleteByMap() {
    HashMap<String, Object> columnMap = new HashMap<>();
    columnMap.put("name","小华");
    columnMap.put("remark","小华爱游泳");
    userMapper.deleteByMap(columnMap);
}

/**
 * SQL语句：
 * DELETE FROM student WHRE name = '小华' AND remark = '小华爱游泳';
**/
```

```java
// 根据条件删除单条

@Test
public void delete() {
    UpdateWrapper<User> wrapper = new UpdateWrapper<>();
    wrapper.eq("remark","小华爱下棋");
    userMapper.delete(wrapper);
}

/** 
 * SQL语句：
 * DELETE FROM student WHRE remark = '小华爱下棋';
**/
```

```java
// 根据条件删除单条2

@Test
public void delete() {
    User user = User.builder().remark("小华爱下棋").build();
    UpdateWrapper<User> wrapper = new UpdateWrapper<>(user);
    userMapper.delete(wrapper);
}
/** 
 * SQL语句：
 * DELETE FROM student WHRE remark = '小华爱下棋';
**/

```

- 实战：批量

```java
// 根据条件删除多条

@Test
public void deleteBatchIds() {
    List<Integer> idList = new ArrayList<>();
    idList.add(4);
    idList.add(7);
    userMapper.deleteBatchIds(idList);
}
/** 
 * SQL语句：
 * DELETE FROM student WHERE id In (4,7)
**/
```



#### Select、查询

- 基本

```java
T selectById(Serializable id);

List<T> selectBatchIds(@Param("coll") Collection<? extends Serializable> idList);

List<T> selectByMap(@Param("cm") Map<String, Object> columnMap);

T selectOne(@Param("ew") Wrapper<T> queryWrapper);

Integer selectCount(@Param("ew") Wrapper<T> queryWrapper);

List<T> selectList(@Param("ew") Wrapper<T> queryWrapper);

List<Map<String, Object>> selectMaps(@Param("ew") Wrapper<T> queryWrapper);

List<Object> selectObjs(@aram("ew") Wrapper<T> queryWrapper);

IPage<T> selectPage(IPage<T> page, @Param("ew") Wrapper<T> queryWrapper);

IPage<Map<String, Object>> selectMapsPage(IPage<T> page, @Param("ew") Wrapper<T> queryWrapper);
```

- 实战：单个

```java
// 根据 ID 查询

@Test
public void selectById() {
    User user = userMapper.selectById(1);
    System.out.println(user);
}

/** 
 * OUTPUT:
 * User(id=1, deptId=1, name=小菜, remark=关注小菜不迷路！)
 *
 * SQL语句:
 * SELECT id, dept_id, name, remark FROM student WHERE ID = 1;
**/
```

```java
// 查询数量

@Test
public void selectCount() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.like("name","小");
    System.out.println(userMapper.selectCount(queryWrapper));
}

/** 
 * OUTPUT:
 * 2
 *
 * SQL语句:
 * SELECT COUNT( 1 ) FROM student WHERE (name LIKE '%小%');
**/
```

```java
// 根据条件查询单条

@Test
public void selectOne() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.eq("name","小菜");
    User user = userMapper.selectOne(queryWrapper);
    System.out.println(user);
}

/**
 * OUTPUT:
 * User(id=1, deptId=1, name=小菜, remark=关注小菜不迷路！)
 *
 * SQL语句:
 * SELECT id, name, dept_id, remark FROM student WHERE (name = '小菜');
**/
```

- 实战：批量

```java
// 查询所有

@Test
public void selectList() {
    List<User> users = userMapper.selectList(null);
    users.forEach(System.out::println);
}

/** 
 * OUTPUT:
 * User(id=1, deptId=1, name=小菜, remark=关注小菜不迷路！)
 * User(id=2, deptId=1, name=小明, remark=好好学习，天天向上！)
 *
 * SQL语句:
 * SELECT id, dept_id, name, remark FROM student;
**/
```

```java
// 根据 ID 批量查询

@Test
public void selectBatchIds() {
    List<User> users = userMapper.selectBatchIds(Arrays.asList(1, 2));
    users.forEach(System.out::println);
}
/** 
 * OUTPUT:
 * User(id=1, deptId=1, name=小菜, remark=关注小菜不迷路！)
 * User(id=2, deptId=1, name=小明, remark=好好学习，天天向上！)
 * 
 * SQL语句:
 * SELECT id, dept_id, name, remark FROM student WHERE ID IN (1, 2);
**/
```

```java
// 根据条件查询多条

@Test
public void selectByMap() {
    HashMap<String, Object> columnMap = new HashMap<>();
    columnMap.put("name","小");
    List<User> users = userMapper.selectByMap(columnMap);
    users.forEach(System.out::println);
}

/**
* OUTPUT:
* null
*
* SQL语句:
* SELECT id, name, dept_id, remark FROM student WHERE name = '小';
**/
```

- 分页

```java
@Test
public void selectPage() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.like("name", "小");
    Page<User> page = new Page<>(1, 1);
    IPage<User> userIPage = userMapper.selectPage(page, queryWrapper);
    System.out.println("数据总数:" + userIPage.getTotal());
    System.out.println("总页数:" + userIPage.getPages());
    System.out.println("当前页:" + userIPage.getCurrent());
    System.out.println("页大小:" + userIPage.getSize());
    userIPage.getRecords().forEach(System.out::println);
}

/**
 * OUTPUT:
 * 数据总数:2
 * 总页数:2
 * 当前页:1
 * 页大小:1
 * User(id=1, deptId=1, name=小菜, remark=关注小菜不迷路！)
 *
 * SQL语句：
 * SELECT id, name, dept_id, remar FROM student WHERE (name LIKE '%小%') LIMIT 0,1;
**/
```



### 条件构造器

- 在 CRUD 的基本操作中，我们想要通过条件查询都是通过 Wrapper 类进行封装的



#### allEq

- 全部 eq 或个别 isNull

```java
allEq(Map<R, V> params)
allEq(Map<R, V> params, boolean null2IsNull)
allEq(boolean condition, Map<R, V> params, boolean null2IsNull)
    
allEq(BiPredicate<R, V> filter, Map<R, V> params)
allEq(BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull)
allEq(boolean condition, BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull) 
```

  - 参数说明

    - param： key 为数据库字段名，value 为字段
    - nullsIsNull：为 true 则在 map 的 value 为 null 时调用 isNull 方法，为 false 时则忽略 value 为 null 时不调用 isNull 方法
    - filter： 过滤函数，判断是否允许字段传入比对条件中

  - 实战

```java
// allEq(Map<R, V> params)

@Test
public void testAllEq() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    Map<String,Object> params = new HashMap<>();
    params.put("name","小菜");
    params.put("dept_id",1);
    params.put("remark",null);
    queryWrapper.allEq(params); //会调用 isNull 方法
    userMapper.selectList(queryWrapper);
}
/** 
 * OUTPUT:
 *｛｝
 *
 * SQL语句:
 * SELECT id,name,dept_id,remark FROM student WHERE (name = '小菜' AND dept_id = 1 AND remark IS NULL);
 **/
```

```java
// allEq(Map<R, V> params, boolean null2IsNull)

@Test
public void testAllEq() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    Map<String,Object> params = new HashMap<>();
    params.put("name","小菜");
    params.put("dept_id",1);
    params.put("remark",null);
    queryWrapper.allEq(params, false); //不会调用 isNull 方法
    userMapper.selectList(queryWrapper);
}
/**
* OUTPUT:
* User(id=1, deptId=1, name=小菜, remark=关注小菜不迷路！)
* 
* SQL语句:
* SELECT id,name,dept_id,remark FROM student WHERE (name = '小菜' AND dept_id = 1);
**/
```

```java
// allEq(boolean condition, Map<R, V> params, boolean null2IsNull)

@Test
public void testAllEq() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    Map<String,Object> params = new HashMap<>();
    params.put("name","小菜");
    params.put("dept_id",1);
    params.put("remark",null);
    queryWrapper.allEq(false,params,false); //不会带入条件进行查询
    userMapper.selectList(queryWrapper);
}
/** 
 * OUTPUT:
 * {name=小菜, remark=关注小菜不迷路！, id=1, dept_id=1}
 * {name=小明, remark=好好学习，天天向上！, id=2, dept_id=1}
 *
 * SQL语句:
 * SELECT id,name,dept_id,remark FROM student;
 **/
```

```java
// allEq(BiPredicate<R, V> filter, Map<R, V> params)

@Test
public void testAllEq() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    Map<String, Object> params = new HashMap<>();
    params.put("name", "小菜");
    params.put("dept_id", 1);
    params.put("remark", null);
    //只有 key 中含有 “m” 才会用作条件判断
    queryWrapper.allEq((k, v) -> (k.contains("m")), params);
    userMapper.selectList(queryWrapper);
}
/** 
 * OUTPUT:
 * 0
 *
 * SQL语句:
 * SELECT id,name,dept_id,remark FROM student WHERE (name = '小菜' AND remark IS NULL);
 **/
```

#### 

#### 比较操作

- eq： 相当于 `=`
- ne： 相当于 `!=`
- gt： 相当于 `>`
- ge： 相当于`>=`
- lt：  相当于 `<`
- le：  相当于`<=`
- between：  相当于`between ... and ...`
- notBetween：  相当于`not between ... and ...`
- in：  相当于`in(.., .., ..)`
- notIn：  相当于`not in(.., .., ..)`



#### 模糊查询

- like： `like("name","tom") --> name like "%tom%"`
- notLike： `notLike("name","tom") --> name not like "%tom%"`
- likeLeft**：** `like("name","tom") --> name like "%tom"`
- likeRight： `like("name","tom") --> name like "tom%"`



#### 排序

- orderBy
- orderByAsc
- orderByDesc



#### 逻辑查询

- or
  - 拼接
    - 主动调用 or 表示紧接着下一个方法不是用 and 连接
    - 不调用 or 则默认为使用 and 连接
    - `eq("id",1).or().eq("name","老王")`
  - 嵌套
    - `or(i -> i.eq("name", "李白").ne("status", "活着"))`

- and

  - 嵌套
    - `and(i -> i.eq("name", "李白").ne("status", "活着"))`



