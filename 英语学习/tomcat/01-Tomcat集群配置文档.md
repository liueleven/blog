# Tomcat集群配置文档

> 更新时间：2019-03-25/26/27/28

[原文](http://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html)

### 概述
- 对于不耐烦的人
- 安全
- 集群基础
- 预览
- 集群信息
- 崩溃后将会话绑定到转移节点
- 配置案例
- 集群结构
- 如何工作的
- 使用JMX监控你的集群
- 常见问题解答

### 对于不耐烦的人
在你的<Engine>或你的<Host>元素中添加`<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"/>`来开启集群

使用上述配置将启用session复制通过DeltaManager复制会话。也就是说，每个会话都会复制到集群中的每一个节点上。这对于较小的集群非常有用，但是我们不推荐用于较大的集群（超过4个节点左右）当使用DeltaManager，Tomcat会复制session到每个节点，即使该节点没有部署应用。

为了避开这些问题，你需要使用BackupManager。BackupManager只将会话数据复制到一个备份节点，并且只适用于部署了应用程序的节点。一旦你使用了DeltaManager运行了一个简单的集群，随着集群中节点数量的增加，您可能希望迁移到备份管理器。

以下是一些重要的默认值：
1. 组播地址是228.0.0.4
2. 组播端口是45564（这个端口和地址一起决定集群成员）
3. 广播的ip是`java.net.InetAddress.getLocalHost().getHostAddress()`（你要确保不广播127.0.0.1，这是一个普遍的错误）
4. 监听复制消息的TCP端口是第一个可用的套接字服务，在4000-4100范围内
5. ClusterSessionListener用来配置监听器
6. 有2个拦截器，TcpFailureDetector 和 MessageDispatchInterceptor

以下是默认集群配置：
```
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
                 channelSendOptions="8">

          <Manager className="org.apache.catalina.ha.session.DeltaManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"/>

          <Channel className="org.apache.catalina.tribes.group.GroupChannel">
            <Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/>
            <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                      address="auto"
                      port="4000"
                      autoBind="100"
                      selectorTimeout="5000"
                      maxThreads="6"/>

            <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
              <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
            </Sender>
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatchInterceptor"/>
          </Channel>

          <Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
                 filter=""/>
          <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/>

          <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                    tempDir="/tmp/war-temp/"
                    deployDir="/tmp/war-deploy/"
                    watchDir="/tmp/war-listen/"
                    watchEnabled="false"/>

          <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
        </Cluster>
```
我们将在本文后面更详细地介绍这一部分。


### 安全
集群的实现是基于安全、可信的网络用于所有与集群相关的网络流量而编写的。在不安全、不可信的网络上运行群集是不安全的。

有许多选项可以为Tomcat集群提供安全、可信的网络。其中包括:
- 专用局域网
- 虚拟专用网络
- IPSEC
- 使用EncryptInterceptor加密集群


### 集群基础

要在Tomcat 9容器中运行会话复制，应完成以下步骤:
- 你的所有会话属性都必须实现java.io.Serializable
- 取消对server.xml中群集元素的注释
- 如果您已经定义了自定义集群阀，请确保在server.xml中的集群元素下也定义了ReplicationValve
- 如果您的Tomcat实例运行在同一台计算机上，请确保每个实例的Receiver.port属性都是唯一的，在大多数情况下，Tomcat足够聪明，可以通过自动检测4000 - 4100范围内的可用端口来自行解决这个问题
- 确保你的web.xml有<distributable/>元素
- 如果您正在使用mod_jk，请确保在您的引擎中设置了jvmRoute属性`<Engine name="Catalina" jvmRoute="node01" >`并且jvmRoute的值可以匹配workers.properties中的worker名称
- 确保所有节点具有相同的时间并与NTP服务同步！
- 确保您的负载均衡器配置为粘性（sticky）会话模式。

负载均衡可以通过许多技术来实现，如负载均衡一章所述。

注意:请记住，您的会话状态由cookie跟踪，所以你的网址从外观上看必须是一样的，否则，将会创建一个新的会话。

集群模块使用Tomcat JULI日志框架，因此您可以通过logging.properties文件配置日志记录。要跟踪消息，您可以启用登录键: `org.apache.catalina.tribes.MESSAGES`


### 概述
在Tomcat中启用会话复制，可以遵循三种不同的路径来实现完全相同的事情:
1. 使用会话持久化，并将会话保存到共享文件系统(持久化管理器+文件存储)
2. 使用会话持久化，并将会话保存到共享数据库(持久性管理器+ JDBCStore )
3. 使用内存复制，使用Tomcat附带的Simpletcpcluster (lib/catalina-tribes.jar + lib/catalina-ha.jar)

Tomcat可以使用DeltaManager对会话状态进行all-to-all复制或者使用BackupManager仅向一个节点执行备份复制。 all-to-all 复制算法只在集群数很小的时候才有作用。对于较大的集群，你应该使用BackupManager使用主-从会话复制策略，其中会话将只存储在一个备份节点上。

目前，你可以使用域工作器属性（mod_jk > 1.2.8）来构建集群分区，这有可能通过DeltaManager来获取更具有伸缩性的集群方案（你需要为此配置区域拦截器）。为来在all-to-all的环境中降低网络流量，你可以将集群分成更小的组。可以不同组实现不同的组播地址来实现。一个非常简单的配置如下所示：
```
         DNS Round Robin
               |
         Load Balancer
          /           \
      Cluster1      Cluster2
      /     \        /     \
  Tomcat1 Tomcat2  Tomcat3 Tomcat4
```
这里要提到的重要一点是，会话复制只是集群化的开始。用于实现集群的另一个流行概念是farming，你只将你的app部署到一台服务器上，集群将在整个集群中分布部署。这是FarmWarDeployer (server.xml上的集群示例)可以提供的所有功能

下一节将深入探讨会话复制的工作原理以及如何配置它。

### 集群信息
成员之间通过组播心跳机制建立的。因此，如果您希望细分集群，可以通过更改<Membership>元素中的组播IP地址或端口来实现。

心跳包含Tomcat节点的IP地址和Tomcat监听复制流量的TCP端口。所有数据通信都是通过TCP进行的。

ReplicationValve用来确定请求何时完成，并启动复制（如果有的话）。只有会话发生改变时才会复制数据（通过调用会话中的setAttribute或者removeAttribute。

同步和异步复制是要性能最重要的考量之一。同步复制：在同步复制模式下，请求不会返回，直到复制的会话通过线路发送并在所有其他群集节点上重新插入。异步复制：同步与异步是使用通道内置标志配置的，是一个Integer值。SimpleTcpCluster/DeltaManager组合的默认值是8。

为了方便起见，`channelSendOptions`可以通过名称而不是整数来设置,然后在启动时将其转换为整数值。有效的选项名称是:"asynchronous"(别名：“async”),"byte_message" (别名 "byte"), "multicast", "secure", "synchronized_ack" (别名 "sync"), "udp", "use_ack"。使用逗号分隔多个名称，例如，使用 "async, multicast" 表示选项`SEND_OPTIONS_ASYNCHRONOUS | SEND_OPTIONS_MULTICAST`。

你可以阅读更多关于[发送标志（概述）](http://tomcat.apache.org/tomcat-9.0-doc/tribes/introduction.html)或者[发送标志（Java文档）](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/tribes/Channel.html)。异步复制期间，请求在数据复制之前返回。异步复制产生更短的请求时间，同步复制保证在请求返回之前复制会话。

### 崩溃后将会话绑定到转移节点
如果您使用mod_jk而不使用粘性会话，或者由于某些原因粘性会话不起作用，或者您只是在进行故障切换，则需要修改会话id，因为它以前包含了前一个tomcat的工作器id (由引擎元素中的jvmRoute定义)。为了解决这个问题，我们将使用JvmRouteBinderValve。

JvmRouteBinderValve重写会话id，以确保下一个请求在故障转移后保持粘性(并且不会因为工作器不再可用而退回到随机节点)。阀门用相同的名称重写cookie中的JSESSIONID值。如果没有安装该阀门，在mod_jk模块出现故障时，将更难确保粘性。

请记住，如果您在server.xml中添加自己的阀门，那么默认值不再有效，请确保您添加了默认定义的所有适当的阀门。

提示：使用 sessionIdAttribute id属性，您可以更改包含旧会话id的请求属性名称。默认属性名为`org.apache.catalina.ha.session.JvmRouteOrignalSessionID`

技巧：
在将一个节点放到所有备份节点之前，您可以通过JMX启用这种mod_jk转换模式！在所有JvmRouteBinderValve备份上设置enable true，在mod_jk禁用工作器，然后删除节点并重新启动它！然后启用mod_jk工作器，并再次禁用JvmRouteBinderValves。这个用例意味着只迁移请求的会话。

### 配置案例
```
  <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
                 channelSendOptions="6">

          <Manager className="org.apache.catalina.ha.session.BackupManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"
                   mapSendOptions="6"/>
          <!--
          <Manager className="org.apache.catalina.ha.session.DeltaManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"/>
          -->
          <Channel className="org.apache.catalina.tribes.group.GroupChannel">
            <Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/>
            <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                      address="auto"
                      port="5000"
                      selectorTimeout="100"
                      maxThreads="6"/>

            <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
              <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
            </Sender>
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatchInterceptor"/>
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.ThroughputInterceptor"/>
          </Channel>

          <Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
                 filter=".*\.gif|.*\.js|.*\.jpeg|.*\.jpg|.*\.png|.*\.htm|.*\.html|.*\.css|.*\.txt"/>

          <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                    tempDir="/tmp/war-temp/"
                    deployDir="/tmp/war-deploy/"
                    watchDir="/tmp/war-listen/"
                    watchEnabled="false"/>

          <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
        </Cluster>
```
打破它！！
```
  <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
                 channelSendOptions="6">
```
这个主元素，在这个元素中可以配置所有集群细节. channelSendOptions 是一个标志，通过SimpleTcpCluster类或任何对象调用SimpleTcpCluster类的send方法来附加到每个消息上。发送标志的描述可以参考我们的javadoc[网站](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/tribes/Channel.html)。DeltaManager使用SimpleTcpCluster的send方法发送信息，backup manager 直接通过通道发送消息。

更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster.html)
```
<Manager className="org.apache.catalina.ha.session.BackupManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"
                   mapSendOptions="6"/>
          <!--
          <Manager className="org.apache.catalina.ha.session.DeltaManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"/>
          -->
```
在 <Context>元素中如果没有定义manager，将会使用这个manager配置模版。在Tomcat5.x中，每个标记为可分发的ewbapp都必须使用同一个管理器，现在情况不在这样了，因为Tomcat可以为每个web应用程序定一个管理器类，这样可以在集群中混合管理器。显然，一个节点应用程序上的管理器必须与另一个节点上同一应用程序上的同一管理器对应。如果没有为webapp指定管理器，并且webapp被标记为<distributable/>， Tomcat将采用此管理器配置并创建克隆此配置的管理器实例。

更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster-manager.html)

