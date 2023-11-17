# maven && idea deploy 上传jar到私库

## 1. 上传maven仓库

### 1.1 Nexus的登录地址和账号

> http://10.1.108.10:8888/
> 
> admin  /  401cc3d3-10f9-40f3-a761-f79b4b32f9d2

### 1.2 settings.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.2.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.2.0 https://maven.apache.org/xsd/settings-1.2.0.xsd">
  <localRepository>/home/shijie/.m2/repository</localRepository>
 <pluginGroups>
  </pluginGroups>
  <proxies>
  </proxies>
  <servers>
    <server>
      <id>rep2</id>
      <username>admin</username>
      <password>401cc3d3-10f9-40f3-a761-f79b4b32f9d2</password>
    </server>
    <server>
      <id>rep2-snapshot</id>
      <username>admin</username>
      <password>401cc3d3-10f9-40f3-a761-f79b4b32f9d2</password>
    </server>
  </servers>
  <mirrors>
    
   <mirror>
     <id>nexus</id>
     <mirrorOf>central</mirrorOf>
     <url>http://10.1.108.10:8888/repository/maven-public/</url>
     </mirror>
    <mirror>
     <id>nexus-snapshot</id>
     <mirrorOf>maven-snapshots</mirrorOf>
     <url>http://10.1.108.10:8888/repository/maven-snapshots/</url>
     </mirror>
   
   <mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>repo1</mirrorOf>
    <name>Nexus aliyun</name>
    <url>https://maven.aliyun.com/repository/public</url>
  </mirror>  
  </mirrors>
  <distributionManagement>
        <repository>
            <id>repo2</id>
            <url>http://10.1.108.10:8888/repository/maven-release/</url>
        </repository>
        <snapshotRepository>
            <id>repo2-snap</id>
            <url>http://10.1.108.10:8888/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
  <profiles>
  </profiles>
</settings>
```

### 1.3 项目中pom.xml配置

```xml
<distributionManagement>
    <!--发布版本仓库-->
    <repository>
        <id>rep2</id>
        <name>libs-releases</name>
        <url>http://10.1.108.10:8888/repository/maven-releases</url>
        <uniqueVersion>false</uniqueVersion>
        <layout>default</layout>
    </repository>
</distributionManagement>
```
