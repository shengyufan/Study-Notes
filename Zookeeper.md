[toc]

<div STYLE="page-break-after:always;"></div>

# 概念

Zookeeper是Hadoop项目下的一个子项目，是一个**树形**目录服务

Zookeeper是一个分布式的、开源的分布式应用程序的协调服务，用于管理应用程序。提供三种主要功能：1）配置管理；2）分布式锁；3）集群管理

# 命令操作

## 数据模型

树形服务，其数据模型和Unix文件系统目录树类似，拥有一个层次化结构

每个节点保存**数据**和**节点信息**，节点可以拥有子节点，也允许存储少量数据

分类：1）PERSISTENT - 持久化节点；2）EPHEMERAL - 临时节点；3）PERSISTENT_SEQUENTIAL - 持久化顺序节点；4）EPHEMERAL_SEQUENTIAL- 临时顺序节点

## 服务端命令

`./zkServer.sh start`：启动Zookeeper服务

`./zkServer.sh status`：查看Zookeeper服务状态

`./zkServer.sh stop`：停止Zookeeper服务

`./zkServer.sh restart`：重启Zookeeper服务

## 客户端命令

`./zkCli.sh -server [ip:2181]`：连接zookeeper服务端

`create /[节点] [数据]`：创建节点

`get /[节点]`：获取节点数据

`set /[节点] [数据]`：修改节点数据

`delete /[节点]`：删除节点，若存在子节点，需要使用 `deleteall`

# API操作

## Curator介绍

Curator是zookeeper的Java客户端库，目标是简化zookeeper客户端的使用

## 常用操作

### 建立连接

```java
@Test
public void testConnect() {
  // 方式1
  /*
  参数列表：
  1. 连接字符串，声明服务端地址和端口
  2. 会话超时时间 (ms)
  3. 连接超时时间 (ms)
  4. 重试策略
  */
  RetryPolicy policy = new ExponentialBackoffRetry(300, 10);
  CuratorFramework client = CuratorFrameworkFactory.newClient(@params);
  client.start();
  
  // 方式2
  CuratorFramework client1 = CuratorFrameworkFactory.builder().connectString().sessionTimeoutMs().connectionTimeoutMs().retryPolicy().build();
  client1.start();
}
```

### 创建节点

```java
@Test
public void testCreate() throws Exception{
  // 创建节点默认为持久化节点
  // 如果创建节点没有指定数据，则默认将当前ip地址作为数据存储
  client.create().forPath("/app1");
  client.create().forPath("/app2", "test".getBytes());
  
  // 创建其他类型的节点
  client.create().withMode(CreateMode.EPHEMERAL).forPath("/app3");
  
  // 创建多级节点
  client.create().creatingParentsIfNeeded().forPath("/app4/p1");
}
```

### 查询节点

```java
@Test
public void testGet() throws Exception{
  // 查询数据: get
  byte[] data = client.getData().forPath("/app1");
  
  // 查询子节点: ls
  List<String> path = client.getChildren().forPath("/app4");
  
  // 查询节点状态信息: ls -s
  Stat status = new Stat();
  client.getData().storingStateIn(status).forPath("/app3");
}
```

### 修改节点

```java
@Test
public void testSet() throws Exception{
  // 修改数据
  client.setData().forPath("/app2", "newtest".getBytes());
  
  // 根据版本修改
  int version = status.getVersion(); // 在状态信息中查询version
  client.setData().withVersion(version).forPath("/app2", "test1".getBytes());
}
```

### 删除节点

```java
@Test
public void testDelete() throws Exception {
  // 删除节点
  client.delete().forPath("/app1");
  
  // 删除带有子节点的节点
  client.delete().deleteChildrenIfNeeded().forPath("/app4");
  
  // 必须成功的删除
  client.detele().guaranteed().forPath("/app3");
  
  // 回调
  client.delete().guaranteed().inBackground(new BackgroundCallbak() {}).forPath("/app3");
}
```

### Watch 事件监听

ZooKeeper 允许用户在指成节点上注册一些Watcher，并且在一些特定事件触发的时候，ZooKeeper 服务端会将事件通知到感兴趣的客户端上去，该机制是ZooKeeper实现**分布式协调服务**的重要特性

提供三种监听器

