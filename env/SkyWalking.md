
# SkyWalking 安装部署

## 1.安装部署es

```shell
docker run --name elasticsearch -p 9200:9200  -p 9300:9300 --restart=always -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms84m -Xmx512m" -d elasticsearch:7.6.2
```

## 2.修改es跨域

进入容器 docker exec -it elasticsearch /bin/bash
修改配置
/usr/share/elasticsearch/config/elasticsearch.yml
配置如下
cluster.name:集群服务名字
http.cors.enabled:开启跨域
http.cors.allow-origin: 允许跨域域名，*代表所有域名
network.host: 外部访问的IP
discovery.zen.minimum_master_nodes: 最小主节点个数
重启es

```url
http://10.1.104.245:9200/
```

## 3.安装es管理页面

```shell
docker run -d --name elastic-hq -p 5000:5000 --restart always elastichq/elasticsearch-hq
http://10.1.104.245:5000/#!/clusters/elasticsearch
```

## 4.安装Skywalking

--安装oap
参数：
--link :alias ，添加到另一个容器的链接，可以添加别名或者不加
–link后面的参数和elasticsearch容器名一致;
SW_STORAGE=elasticsearch7 是固定的，使用es7;
SW_STORAGE_ES_CLUSTER_NODES:wnn_es7也可改为es服务器部署的Ip地址，比如ip:9200

```bash
docker run --name oap -d -p 1234:1234 -p 11800:11800 -p 12800:12800 --restart always --link elasticsearch:elasticsearch -e SW_STORAGE=elasticsearch7 -e SW_STORAGE_ES_CLUSTER_NODES=elasticsearch:9200 apache/skywalking-oap-server:8.5.0-es7
```

> 参数解释
--link elasticsearch:elasticsearch:存储服务使用elasticsearch
-e SW_STORAGE=elasticsearch7：存储服务elasticsearch的版本
-e SW_STORAGE_ES_CLUSTER_NODES=elasticsearch:9200:存储服务elasticsearch的链接地址

--安装Skywalking-UI，需要指定Skywalking服务名字：

```bash
docker run --name ui -d -p 18080:8080 --link oap -e SW_OAP_ADDRESS=oap:12800 --restart always apache/skywalking-ui:8.5.0
```

```url
http://10.1.104.245:18080/
```

## 5.服务集成

### 5.1 探针下载

```url
https://archive.apache.org/dist/skywalking/8.5.0/
```

添加启动参数

```bash
-javaagent:D:/tools/apache-skywalking-apm-bin-es7/agent/skywalking-agent.jar
-Dskywalking_config=D:/tools/apache-skywalking-apm-bin-es7/agent/config/agent.config
-Dskywalking.collector.backend_service=10.1.104.245:11800
-Dskywalking.agent.service_name=dqyun
```

[url](http://10.1.104.245:18080/)

### 5.2 service 模块下 的pom文件添加

```xml
<dependency>
    <groupId>org.apache.skywalking</groupId>
    <artifactId>apm-toolkit-trace</artifactId>
    <version>8.5.0</version>
</dependency>

<dependency>
    <groupId>org.apache.skywalking</groupId>
    <artifactId>apm-toolkit-logback-1.x</artifactId>
    <version>8.5.0</version>
</dependency>
```

### 5.3 Logback.xml 添加 logback.xml 放到 start下面的resource中

```xml
<!--添加-->
<appender name="grpc-log" class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.log.GRPCLogClientAppender">
    <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
        <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.mdc.TraceIdMDCPatternLogbackLayout">
            <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%X{tid}] [%thread] %-5level %logger{36} -%msg%n</Pattern>
        </layout>
    </encoder>
</appender>

<!--注意是在level = “info” 标签下增加没有的-->
<root level="info">
    <appender-ref ref="CONSOLE"/>
    <appender-ref ref="DEBUG_FILE"/>
    <appender-ref ref="INFO_FILE"/>
    <appender-ref ref="WARN_FILE"/>
    <appender-ref ref="ERROR_FILE"/>
    <appender-ref ref="grpc-log"/>
</root>
```

## 6. 文档

```url
https://skywalking.apache.org/zh/2022-04-19-how-to-use-the-java-agent-injector/
```
