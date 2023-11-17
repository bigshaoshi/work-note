# SpringBoot2.7项目打包镜像方案 

> [spring官方文档](https://docs.spring.io/spring-boot/docs/2.7.7/reference/pdf/spring-boot-reference.pdf#page=427&zoom=100,0,686)

文件中的几行代码就可以将Spring Boot fat jar转换为docker映像，我们将使用分层特性来创建一个优化的docker镜像。当你创建Jar包含了layers索引文件，spring-boot-jarmode-layertools Jar将被添加为依赖于您的jar。有了这个jar放在类路径上，您就可以以特殊的方式启动应用程序模式，它允许引导代码运行与应用程序完全不同的东西，
例如，提取层的东西。
> layertools模式不能与完全可执行的Spring Boot一起使用包含启动脚本的归档文件。禁用启动脚本配置构建一个用于layertools的jar文件

下面是使用layertools jar模式启动jar的方法:

```bash
java -Djarmode=layertools -jar my-app.jar
```

这将提供以下输出:

```shell
Usage:   
java -Djarmode=layertools -jar my-app.jar 
Available commands:   
    list List layers from the jar that can be extracted   
    extract Extracts layers from the jar for image creation   
    help Help about any command 
```

可以使用extract命令轻松地将应用程序拆分为多个层，以便添加到dockerfile。下面是一个使用jarmode的Dockerfile示例。

- Dockerfile example:

```Dockerfile
FROM centos:7 as builder 
WORKDIR application 
ARG JAR_FILE=target/*.jar 
COPY ${JAR_FILE} application.jar 
RUN java -Djarmode=layertools -jar application.jar extract

FROM centos:7 
WORKDIR application 
COPY --from=builder application/dependencies/ ./ 
COPY --from=builder application/spring-boot-loader/ ./ 
COPY --from=builder application/snapshot-dependencies/ ./ 
COPY --from=builder application/application/ ./ 
ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"] 
```

假设上面的Dockerfile在当前目录中，可以用它来构建docker映像，或者选择指定应用程序jar的路径，如下所示
例子:
```bash
docker build --build-arg JAR_FILE=path/to/myapp.jar .
```

这是一个多级dockerfile。构建器阶段提取以后需要的目录。每一个COPY命令的一部分与jarmode提取的层有关。
当然，不使用jarmode也可以编写Dockerfile。你可以使用一些组合Unzip和mv将内容移动到正确的层，但jarmode简化了这一点。

## Maven + docker
> [SpringBoot 通过maven插件实现docker镜像打包](https://juejin.cn/post/7155277691850555429)

```bash
mvn build-helper:parse-version versions:set  -DnewVersion=\${parsedVersion.majorVersion}.\${parsedVersion.minorVersion
}.\${parsedVersion.nextIncrementalVersion}-SNAPSHOT package

git add .

git push

export MAVEN_HOME="/var/jenkins_home/maven/apache-maven-3.9.1"
export JAVA_HOME="/root/jdk1.8.0_361/"
export PATH=$JAVA_HOME/bin:$MAVEN_HOME/bin:$PATH
export DOCKER_HOST=tcp://10.1.104.246:2375
java -version
mvn -v
pwd
mvn build-helper:parse-version versions:set  -DnewVersion=\${parsedVersion.majorVersion}.\${parsedVersion.minorVersion
}.\${parsedVersion.nextIncrementalVersion}-SNAPSHOT package
git add .
git commit -m "update: 升级pom版本号"
git push
```