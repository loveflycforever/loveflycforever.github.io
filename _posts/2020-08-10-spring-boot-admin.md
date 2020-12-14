---
layout: post
title:  "Spring Boot Admin 实现简单的服务监控"
subtitle: ""
tags: [Spring Boot]
bigimg: https://spring.io/images/spring-logo-9146a4d3298760c2e7e49595184e1975.svg
comments: true
---

# 服务端配置

## Maven 依赖

```xml
<project>
    <properties>
        <springboot.version>2.1.15.RELEASE</springboot.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>${springboot.version}</version>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-server</artifactId>
            <version>2.1.6</version>
        </dependency>
    </dependencies>
</project>
```

## 启动类

```java
import de.codecentric.boot.admin.server.config.EnableAdminServer;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * 启动类
 */
@SpringBootApplication
@EnableAdminServer
public class ServerAdminApplication {

    public static void main(String[] args) {
        SpringApplication.run(ServerAdminApplication.class, args);
    }

}
```

## 配置文件

```properties
spring.application.name=server-admin
server.servlet.context-path=/server_admin
server.port=9090
```

# 客户端配置

## Maven 依赖

```xml
<project>
    <properties>
        <springboot.version>2.1.15.RELEASE</springboot.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>${springboot.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
            <version>${springboot.version}</version>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-client</artifactId>
            <version>2.1.6</version>
        </dependency>
    </dependencies>
</project>
```

## 配置文件

```properties
spring.application.name=client-admin
server.servlet.context-path=/client_admin
server.port=8080
spring.boot.admin.client.url=http://127.0.0.1:9090/server_admin
management.endpoints.web.exposure.include=*
management.endpoint.health.show-details=ALWAYS
```

