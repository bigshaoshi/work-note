# maven部署自动更新版本号

## 1. maven plugin

### （1） spring-boot-maven-plugin 

>不用3.0.0以上的

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>2.3.2.RELEASE</version>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### （2）versions-maven-plugin

> 版本控制： version:set

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>versions-maven-plugin</artifactId>
    <version>2.7</version>
    <configuration>
        <generateBackupPoms>true</generateBackupPoms>
        <processAllModules>true</processAllModules>
    </configuration>
</plugin>
```

### （3）build-helper-maven-plugin

> 解析pom的项目版本号

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>build-helper-maven-plugin</artifactId>
    <version>3.0.0</version>
</plugin>
```

## 2. command

> 自动更新版本号：先mvn package 后 更新版本号

```bash
mvn build-helper:parse-version versions:set  -DnewVersion=\${parsedVersion.majorVersion}.\${parsedVersion.minorVersion}.\${parsedVersion.nextIncrementalVersion}-SNAPSHOT depoly
```