- ﻿﻿NodeCache：只是监听某一个特定的节点 

  ```java
  @Test
  public void testNodeCache() throws Exception {
    // 创建对象
    final NodeCache nodeCache = new NodeCache(client, "/app1");
    // 注册监听
    nodeCache.getListenable().addListener(new NodeCacheListener() {
      @Override
      public void nodeChanged() throws Exception {
        // 执行操作
        // 例: 获取修改后数据
        byte[] data = nodeCache.getCurrentData().getData();
      }
    });
    //开启监听，设置为true，表示加载缓冲数据
    nodeCache.start(true);
  }
  ```

- ﻿﻿PathChildrenCache：监控一个ZNode的子节点.

  ```java
  @Test
  public void testPathChildrenCache() throws Exception {
     // 创建对象
    final PathChildrenCache pathChildrenCache = new PathChildrenCache(client, "/app1", true);
    // 注册监听
    pathChildrenCache.getListenable().addListener(new PathChildrenCacheListener() {
      @Override
      public void childEvent(CuratorFramework client, PathChildrenCacheEvent event) throws Exception {
        // 执行操作
        // 例: 获取子节点新数据
        PathChildrenCacheEvent.Type type = event.getType();
        if (type.equals(PathChildrenCacheEvent.Type.CHILD_UPDATED)) {
          byte[] data = event.getData().getData();
        }
      }
    });
    //开启监听，设置为true，表示加载缓冲数据
    pathChildrenCache.start();
  }
  ```

- ﻿﻿TreeCache：可以监控整个树上的所有节点，类似于PathChildrenCache和NodeCache的组合

  ```java
  @Test
  public void testTreeCache() throws Exception {
    // 创建对象
    final TreeCache treeCache = new treeCache(client, "/app1", true);
    // 注册监听
    treeCache.getListenable().addListener(new TreeCacheListener() {
      @Override
      public void childEvent(CuratorFramework client, TreeCacheEvent event) throws Exception {
        // 执行操作
      }
    });
    //开启监听，设置为true，表示加载缓冲数据
    treeCache.start();
  }
  ```

# 分布式锁

分布式锁解决**跨机器**的进程之间的数据同步问题

常用的实现方式

1. 基于缓存（Redis）
2. Zookeeper
3. 数据库层面（悲观锁、乐观锁）

## 原理

核心思想：当客户端获取锁，则创建节点；使用完锁，删除该节点

1. 客户端获取锁时，在lock节点下创建**临时顺序**节点，保证出现异常时也能释放锁
2. 然后获取lock下面的所有子节点，客户端获取到所有的子节点之后，如果发现自己创建的子节点序号最小，那么就认为该客户端获取到了锁。使用完锁后，将该节点删除
3. 如果发现自己创建的节点并非lock所有子节点中最小的，说明自己还没有获取到锁，此时客户端露要找到比自己小的那个节点，同时对其注册事件监听器，监听删除事件
4. 如果发现比自己小的那个节点被删除，则客户端的Watcher会收到相应通知，此时再次判断自己创建的节点是否是lock子节点中序号最小的，如果是则获取到了锁，如果不是则重复以上步骤继续获取到比自己小的一个节点并注册监听

# 集群

## Leader选举

Serverid：服务器ID。比如有三台服务器，编号分别是1，2，3。 编号越大在选择算法中的权重越大

﻿﻿Zxid：数据ID。服务器中存放的最大数据ID.值越大说明数据 越新，在选举算法中数据越新权重越大

在Leader选举的过程中，如果某台ZooKeeper获得了超过半数的选票，则此ZooKeeper就可以成为Leader了

## 故障

1. 从服务器宕机，若集群中还有超过半数的服务器运行，整个集群正常运行
2. 主服务器宕机，集群会重新选举新leader

## 角色

在ZooKeeper集群服务中有三个角色：

- Leader领导者：
  1. ﻿﻿处理事务请求
  2. ﻿集群内部各服务器的调度者
- Follower 跟随者：
  1. ﻿﻿处理客户端非事务请求，转发事务请求给Leader服务器
  2. ﻿﻿参与Leader选举投票
- Observer 观察者：
  1. 处理客户端非事务请求，转发事务请求给Leader服务器
  2. 不参与Leader选举投票