```
 <Channel className="org.apache.catalina.tribes.group.GroupChannel">
```

通道元素是部落，在Tomcat内部使用的群组通信框架。这个元素封装了所有与通信和成员逻辑相关的东西。

更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster-channel.html)
```
<Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/>
```
成员通过组播完成。请注意，如果您想将您的成员扩展到多播以外的点，部落也支持使用静态成员检查器的静态成员资格。地址属性是使用的多播地址，端口是多播端口。这两者共同创造了集群分离。如果您想要一个质量保证群集和一个生产群集，最简单的配置是让质量保证群集位于与生产群集不同的多播地址/端口组合上。 成员组件向其他节点广播自己的TCP地址/端口，以便节点之间的通信可以通过TCP完成。请注意，正在广播的地址是Receiver.address属性之一。

更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html)

```
   <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                      address="auto"
                      port="5000"
                      selectorTimeout="100"
                      maxThreads="6"/>
```

```
 <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
              <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
            </Sender>
```
在部落中，发送和接收数据的逻辑被分成两个功能组件。顾名思义，接收者负责接收消息。因为部落堆栈没有线程(现在其他框架也采用了这种流行的改进)，所以这个组件中有一个线程池，它有一个maxThreads和minThreads设置。地址属性是成员组件将向其他节点广播的主机地址。

