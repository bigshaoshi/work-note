# rocketmq

测试环境控制台：
http://10.1.104.244:8090/#/

## 部署

Docker 拉取指定的镜像后，启动命令：

```bash
 # 启动namesrv
docker run -d --restart=always --name rmqnamesrv --privileged=true -p 9876:9876  -v /home/rocketmq/nameserver/logs:/root/logs -v /home/rocketmq/nameserver/store:/root/store -e "MAX_POSSIBLE_HEAP=100000000" apache/rocketmq:4.9.0 sh mqnamesrv
# 启动broker
docker run -d --restart=always --name rmqbroker --link rmqnamesrv:namesrv -p 10911:10911 -p 10909:10909 --privileged=true -v /home/rocketmq/data/broker/logs:/root/logs -v /home/rocketmq/data/broker/store:/root/store -v /home/rocketmq/broker.conf:/home/rocketmq/broker.conf -e "NAMESRV_ADDR=namesrv:9876" -e "MAX_POSSIBLE_HEAP=200000000" apache/rocketmq:4.9.0 sh mqbroker -c /home/rocketmq/broker.conf
#启动控制台
docker run -d --restart=always --name rmqadmin -e "JAVA_OPTS=-Drocketmq.namesrv.addr=10.1.104.244:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 8090:8080 pangliang/rocketmq-console-ng
```

目前开发环境和测试环境共用一个mq。为了区分环境，不同环境的项目请用topic区分。
topic命名：${业务topic_value}_${profile}
例如：api_resource_dev \ api_resource_test

## 配置信息

***一定不要让这三个配置同时存在！！***

```bash
rocketmq.name-server=127.0.0.1:9876
rocketmq.consumer.group=test-group
rocketmq.consumer.topic=test-topic
```

>不然spring自己会启动一个默认的pull - consumer，导致自己的无法消费所有的broker-queue,导致消息丢失。

> pom配置

```bash
rocketmq:
  name-server: 10.1.104.244:9876
  consumer:
    # topic: api_resource #自定义
    group: api  # 自定义
  producer:
    group: api  # 自定义
customer-consumer-topic-key: topic-value
```

> Maven:

```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-spring-boot-starter</artifactId>
    <version>2.2.2</version>
</dependency>
```

## 各业务mq信息统计

| 业务名  | consumer组件 | producer组件 | consumer  | producer | 参数 | 对接人|
| ------| ----------| -------- | ------- | -------- | ------- | ---------- |
| 更新用户接口调用次数 | api   |    | topic: api_resource group: api |     | {"userId":54,"productId":1,"apiDayCount":2,"apiMaxQps":1,"expireTime":"2023-04-24T12:00:00"} | 消费者：刘乐 生产者：檀皓 |
| 同步产品信息    | api | srv-order  | Topic: product-update  |  |   |     |
