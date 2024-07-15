# 1.异常记录

```
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration$EnableWebMvcConfiguration': Receiver class org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration$EnableWebMvcConfiguration does not define or inherit an implementation of the resolved method 'abstract void setServletContext(jakarta.servlet.ServletContext)' of interface org.springframework.web.context.ServletContextAware.




--处理方式
这个错误表明在 Spring Boot 3.x 中，jakarta.servlet API 和 Spring 之间存在版本兼容性问题。Spring Boot 3.x 版本已经全面迁移到了 Jakarta EE 9+，这意味着 javax.servlet 包已经迁移到了 jakarta.servlet 包。

要解决这个问题，你需要确保所有的依赖项都使用了 Jakarta EE 9+ 的包，同时更新相关依赖项以确保兼容性。以下是一些具体步骤来解决这个问题：

1. 更新依赖项以使用 Jakarta EE 9+ 版本
将 javax.servlet 相关依赖项更新为 jakarta.servlet。
```

# 2.当遇到依赖冲突时

```sh
mvn dependency:tree
```

