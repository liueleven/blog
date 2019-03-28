# 如何配置集群Session复制
[原文](http://tomcat.apache.org/tomcat-9.0-doc/config/cluster.html)

### 目录
- 简介
- 安全
- 引擎 vs 主机 放置对比
- 上下文属性复制
- 嵌套组件
- 已弃用的配置选项
- 属性
    - 简单的集群属性

### 简介
Tomcat集群提供了session复制，上下文属性复制和集群范围内的WAR文件部署等实现。虽然集群配置相当复杂，但是默认的配置对大多数人来说足以完成工作。

Tomcat对集群的实现是非常易可扩展的，因此我们开放了无数的选项。使得配置看起来非常的多，但是不要失去信心。相反，你对接下来要做的失去拥有很都的控制权。

### 安全
集群的实现是基于安全，可信的网络用于所有与集群相关网络而编写的。在不安全，不可信的网络上运行集群是不安全的。

有许多选项可以为Tomcat集群提供安全可信的网络，其中包括：
- 专用局域网
- 虚拟专用网络
- [IPSEC](https://baike.baidu.com/item/ipsec/2472311?fr=aladdin)（一种安全加密协议）

### 引擎 vs 主机 放置对比
你可以将<Cluster>元素放在<Engine>容器中或者<Host>容器中。放置在引擎中意味这你将支持集群中所有Tomcat虚拟主机，并且组件间共享消息。当你把<Cluster>放在<Engine>元素中，集群会将每个会话管理器的主机名附加到管理器名称中，这样，两个具有相同名称但位于两个不同主机中的上下文将是可区分的。


### 上下文属性复制
配置上下文属性复制，只需交换应用程序上下文所使用的上下文实现即可。
```
<Context className="org.apache.catalina.ha.context.ReplicatedContext"/>
```
这个上下文扩展了Tomcat标准上下文( StandardContext)，所以基本实现中的所有选项都是有效的。

### 嵌套组件

##### 管理器
这个会话管理器元素可以识别当前集群使用了哪种会话管理器。这个管理器配置与你在常规<Context>中一样配置。`org.apache.catalina.ha.session.DeltaManager `是默认值，它与SimpleTcpCluster的实现紧密相关。其它管理器，例如`org.apache.catalina.ha.session.BackupManager`是松耦合的，不需要依赖SimpleTcpCluster进行数据复制。

##### 通道
通道及其子组件都是集群组的输入输出层的一部分，并且是我们自己的一个名为“部落”的模块 网络层、消息传递和成员逻辑的任何配置和调整都将在通道及其嵌套组件中完成。你总能找到更多关于Apache部落的信息

##### 阀门
Tomcat 集群实现使用Tomcat阀门来跟踪请求合适你如和退出Servlet容器，他使用阀门能够智能做出决策何时复制数据，总是在请求的末尾进行的。

##### 部署器
这个部署器是Tomcat场部署器，它允许你在集群范围内部署和取消部署应用程序。如果您希望跟踪消息，您可以在此添加一个侦听器，或者您可以向通道对象添加一个阀门。

### 已弃用的配置项
启用设置：在Tomcat早起的版本中，你可以使用管理器来控制会话管理器<property>=value，这个已经停止使用了，这种写入的方式会干扰一个集群下能够支持多个不同的管理器类，相同的属性可能对不同的管理器产生不同的影响。

### 属性

##### SimpleTcpCluster 属性


className 
```
主集群类，目前只有一个可用，org.apache.catalina.ha.tcp.SimpleTcpCluster
```
channelSendOptions
```
部落通道发送选项，默认是8。这个选项用于设置所有通过SimpleTcpCluster发送
的消息使用的flag，这个flag决定消息的发送方式，是一个简单的逻辑或   

int options = Channel.SEND_OPTIONS_ASYNCHRONOUS |
              Channel.SEND_OPTIONS_SYNCHRONIZED_ACK |
              Channel.SEND_OPTIONS_USE_ACK;
一些值如下：
Channel.SEND_OPTIONS_SYNCHRONIZED_ACK = 0x0004
Channel.SEND_OPTIONS_ASYNCHRONOUS = 0x0008
Channel.SEND_OPTIONS_USE_ACK = 0x0002

因此要使用ACK和ASYNC消息，这个flag应该是10(8+2)。

注意如果你使用ASYNC消息，接收节点可能会以不同于发送顺序的顺序处理会话的更新消息。
```
channelStartOptions
```
为集群使用的<Channel>对象设置开始和停止标志。默认是Channel.DEFAULT表示启
动所有通道服务，例如发送方，接收方，成员发送方和成员接收方，有以下flag可用：
Channel.DEFAULT = Channel.SND_RX_SEQ (1) |
                  Channel.SND_TX_SEQ (2) |
                  Channel.MBR_RX_SEQ (4) |
                  Channel.MBR_TX_SEQ (8);
```
heartbeatBackgroundEnabled
```
标记是否在容器后台线程调用通道心跳。默认值为假。启用此标志不要忘记禁用通道心跳线程。
```
notifyLifecycleListenerOnFailure
```
如果所有集群侦听器都无法接受通道消息，则标记是否通知生命周期侦听器。默认值为假。
```