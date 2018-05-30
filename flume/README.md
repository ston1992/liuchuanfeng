# Flume

Flume是Cloudera提供的一个高可用、高可靠、分布式的海量日志采集、聚合和传输的系统。  简言之，就是收集、汇聚并且移动大量日志文件的开源框架。

​	A Flume event is defined as a unit of data flow having a byte payload and an optional set of string attributes. A Flume agent is a (JVM) process that hosts the components through which events flow from an external source to the next destination (hop).@picture

​	Flume allows a user to build multi-hop flows where events travel through multiple agents before reaching the final destination. It also allows fan-in and fan-out flows, contextual routing and backup routes (fail-over) for failed hops.

	### Reliability:  

The events are staged in a channel on each agent. The events are removed from a channel only after they are stored in the channel of next agent or in the terminal repository. Flume uses a transactional approach to guarantee the reliable delivery of the events. The sources and sinks encapsulate in a transaction the storage/retrieval, respectively, of the events placed in or provided by a transaction provided by the channel. This ensures that the set of events are reliably passed from point to point in the flow. In the case of a multi-hop flow, the sink from the previous hop and the source from the next hop both have their transactions running to ensure that the data is safely stored in the channel of the next hop.

### Recoverability  

The events are staged in the channel, which manages recovery from failure. Flume supports a durable file channel which is backed by the local file system. There’s also a memory channel which simply stores the events in an in-memory queue, which is faster but any events still left in the memory channel when an agent process dies can’t be recovered.

### 操作步骤

#### 1、设置客户端（agent）

Each component (source, sink or channel) in the flow has a name, type, and set of properties that are specific to the type and instantiation. 拥有相互连接关系的component（source、sink、channel）必须在同一个agent中明确定义。

For example, an agent flows events from an Avro source called avroWeb to HDFS sink hdfs-cluster1 via a file channel called file-channel. The configuration file will contain names of these components and file-channel as a shared channel for both avroWeb source and hdfs-cluster1 sink.

#### 2、启动agent

```
$ bin/flume-ng agent -n $agent_name -c conf -f conf/flume-conf.properties.template
```



### 典型场景







各种场景及source、channel、sink具体的配置方法见：

http://flume.apache.org/FlumeUserGuide.html