更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster-receiver.html)

```
<Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
              <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
            </Sender>
```
顾名思义，发送方组件负责向其他节点发送消息。发送器有一个外壳组件，ReplicationTransmitter，但是真正完成的工作是在子组件，Transport完成的。部落支持有一个发件池，这样消息可以并行发送，如果使用NIO发件，您也可以同时发送消息。 并发意味着同时向多个发件人发送消息，并行意味着同时向多个发件人发送消息。

```
<Channel>
    <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>
    <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatchInterceptor"/>
    <Interceptor className="org.apache.catalina.tribes.group.interceptors.ThroughputInterceptor"/>
</Channel>
```
部落通过一个栈发送消息。栈中的每个元素都称为拦截器，和Tomcat servlet容器中的阀门非常相似。使用拦截器，逻辑可以被分解成更易于管理的代码片段，拦截器配置如下：

TcpFailureDetector：通过TCP验证来崩溃的成员，如果组播数据包被丢弃，这个拦截器会防止误报，即标记为崩溃的节点，即使它仍然活着并在运行。

MessageDispatchInterceptor：将消息分派到线程(线程池)以异步发送消息。

ThroughputInterceptor：输出消息流量的简单统计

请注意拦截器的顺序很重要，它们在server.xml中的定义方式是它们在通道堆栈中的表示方式。把它想象成一个链表，头部是最先拦截的，尾部是最后的。

