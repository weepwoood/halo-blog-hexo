---
title: Servlet
tags:
  - Java
  - Servlet
categories:
  - [Java]
date: 2020-05-26 22:00:08
---



JavaWeb之Servlet，开发动态Web的技术


---

## Servlet简介

Servlet其实就是一个遵循Servlet开发的java类。Servlet是由服务器调用的，运行在服务器端。

开发Servlet需要实现的步骤：

+ 编写一个类实现Servlet接口
+ 把开发好的Java类部署到Web服务器中

## Tomcat下的Servlet案例

启动Tomcat，进入http://localhost:8080/examples/

可以看到Tomcat为我们提供的Examples

![Examples]( http://img.whl123456.top/image/Examples.png)

选择[Servlets examples](http://localhost:8080/examples/servlets)，就可以看到实例以及源码

选择[HelloWorld Example](http://localhost:8080/examples/servlets/helloworld.html)源码可以看到

```java
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class HelloWorld extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
    throws IOException, ServletException
    {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Hello World!</title>");
        out.println("</head>");
        out.println("<body>");
        out.println("<h1>Hello World!</h1>");
        out.println("</body>");
        out.println("</html>");
    }
}
```



## 初使用Servlet

使用Maven将Servlet有关的包

去[Maven资源库网站](https://mvnrepository.com/)寻找需要的包，搜索servlet api（[搜索结果](https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api)）

已[4.0.1为例](https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api/4.0.1)，复制Maven格式的项目依赖到pom.xml中的`<dependencies>...</dependencies>`内

```xml
    <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <!--作用域-->
      <scope>provided</scope>
    </dependency>
```

在IEDA中刷新本地项目依赖的pom.xml，便会自动下载需要的依赖

---

创建Servlet项目

![image-20200526224208126]( http://img.whl123456.top/image/image-20200526224208126.png)

在HelloServlet文件中输入

```java
public class HelloServlet extends HttpServlet {

}
```

IDEA会进行自动导包

---

重写`doGet`和`doPost`方法

```java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html");
        //响应的类型：HTML
        resp.setContentType("text/html");
        resp.setCharacterEncoding("utf-8");
        //获取响应的输出流
        PrintWriter out = resp.getWriter();  //resp.getOutputStream()
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Hello World!</title>");
        out.println("</head>");
        out.println("<body>");
        out.println("<h1>Hello World!</h1>");
        out.println("</body>");
        out.println("</html>");
    }

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }
}

```

---

去`webapp→WEB-INF→web.xml`注册Servlet，将以下内容放入`<web-app>...</web-app>`内

```xml
  <!--注册Servlet-->
  <servlet>
    <servlet-name>helloServlet</servlet-name>
    <servlet-class>top.whl123456.servlet.HelloServlet</servlet-class>
  </servlet>

  <!--设置Mapping（映射）-->
  <servlet-mapping>
    <servlet-name>helloServlet</servlet-name>
    <!--请求路径-->
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
```

---

启动Tomcat，访问web.xml中设置的映射[http://localhost:8080/hello](http://localhost:8080/hello)便可以进入到动态页面

到此的[项目源码](https://github.com/Lanqilu/JavaWeb/commit/98152c4fa0058deed28a3fd48c61e000fec65b65)已在GitHub中

## HelloServlet

1. 创建一个普通Maven项目，删除src文件夹

2. 在pom.xml文件中添加依赖

   ```xml
       <dependencies>
           <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
           <dependency>
               <groupId>javax.servlet</groupId>
               <artifactId>javax.servlet-api</artifactId>
               <version>4.0.1</version>
           </dependency>
       </dependencies>
   ```

3. 通过创建Module建立子模块

   > 在父项目中的pom.xml中自动生成`<modules>...</modules>`
   >
   > 在子项目中的pom.xml中自动生成`<parent>...</parent>`
   >
   > 父项目中的jar包子项目可以直接使用，反之不可以

4. 更换web.xml文件内容至最新内容

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                         http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0"
            metadata-complete="true">
   
   
   </web-app>
   ```

5. 在main文件夹中建立java和resources文件夹，文件结构图如下所示

   ```
   JavaWeb02
    ├── JavaWeb02.iml
    ├── pom.xml
    └── servlet01
        ├── pom.xml
        └── src
            └── main
                ├── java
                ├── resources
                └── webapp
                    ├── index.jsp
                    └── WEB-INF
                        └── web.xml
   ```

6. 创建一个简单的Servlet Demo

   ```java
   public class HelloServlet extends HttpServlet {
   }
   ```

   > Servlet有两个默认的实现类：HttpServlet，GenericServlet

---

7. 重写`doPost()`和`doGet()`方法

   > IDEA中重写方法的快捷键是Ctrl+O

   ```java
   //get和post只是请求方式的不同，业务逻辑一样，可以相互调用
   @Override
   protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       //ServletOutputStream outputStream = resp.getOutputStream();
       PrintWriter writer = resp.getWriter();//响应流
       writer.print("hello,servlet");
   
   }
   
   @Override
   protected void doPut(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       doGet(req, resp);
       }
   ```
   
8. 编写Servlet的映射，见上节配置

9. 配置Tomcat，配置项目发布路径

   > 对于artifact配置可参考，该[博客](https://www.cnblogs.com/gbb123/p/6782227.html)

## ServletContext

## HttpServletResponse

Web服务器接收到客户端的Http请求，针对这个请求，分别创建一个代表请求的 HttpServletRequest对象，代表响应的一个 HttpServletResponse

+ 获取客户端请求的参数：HttpServletRequest
+ 给客户端响应一些信息：HttpServletResponse

```java
public interface HttpServletResponse extends ServletResponse {
//...
}
```



![HttpServletResponse&ServletResponse]( http://img.whl123456.top/image/servletResponse.png)

### 分类

向浏览器发送数据的方法：

```java
public interface ServletResponse {
    public ServletOutputStream getOutputStream() throws IOException;
    public PrintWriter getWriter() throws IOException;
}
```

向浏览器发送响应头的方法

```java
public interface ServletResponse {
    public void setCharacterEncoding(String charset);
    public void setContentLength(int len);
    public void setContentLengthLong(long len);
    public void setContentType(String type);
}

```

```java
public interface HttpServletResponse extends ServletResponse {
    public void setDateHeader(String name, long date);
    public void addDateHeader(String name, long date);
    public void setHeader(String name, String value);
    public void addHeader(String name, String value);
    public void setIntHeader(String name, int value);
    public void addIntHeader(String name, int value);
}
```

状态码常量

```java
public interface HttpServletResponse extends ServletResponse {
    public static final int SC_CONTINUE = 100;
    public static final int SC_SWITCHING_PROTOCOLS = 101;
    public static final int SC_OK = 200;
    public static final int SC_CREATED = 201;
    public static final int SC_ACCEPTED = 202;
    public static final int SC_NON_AUTHORITATIVE_INFORMATION = 203;
    public static final int SC_NO_CONTENT = 204;
    public static final int SC_RESET_CONTENT = 205;
    public static final int SC_PARTIAL_CONTENT = 206;
    public static final int SC_MULTIPLE_CHOICES = 300;
    public static final int SC_MOVED_PERMANENTLY = 301;
    public static final int SC_MOVED_TEMPORARILY = 302;
    public static final int SC_FOUND = 302;
    public static final int SC_SEE_OTHER = 303;
    public static final int SC_NOT_MODIFIED = 304;
    public static final int SC_USE_PROXY = 305;
    public static final int SC_TEMPORARY_REDIRECT = 307;
    public static final int SC_BAD_REQUEST = 400;
    public static final int SC_UNAUTHORIZED = 401;
    public static final int SC_PAYMENT_REQUIRED = 402;
    public static final int SC_FORBIDDEN = 403;
    public static final int SC_NOT_FOUND = 404;
    public static final int SC_METHOD_NOT_ALLOWED = 405;
    public static final int SC_NOT_ACCEPTABLE = 406;
    public static final int SC_PROXY_AUTHENTICATION_REQUIRED = 407;
    public static final int SC_REQUEST_TIMEOUT = 408;
    public static final int SC_CONFLICT = 409;
    public static final int SC_GONE = 410;
    public static final int SC_LENGTH_REQUIRED = 411;
    public static final int SC_PRECONDITION_FAILED = 412;
    public static final int SC_REQUEST_ENTITY_TOO_LARGE = 413;
    public static final int SC_REQUEST_URI_TOO_LONG = 414;
    public static final int SC_UNSUPPORTED_MEDIA_TYPE = 415;
    public static final int SC_REQUESTED_RANGE_NOT_SATISFIABLE = 416;
    public static final int SC_EXPECTATION_FAILED = 417;
    public static final int SC_INTERNAL_SERVER_ERROR = 500;
    public static final int SC_NOT_IMPLEMENTED = 501;
    public static final int SC_BAD_GATEWAY = 502;
    public static final int SC_SERVICE_UNAVAILABLE = 503;
    public static final int SC_GATEWAY_TIMEOUT = 504;
    public static final int SC_HTTP_VERSION_NOT_SUPPORTED = 505;
}
```

### 常见应用

1. 向浏览器输出消息
2. 下载文件
   + 获取下载文件的路径
   + 下载文件的文件名
   + 浏览器支持
   + 获取下载文件的输入流
   + 创建缓冲区
   + 获取OutPutStream对象
   + 将文件OutPutStream流写入到buffer缓冲区
   + 使用OutPutStream将缓冲区中的数据输出到客户端

#### 下载文件

```java
public class fileServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //  获取下载文件的路径
        String realPath = "E:\\Desktop\\JavaWeb02\\response\\src\\main\\resources\\1.png";
        //String realPath = this.getServletContext().getRealPath(filePath);
        System.out.println("下载文件的路径："+ realPath);
        //  下载文件的文件名
        String fileName = realPath.substring(realPath.lastIndexOf("\\") + 1);
        //  浏览器支持
        resp.setHeader("Content-Disposition","attachment;filename="+ URLEncoder.encode(fileName,"utf-8"));
        //  获取下载文件的输入流
        FileInputStream fileInputStream = new FileInputStream(realPath);
        //  创建缓冲区
        int len = 0;
        byte[] buffer = new byte[1024];
        //  获取OutPutStream对象
        ServletOutputStream outputStream = resp.getOutputStream();
        //  将文件OutPutStream流写入到buffer缓冲区,使用OutPutStream将缓冲区中的数据输出到客户端
        while ((len=fileInputStream.read(buffer)) != -1){
            outputStream.write(buffer,0,len);
        }
        //  关闭流
        fileInputStream.close();
        outputStream.close();

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

#### 验证码功能

+ 前端实现，js
+ 后端实现，java图片类，生成一张图片

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //浏览器5s自动刷新
    resp.setHeader("refresh","3");

    //在内存中创建图片
    BufferedImage bufferedImage = new BufferedImage(80,20,BufferedImage.TYPE_3BYTE_BGR);
    //得到图片
    Graphics2D graphics = (Graphics2D) bufferedImage.getGraphics();//笔
    //设置图片的背景颜色
    graphics.setColor(Color.white);
    graphics.fillRect(0,0,80,20);
    //给图片写数据
    graphics.setColor(Color.BLUE);
    graphics.setFont(new Font(null, Font.BOLD, 20));
    graphics.drawString(makeNum(),0,20);

    //告诉浏览器，这个请求用浏览器打开
    resp.setContentType("image/jpeg");
    //网站缓存，不让浏览器设置缓存
    resp.setDateHeader("expires",-1);
    //缓存控制
    resp.setHeader("Cache-Control","no-cache");
    resp.setHeader("Pragma","no-cache");

    //把图片写给浏览器
    boolean write = ImageIO.write(bufferedImage,"jpg", resp.getOutputStream());
}

//生成随机数
private String makeNum(){
    Random random = new Random();
    String num = random.nextInt(9999999) + "";
    StringBuffer stringBuffer = new StringBuffer();
    for (int i = 0; i < 7 - num.length(); i++) {
        stringBuffer.append("0");
    }
    num = stringBuffer.toString() + num;
    return num;
}

```

### Response实现重定向

重定向：一个Web资源B收到客户端A的请求后，B通知客户端A访问另外一个web资源C的过程

使用场景：用户登录

```java
void sendRedirect(String var1) throws IOException;
```

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.sendRedirect("/response_war/img");
}
```

Status Code:302

Location:/response_war/img

实现原理

```java
resp.setHeader("Location","/response_war/img");
resp.setStatus(302);
```

> 重定向url会发生变化
>
> 转发url不会发生变化

```java
//处理请求
String username = req.getParameter("username");
String password = req.getParameter("password");

System.out.println("username = " + username);
System.out.println("password = " + password);

resp.sendRedirect("/response_war/success.jsp");
```

注意文件路径

## HttpServletRequest

HttpServletRequest代表客户端的请求，用户通过Http协议访问服务器，Http请求中的所有信息会被封装到HttpServletRequest，通过这个 HttpServletRequest的方法，获得客户端的所有信息

### 获取前端传递的参数

```java
String username = req.getParameter("username");
String password = req.getParameter("password");
```

### 请求转发

```java
req.getRequestDispatcher("/success.jsp").forward(req,resp);
```

## Cookie和Session

### cookie

+ 从请求中拿到 cookie信息
+ 服务器响应给客户端 cookie

```java
//服务端从客户端获取Cookie
Cookie[] cookies = req.getCookies();

//判断Cookie是否存在
if (cookies != null) {
    out.write("上次访问时间是:");
    //遍历数组
    for (int i = 0; i < cookies.length; i++) {
        Cookie cookie = cookies[i];
        //获取Cookie名字
        if(cookie.getName().equals("time")){
            //获取Cookie中的值
            long time = Long.parseLong(cookie.getValue());
            Date date = new Date(time);
            out.write(date.toLocaleString());
        }
    }
}else {
    out.write("第一次访问");
}

//服务器给客户端响应Cookie
Cookie cookie = new Cookie("time", System.currentTimeMillis()+"");

//cookie有效期
cookie.setMaxAge(24*60*60);
resp.addCookie(cookie);
```

### session

+ 一次登录多次使用，保存用户信息

+ 购物车信息
+ 网站经常使用的信息

> 与Cookie的区别
>
> + Cookie是把用户的数据写给用户的浏览器，浏览器保存（可以保存多个）
> + Session把用户的数据写到用户独占 Session中，服务器端保存（保存重要的信息，减少服务器资源的浪费
> + Session对象由服务创建；

```java
//得到Session
HttpSession session = req.getSession();

//存信息
session.setAttribute("name",new Person("Lanqilu",18));

//获取Session的ID
String id = session.getId();

boolean aNew = session.isNew();
if (aNew) {
resp.getWriter().write("session创建成功" + id);
}else {
resp.getWriter().write("session已经存在" + id);
}
```

```java
//得到Session
HttpSession session = req.getSession();

//取Session
Person person = (Person) session.getAttribute("name");
System.out.println("person.toString() = " + person.toString());
```

```java
HttpSession session = req.getSession();
session.removeAttribute("name");
//手动注销
session.invalidate();
```

Session自动过期，web.xml

```xml
<!--设置Session默认的失效时间-->
<session-config>
    <!--15分钟后自动失效-->
    <session-timeout>15</session-timeout>
</session-config>
```

