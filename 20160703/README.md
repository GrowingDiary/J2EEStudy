#20160703 JSP内置对象

* 2016-07-03 10:30:03
* <shanhui@agora.io>

##1 Request

* 相同name，多个参数时，使用```getParameterValues```，取得的字符串需要检查是否为空。

    ``` java
    String[] multiValues = request.getParameterValues("hobby");
    if (multiValues == null) {
        out.print("request hobby values is null");
        return;
    }
    ```
    
* Form提交的是value值。

    ``` html
    <input type="checkbox" name="hobby" value="笔记" >笔记本
    ```
    
    submit后收到的是==笔记==，不是==笔记本==。

* [常见请求错误](http://baike.baidu.com/view/3734067.htm)（考试会考）

    | 错误号 | 意义 |
    | :---: | --- |
    | 401 | Unauthorized |
    | 402 | Payment Required |
    | 403 | Forbidden |
    | 404 | Not Found |
    | 405 | Method NotAllowed |

##2 Response

影响和干涉响应

* 跳转，```sendRedirect()```
* 设置Http协议，```setHeader("refresh", "1")```，```setHeader("refresh", "3;URL=http://www.163.com")```

__注：__

* java内时间与日期相关类：```Date```，```Time```，```Timestamp```
* 复合属性：```3;URL=http://www.163.com```，```text/html;charset=gbk```

##3 HTTP关联(重要)

Http是无状态的，要变成有状态的，需要增加关联。

1. application关联__(不能随便使用)__

    ``` java
    application.setAttribute("a", "aaa");
    ```
    
    __生命周期：__从==服务器==开始启动到结束，系统开销很大。
    
2. session关联__(推荐使用)__

    ``` java
    session.setAttribute("b", "bbb");
    ```
    
    __生命周期：__从==浏览器==打开到关闭，系统开销较小。
    
3. request关联

    ``` java
    request.setAttribute("c", "ccc");
    ```
    
    __生命周期：__从==请求==开始到结束，系统开销最小，共享范围只有请求与被请求，条件比较苛刻。

4. page

    __生命周期：__当前页面，不能实现数据共享。


##4 PageContext

* 页面转向方法：```pageContext.forward("index.jsp")```
* ```findAttribute()```从小到大开始查找，==Page -> Request -> Session -> Application==
* ```getServletContext()```相当于Application
* ```getSession()```相当于Session

##5 exception，config，page

基本不用，不详讲解。


##6 Cookie

Cookie不是内置对象，Servlet时候再详细讲解。