更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster-interceptor.html)

```
<Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
    filter=".*\.gif|.*\.js|.*\.jpeg|.*\.jpg|.*\.png|.*\.htm|.*\.html|.*\.css|.*\.txt"/>
```
集群使用阀门来跟踪对web应用程序的请求，我们已经在上面提到了ReplicationValve和JvmRouteBinderValve。<Cluster》元素本身不是Tomcat管道中的一部分，而是集群将阀门添加到其父容器中。如果<Cluster>元素在<Engine>元素中配置，阀门将被添加到引擎中，依此类推。

更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster-valve.html)

```
  <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                    tempDir="/tmp/war-temp/"
                    deployDir="/tmp/war-deploy/"
                    watchDir="/tmp/war-listen/"
                    watchEnabled="false"/>
```
默认的tomcat集群支持人工部署，即集群可以在其他节点上部署和取消部署应用程序,该组件的状态目前正在变化中，但将很快得到解决。Tomcat 5.0和5.5之间的部署算法发生了变化，此时，该组件的逻辑变为部署目录必须匹配webapps目录。


更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster-deployer.html)
```
 <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
        </Cluster>
```
由于SimpleTcpCluster本身是通道对象的发送者和接收者，组件可以将自己注册为SimpleTcpCluster的监听器。ClusterSessionListener上的监听器监听增量管理器复制消息，并将增量应用于管理器，而管理器又将增量应用于会话。

更多的信息，请[参考文档](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster-listener.html)


### 集群结构
##### 组件等级：
```
 Server
           |
         Service
           |
         Engine
           |  \
           |  --- Cluster --*
           |
         Host
           |
         ------
        /      \
     Cluster    Context(1-N)
        |             \
        |             -- Manager
        |                   \
        |                   -- DeltaManager
        |                   -- BackupManager
        |
     ---------------------------
        |                       \
      Channel                    \
    ----------------------------- \
        |                          \
     Interceptor_1 ..               \
        |                            \
     Interceptor_N                    \
    -----------------------------      \
     |          |         |             \
   Receiver    Sender   Membership       \
                                         -- Valve
                                         |      \
                                         |       -- ReplicationValve
                                         |       -- JvmRouteBinderValve
                                         |
                                         -- LifecycleListener
                                         |
                                         -- ClusterListener
                                         |      \
                                         |       -- ClusterSessionListener
                                         |
                                         -- Deployer
                                                \
                                                 -- FarmWarDeployer
```


##### 如何工作的
为了便于理解集群是如何工作的，我们将带您经历一系列场景。在这个场景中，我们只计划使用两个TomcatA实例tomCata和TomcatB。我们将讲述以下一系列事件:
1. TomcatA 启动
2. TomcatB 启动（需要等待TomcatA完成启动）
3. TomcatA 接收请求，创建session S1
4. TomcatA 销毁
5. TomcatB 接收session S1的请求
6. TomcatA 启动
7. TomcatA 接收一个请求，让session S1失效
8. TomcatB 接收一个新session S2的请求
9. TomcatA 不活动season S2过期

好了，现在我们有了一个好的流程，我们将带您了解会话复制代码中到底发生了什么

1. Tomcat A启动

Tomcat使用标准顺序启动。创建主机对象时，会有一个集群对象与之关联。解析上下文时，如果分发元素在web.xml中，Tomcat会要求集群类(在本例中为SimpleTcpCluster )为复制上下文创建一个管理器。因此，在启用集群的情况下，web.xml Tomcat中的可分发集将为该上下文创建增量管理器，而不是标准管理器。群集类将启动成员服务(组播)和复制服务( tcp单播)。更多关于架构的信息，请参见本文后面的部分。

2. Tomcat B启动

