环境:Flink1.11.1  StandAlone 

[TOC]
# bugs
## 1.java.lang.OutOfMemoryError: Direct buffer memory
desc:
```
java.lang.OutOfMemoryError: Direct buffer memory. The direct out-of-memory error has occurred. This can mean two things: either job(s) require(s) a larger size of JVM direct memory or there is a direct memory leak. The direct memory can be allocated by user code or some of its dependencies. In this case 'taskmanager.memory.task.off-heap.size' configuration option should be increased. Flink framework and its dependencies also consume the direct memory, mostly for network communication. The most of network memory is managed by Flink and should not result in out-of-memory error. In certain special cases, in particular for jobs with high parallelism, the framework may require more direct memory which is not managed by Flink. In this case 'taskmanager.memory.framework.off-heap.size' configuration option should be increased. If the error persists then there is probably a direct memory leak in user code or some of its dependencies which has to be investigated and fixed. The task executor has to be shutdown...
```
solve:
```
在flink-conf.xml增加taskmanager.memory.task.off-heap.size(默认为0)=512mb
```

## 2.No ExecutorFactory found to execute the application

solve:
添加依赖
```
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-clients_2.12</artifactId>
    <version>1.11.1</version>
</dependency>
```

## 3. Flink启动es相关程序报错
desc: 启动Flink后，执行es写入时报错，错误信息如下
```
Caused by: org.elasticsearch.xpack.monitoring.exporter.ExportException: bulk [default_local] reports failures when exporting documents
	at org.elasticsearch.xpack.monitoring.exporter.local.LocalBulk.throwExportException(LocalBulk.java:124) ~[?:?]
	... 31 more
[2020-10-16T16:47:34,024][WARN ][o.e.x.m.e.l.LocalExporter] [master] unexpected error while indexing monitoring document
org.elasticsearch.xpack.monitoring.exporter.ExportException: ClusterBlockException[blocked by: [FORBIDDEN/12/index read-only / allow delete (api)];]
	at org.elasticsearch.xpack.monitoring.exporter.local.LocalBulk.lambda$throwExportException$2(LocalBulk.java:128) ~[?:?]
	at java.util.stream.ReferencePipeline$3$1.accept(ReferencePipeline.java:193) ~[?:1.8.0_261]
	at java.util.stream.ReferencePipeline$2$1.accept(ReferencePipeline.java:175) ~[?:1.8.0_261]
	at java.util.Spliterators$ArraySpliterator.forEachRemaining(Spliterators.java:948) ~[?:1.8.0_261]
	at java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:482) ~[?:1.8.0_261]
	at java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:472) ~[?:1.8.0_261]
	at java.util.stream.ForEachOps$ForEachOp.evaluateSequential(ForEachOps.java:151) ~[?:1.8.0_261]
	at java.util.stream.ForEachOps$ForEachOp$OfRef.evaluateSequential(ForEachOps.java:174) ~[?:1.8.0_261]
	at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234) ~[?:1.8.0_261]
	at java.util.stream.ReferencePipeline.forEach(ReferencePipeline.java:418) ~[?:1.8.0_261]
	at org.elasticsearch.xpack.monitoring.exporter.local.LocalBulk.throwExportException(LocalBulk.java:129) ~[?:?]
	at org.elasticsearch.xpack.monitoring.exporter.local.LocalBulk.lambda$doFlush$0(LocalBulk.java:111) ~[?:?]
	at org.elasticsearch.action.ActionListener$1.onResponse(ActionListener.java:61) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.ContextPreservingActionListener.onResponse(ContextPreservingActionListener.java:43) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.TransportAction$1.onResponse(TransportAction.java:68) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.TransportAction$1.onResponse(TransportAction.java:64) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.bulk.TransportBulkAction$BulkRequestModifier.lambda$wrapActionListenerIfNeeded$0(TransportBulkAction.java:659) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.ActionListener$1.onResponse(ActionListener.java:61) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.bulk.TransportBulkAction$BulkOperation$1.finishHim(TransportBulkAction.java:464) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.bulk.TransportBulkAction$BulkOperation$1.onFailure(TransportBulkAction.java:459) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.TransportAction$1.onFailure(TransportAction.java:74) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.replication.TransportReplicationAction$ReroutePhase.finishAsFailed(TransportReplicationAction.java:928) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.replication.TransportReplicationAction$ReroutePhase.doRun(TransportReplicationAction.java:768) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.common.util.concurrent.AbstractRunnable.run(AbstractRunnable.java:37) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.replication.TransportReplicationAction.doExecute(TransportReplicationAction.java:170) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.replication.TransportReplicationAction.doExecute(TransportReplicationAction.java:99) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.TransportAction$RequestFilterChain.proceed(TransportAction.java:145) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.xpack.security.action.filter.SecurityActionFilter.apply(SecurityActionFilter.java:122) ~[?:?]
	at org.elasticsearch.action.support.TransportAction$RequestFilterChain.proceed(TransportAction.java:143) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.TransportAction.execute(TransportAction.java:121) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.TransportAction.execute(TransportAction.java:64) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.bulk.TransportBulkAction$BulkOperation.doRun(TransportBulkAction.java:434) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.common.util.concurrent.AbstractRunnable.run(AbstractRunnable.java:37) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.bulk.TransportBulkAction.executeBulk(TransportBulkAction.java:547) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.bulk.TransportBulkAction.doExecute(TransportBulkAction.java:253) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.bulk.TransportBulkAction.lambda$processBulkIndexIngestRequest$4(TransportBulkAction.java:601) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.ingest.IngestService$4.doRun(IngestService.java:419) [elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.common.util.concurrent.ThreadContext$ContextPreservingAbstractRunnable.doRun(ThreadContext.java:751) [elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.common.util.concurrent.AbstractRunnable.run(AbstractRunnable.java:37) [elasticsearch-7.0.0.jar:7.0.0]
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_261]
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_261]
	at java.lang.Thread.run(Thread.java:748) [?:1.8.0_261]
Caused by: org.elasticsearch.cluster.block.ClusterBlockException: blocked by: [FORBIDDEN/12/index read-only / allow delete (api)];
	at org.elasticsearch.cluster.block.ClusterBlocks.indexBlockedException(ClusterBlocks.java:208) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.replication.TransportReplicationAction.blockExceptions(TransportReplicationAction.java:253) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.replication.TransportReplicationAction.access$500(TransportReplicationAction.java:99) ~[elasticsearch-7.0.0.jar:7.0.0]
	at org.elasticsearch.action.support.replication.TransportReplicationAction$ReroutePhase.doRun(TransportReplicationAction.java:762) ~[elasticsearch-7.0.0.jar:7.0.0]
	... 19 more
```
其中核心报错信息如下：
```
ClusterBlockException[blocked by: [FORBIDDEN/12/index read-only / allow delete (api)];]
```

reason：磁盘空间不足，导致es的索引不可写

相关链接：
1. [stackoverflow](https://stackoverflow.com/questions/48032661/transporterror403-ucluster-block-exception-ublocked-by-forbidden-12-inde)
2. [链接](https://blog.csdn.net/dyr_1203/article/details/85619238)

solve：
1. 磁盘扩充
2. 修改index配置
```
PUT /<yourindex>/_settings
{
  "index.blocks.read_only_allow_delete": null
}
```
或者
```
curl -XPUT -H "Content-Type: application/json" http://127.0.0.1:9200/_all/_settings -d '{"index.blocks.read_only_allow_delete": null}'

```
