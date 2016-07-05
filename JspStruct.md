#JSP构成

JSP主要由3部分构成，分为：

1. 代码段

    ``` java
    <%
        out.print("Hello, World");
    %>
    ```
    
2. 表达式
    功能有两个：求出表达式的值，输出
    
    ``` java
    <%=request.getHeader("User-Agent")%>
    ```
    
    EL表达式
    
    ``` java
    ${header["User-Agent"]}
    ${header.refer}
    ```
    
3. 声明部分（已淘汰）

    有生命周期，讲解Servlet时补全。

    ``` java
    <%!
        int i = 0;
    %>
    ```