当TomcatB启动时，它遵循与TomcatA相同的顺序，只有一个例外。群集已启动，并将建立一个成员身份( TomcatA、TomcatB )。TomcatB现在将从集群中已经存在的服务器请求会话状态，在本例中是TomcatA。TomcatA响应请求，在TomcatB开始监听HTTP请求之前，状态已经从TomcatA转移到TomcatB。如果TomcatA没有响应，TomcatB将在60秒后超时，并发出一个日志条目。对于在其网站中可分发的每个网站应用程序，会话状态都会被传输。注意:为了有效地使用会话复制，所有的tomcat实例都应该进行相同的配置。

3. TomcatA 接收请求，创建session S1

进入TomcatA的请求被和没有会话复制时完全相同的方式对待。该操作发生在请求完成时，复制阀将在响应返回给用户之前拦截请求。此时，它发现会话已被修改，它使用TCP将会话复制到TomcatB，一旦序列化的数据被移交给操作系统的TCP逻辑，请求将通过阀门管道返回给用户。对于每个请求，复制整个会话，这允许在不调用setAttribute或removeAttribute的情况下修改会话属性的代码被复制。useDirtyFlag配置参数可用于优化会话复制的次数。

4. TomcatA销毁

TomcatA销毁时，TomcatB会收到一个通知，表明TomcatA已经退出集群。TomcatB将TomcatA从其成员列表中删除，并且TomcatA将不再被通知在TomcatB中发生的任何更改。负载平衡器将把请求从TomcatA重定向到TomcatB，所有会话都是当前的。

5. TomcatB 接收session S1的请求

没有什么令人兴奋的，TomcatB会像处理其他请求一样处理这个请求。

6. TomcatA 启动

在启动时，在TomcatA开始接受新请求并使其可用之前，将遵循上述1 ) 2 )的启动顺序。它将加入集群，联系TomcatB了解所有会话的当前状态。一旦它接收到会话状态，它就完成加载并打开它的HTTP/mod_jk端口。因此，在从TomcatB接收到会话状态之前，不会有任何请求发送到TomcatA。

7. TomcatA 接收一个请求，让session S1失效

无效调用被拦截，会话与无效会话一起排队。当请求完成时，它不会发送已更改的会话，而是向TomcatB发送一条“过期”消息，TomcatB也会使会话无效。

8. TomcatB 接收一个新session S2的请求

与步骤3 )相同的场景

9. TomcatA 不活动season S2过期

无效调用被拦截的情况与用户使会话无效的情况相同，并且会话与无效会话一起排队。此时，无效会话将不会被复制，直到另一个请求通过系统并检查无效队列。

**成员** 集群类成员资格是使用非常简单的组播ping建立的。每个Tomcat实例将定期发送一个组播ping，在ping消息中，该实例将广泛地转换其用于复制的IP和TCP侦听端口。如果一个实例在给定的时间范围内没有收到这样的ping，则该成员被认为是死了。非常简单，非常有效！当然，您需要在您的系统上启用多播。

**TCP 复制** 一旦接收到组播ping，该成员将被添加到集群。在下一个复制请求时，发送实例将使用主机和端口信息并建立一个TCP套接字。使用这个套接字，它发送序列化数据。我选择TCP套接字的原因是因为它内置了流量控制和保证交付。所以我知道，当我发送一些数据时，它会到达那里: )

**使用框架的分布式锁定和页面** Tomcat不会在整个集群中保持会话实例同步.这种逻辑的实现会带来很大的开销，并会导致各种问题。如果您的客户端使用多个请求同时访问同一个会话，则最后一个请求将覆盖群集中的其他会话。

### 使用JMX监控你的集群
使用集群时，监控是一个非常重要的问题。一些集群对象是JMX MBeans

以下参数添加到您的启动脚本中:
```
set CATALINA_OPTS=\
-Dcom.sun.management.jmxremote \
-Dcom.sun.management.jmxremote.port=%my.jmx.port% \
-Dcom.sun.management.jmxremote.ssl=false \
-Dcom.sun.management.jmxremote.authenticate=false
```
集群Mbeans列表

![image](https://raw.githubusercontent.com/liueleven/blog/master/%E8%8B%B1%E8%AF%AD%E5%AD%A6%E4%B9%A0/tomcat/%E9%9B%86%E7%BE%A4Mbeans%E5%88%97%E8%A1%A8.png)

### FAQ
请查看[常见集群疑问解答](https://wiki.apache.org/tomcat/FAQ/Clustering)