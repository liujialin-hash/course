#### 不实际跳转的超链接

```jsp
--不实际跳转的超链接			鼠标点击事件
<a href="javascript:;" onclick=""></a>
```

#### script函数的定义

```jsp
<script>
        function del(){
            
        }
</script>
```

#### <jsp:useBean>

```jsp
--在jsp页面中使用JavaBean
<jsp:useBean id="name" class="包路径"，scope="生效空间">
```

#### <jsp:include>

```jsp
--把指定的文件插入正在生成的页面当中，动态包含，先处理，后包含
<jsp:include page="url">
```

#### <%@include%>

```jsp
--把指定的文件插入正在生成的页面当中，静态包含，先包含，后处理
<%@include file='url'%>
```

#### 所见即所得插件

```jsp
--只有在textarrt中生效 需要在该标签中加入class属性设置为ckeditor
<textarea id="editor1" name="editor1" rows="3" class="ckeditor"
                  required><%= (session.getAttribute("cateNewsById") != null) ? ((News) session.getAttribute("cateNewsById")).getText() : "" %></textarea>
--设置其他格式
<script>
            CKEDITOR.editorConfig=function (config){
                config.language='zh-cn';//配置语言
                config.uiColor='green';//背景颜色
                config.width='auto';//宽度
                config.height='333px'//高度
                config.skin='office2008'//皮肤
            }
</script>

```

<form>

```jsp
<form action='要跳转的地址' method='提交方式' enctype='编码格式(multipart/form-data)'>
    <table>
        <tr>
            <label for='表单控件的id'>
            </label>
        </tr>
    </table>
</form>
```

#### a标签的属性

```
a标签的属性
```

#### 获取上下文路径

```jsp
${pageContext.request.contextPath}
<%=request.getContextPath()%>
```

#### 实现倒计时的功能

```jsp
<script>
--定义初始的变量
    var countdown=2;
    --定义倒计时的函数
    function updateCountdown(){
        --判断countdown是否大于等于零
        if(countdown>=0){
            --给默认值进行减法
            countdown--;
            --获取节点并将countdown赋值给节点
            document.getElementById("countdown").innerText=countdown;
        }else{
            --跳转到需要跳转的页面
            window.location.href="url"
        }
    }
    --每隔一秒就调用一次updateCountdown函数
    setInterval(updateCountdown,1000);
</script>
<p>
    <span id="countdown">2</span>
</p>
```

#### <option>

```JSP
--选中功能
<option value="<%= newsType.getId() %>" <%= (newsType1 != null && newsType1.equals(newsType.getId())) ? "selected" : "" %>><%= newsType.getName() %>

```

#### 下拉功能

```jsp
<select id="newsType" name="newsType">
```

#### 返回功能

```jsp
onclick="javascprit:history.back()"
```

#### 指定表单为文件表单，多部件提交

```jsp
enctype="multipart/form-data"
```

#### 上传功能需要的依赖

```xml
<dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.4</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.11.0</version>
</dependency>

```

#### ServletFileUpload

```jsp
--设置请求信息实体内容的最大允许字节数
setSizeMax(long)
--解析form表单的每个字符数据，返回一个FileItem对象集合
parseRequest(HttpServletRequest)
--判断请求信息中的内容是否Multipart/form-data类型
isMultipartContent(HttpServletRequest)
--设置转换时使用的字符编码
setHeadeEncoding(String)
```

#### FileItem（表单数据）

```js
--普通字段返回true 文件表单字段返回flase
isFormField（）
--获取文件名(普通表单字段返回空)
getName()
--返回表单字段元素的name属性值
getFieldName()
--將FileItem中保存的主体保存到指定文件中
wirte()
--将FileItem保存的主体返回一个字符串
getString()
--上面的重载方法可以指定字符集
getStirng(String)
--返回文件的字节数
getSize()
```

#### 重写HttpServlet类用以输出自己想看到的日志

```Java
/*
 * Copyright (c) 2024. Lorem ipsum dolor sit amet, consectetur adipiscing elit.
 * Morbi non lorem porttitor neque feugiat blandit. Ut vitae ipsum eget quam lacinia accumsan.
 * Etiam sed turpis ac ipsum condimentum fringilla. Maecenas magna.
 * Proin dapibus sapien vel ante. Aliquam erat volutpat. Pellentesque sagittis ligula eget metus.
 * Vestibulum commodo. Ut rhoncus gravida arcu.
 */

package com.brcb.servlet;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

public class ExampleServlet extends HttpServlet {
    Logger logger = LogManager.getLogger(HttpServlet.class);

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h2>GET Request Processed</h2>");
        out.println("<p>Welcome to the Example Servlet</p>");
        out.println("<p>Request URL: " + request.getRequestURL() + "</p>");
        out.println("</body></html>");

        // Logging the information
        logger.info("GET Request Processed");
        logger.info("Welcome to the Example Servlet");
        logger.info("Request URL: " + request.getRequestURL());
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h2>POST Request Processed</h2>");
        out.println("<p>Form data:</p>");
        out.println("<ul>");

        // Iterate over form parameters and display them
        request.getParameterMap().forEach((name, values) -> {
            out.println("<li>" + name + ": " + String.join(", ", values) + "</li>");
            logger.info(name + ": " + String.join(", ", values));
        });

        out.println("</ul>");
        out.println("</body></html>");

        // Logging the information
        logger.info("POST Request Processed");
        logger.info("Form data processed");
    }
}
```

