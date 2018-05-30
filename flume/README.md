# Flume

Flume是Cloudera提供的一个高可用、高可靠、分布式的海量日志采集、聚合和传输的系统。  简言之，就是收集、汇聚并且移动大量日志文件的开源框架。

​	A Flume event is defined as a unit of data flow having a byte payload and an optional set of string attributes. A Flume agent is a (JVM) process that hosts the components through which events flow from an external source to the next destination (hop).
![flume-agent](https://github.com/ston1992/liuchuanfeng/blob/master/flume/pcture/flume-agent.png)

​	Flume allows a user to build multi-hop flows where events travel through multiple agents before reaching the final destination. It also allows fan-in and fan-out flows, contextual routing and backup routes (fail-over) for failed hops.

###Reliability:  

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



## Component Summary

| Component Interface                      | Type Alias          | Implementation Class                     |
| ---------------------------------------- | ------------------- | ---------------------------------------- |
| org.apache.flume.Channel                 | memory              | org.apache.flume.channel.MemoryChannel   |
| org.apache.flume.Channel                 | jdbc                | org.apache.flume.channel.jdbc.JdbcChannel |
| org.apache.flume.Channel                 | file                | org.apache.flume.channel.file.FileChannel |
| org.apache.flume.Channel                 | –                   | org.apache.flume.channel.PseudoTxnMemoryChannel |
| org.apache.flume.Channel                 | –                   | org.example.MyChannel                    |
| org.apache.flume.Source                  | avro                | org.apache.flume.source.AvroSource       |
| org.apache.flume.Source                  | netcat              | org.apache.flume.source.NetcatSource     |
| org.apache.flume.Source                  | seq                 | org.apache.flume.source.SequenceGeneratorSource |
| org.apache.flume.Source                  | exec                | org.apache.flume.source.ExecSource       |
| org.apache.flume.Source                  | syslogtcp           | org.apache.flume.source.SyslogTcpSource  |
| org.apache.flume.Source                  | multiport_syslogtcp | org.apache.flume.source.MultiportSyslogTCPSource |
| org.apache.flume.Source                  | syslogudp           | org.apache.flume.source.SyslogUDPSource  |
| org.apache.flume.Source                  | spooldir            | org.apache.flume.source.SpoolDirectorySource |
| org.apache.flume.Source                  | http                | org.apache.flume.source.http.HTTPSource  |
| org.apache.flume.Source                  | thrift              | org.apache.flume.source.ThriftSource     |
| org.apache.flume.Source                  | jms                 | org.apache.flume.source.jms.JMSSource    |
| org.apache.flume.Source                  | –                   | org.apache.flume.source.avroLegacy.AvroLegacySource |
| org.apache.flume.Source                  | –                   | org.apache.flume.source.thriftLegacy.ThriftLegacySource |
| org.apache.flume.Source                  | –                   | org.example.MySource                     |
| org.apache.flume.Sink                    | null                | org.apache.flume.sink.NullSink           |
| org.apache.flume.Sink                    | logger              | org.apache.flume.sink.LoggerSink         |
| org.apache.flume.Sink                    | avro                | org.apache.flume.sink.AvroSink           |
| org.apache.flume.Sink                    | hdfs                | org.apache.flume.sink.hdfs.HDFSEventSink |
| org.apache.flume.Sink                    | hbase               | org.apache.flume.sink.hbase.HBaseSink    |
| org.apache.flume.Sink                    | asynchbase          | org.apache.flume.sink.hbase.AsyncHBaseSink |
| org.apache.flume.Sink                    | elasticsearch       | org.apache.flume.sink.elasticsearch.ElasticSearchSink |
| org.apache.flume.Sink                    | file_roll           | org.apache.flume.sink.RollingFileSink    |
| org.apache.flume.Sink                    | irc                 | org.apache.flume.sink.irc.IRCSink        |
| org.apache.flume.Sink                    | thrift              | org.apache.flume.sink.ThriftSink         |
| org.apache.flume.Sink                    | –                   | org.example.MySink                       |
| org.apache.flume.ChannelSelector         | replicating         | org.apache.flume.channel.ReplicatingChannelSelector |
| org.apache.flume.ChannelSelector         | multiplexing        | org.apache.flume.channel.MultiplexingChannelSelector |
| org.apache.flume.ChannelSelector         | –                   | org.example.MyChannelSelector            |
| org.apache.flume.SinkProcessor           | default             | org.apache.flume.sink.DefaultSinkProcessor |
| org.apache.flume.SinkProcessor           | failover            | org.apache.flume.sink.FailoverSinkProcessor |
| org.apache.flume.SinkProcessor           | load_balance        | org.apache.flume.sink.LoadBalancingSinkProcessor |
| org.apache.flume.SinkProcessor           | –                   |                                          |
| org.apache.flume.interceptor.Interceptor | timestamp           | org.apache.flume.interceptor.TimestampInterceptor$Builder |
| org.apache.flume.interceptor.Interceptor | host                | org.apache.flume.interceptor.HostInterceptor$Builder |
| org.apache.flume.interceptor.Interceptor | static              | org.apache.flume.interceptor.StaticInterceptor$Builder |
| org.apache.flume.interceptor.Interceptor | regex_filter        | org.apache.flume.interceptor.RegexFilteringInterceptor$Builder |
| org.apache.flume.interceptor.Interceptor | regex_extractor     | org.apache.flume.interceptor.RegexFilteringInterceptor$Builder |
| org.apache.flume.channel.file.encryption.KeyProvider$Builder | jceksfile           | org.apache.flume.channel.file.encryption.JCEFileKeyProvider |
| org.apache.flume.channel.file.encryption.KeyProvider$Builder | –                   | org.example.MyKeyProvider                |
| org.apache.flume.channel.file.encryption.CipherProvider | aesctrnopadding     | org.apache.flume.channel.file.encryption.AESCTRNoPaddingProvider |
| org.apache.flume.channel.file.encryption.CipherProvider | –                   | org.example.MyCipherProvider             |
| org.apache.flume.serialization.EventSerializer$Builder | text                | org.apache.flume.serialization.BodyTextEventSerializer$Builder |
| org.apache.flume.serialization.EventSerializer$Builder | avro_event          | org.apache.flume.serialization.FlumeEventAvroEventSerializer$Builder |
| org.apache.flume.serialization.EventSerializer$Builder | –                   | org.example.MyEventSerializer$Builder    |
