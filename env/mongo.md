# mongo

> [下载地址](https://www.mongodb.com/download-center/enterprise/releases/archive)

版本号：4.0.3
机器：10.1.104.241
端口：27017

启动mongo之前需要安装：

```bash
yum install net-snmp

```

启动：

```bash
mongod --config /usr/local/mongo/mongo-4.0.3/mongodb.conf
```

停止：

```bash
mongod --config /usr/local/mongo/mongo-4.0.3/mongodb.conf --shutdown
```

> 客户端建议使用mongo自己的MongoDB compass
>