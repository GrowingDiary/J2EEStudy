#JSP内置对象

JSP一共有九个内置对象，分别为：

* [out](#out)
* [request](#request)
* [response](#response)
* [application](#application)
* [session](#session)
* [pageContext](#pageContext)
* [exception](#others)
* [page](#others)
* [config](#others)

##<a name="out"></a>out

| 作用域 | 功能 |
| --- | --- |
| page | 输出字符串，对象等 |

__示例：__

``` java
out.print("AAA"+"<br>");
out.print("BBB");
```

__注意点：__

1. 换行不能使用```\n```，而需要使用```<br>```
2. 中文乱码问题

    ``` java
    out.print(new String(request.getParameter("aaa").getBytes("ISO-8859-1"), "gbk"))
    ```

##<a name="request"></a>request

| 作用域 | 功能 |
| --- | --- |
| request | 封装了由WEB浏览器或其它客户端生成地HTTP请求的细节（参数，属性，头标和数据）|

__示例：__

1. ```getHeader()```方式

    ``` java
    // 用户代理，使服务器能够识别客户使用的浏览器版本、操作系统、浏览器渲染引擎、浏览器语言等
    request.getHeader("User-Agent")
    // 客户端能处理的MIME类型
    request.getHeader("Accept")
    // 请求页URL
    request.getHeader("referer")
    ```
    
2. 直接调用相关接口方式

    ``` java
    // 客户端IP
    request.getRemoteAddr()
    // 客户端主机名，如无法解析则显示IP
    request.getRemoteHost()
    // 客户端端口
    request.getRemotePort()
    ```
    
3. ==__获取请求参数(重点)__==

    ``` java
    // 获取name为user的参数
    request.getParameter("user")
    // 获取name为hobby的多个参数
    request.getParameterValues("hobby")
    ```
    
4. 其他

    ``` java
    // 增加属性
    request.setAttribute("attr1", "sample")
    // 显示属性
    request.getAttribute("attr1")
    // 显示属性名
    request.getAttributeNames()
    // 移除属性
    request.removeAttribute("attr1")
    ```
    
__注意点：__

1. 使用```getParameterValues()```，取得的字符串需要检查是否为空。

    ``` java
    String[] multiValues = request.getParameterValues("hobby");
    if (multiValues == null) {
        out.print("request hobby values is null");
        return;
    }
    ```
    
2. Form提交的是value值。

    ``` html
    <input type="checkbox" name="hobby" value="笔记" >笔记本
    ```
    
    submit后收到的是==笔记==，不是==笔记本==。
    
3. __==[常见请求错误](http://baike.baidu.com/view/3734067.htm)(重点)==__

    | 错误号 | 意义 |
    | :---: | --- |
    | 401 | Unauthorized |
    | 402 | Payment Required |
    | 403 | Forbidden |
    | 404 | Not Found |
    | 405 | Method NotAllowed |
    
4. 生命周期从请求开始到结束，系统开销最小，共享范围只有请求与被请求，条件比较苛刻。

##<a name="response"></a>response

| 作用域 | 功能 |
| --- | --- |
| page | 封装了返回到HTTP客户端的输出，向页面作者提供设置响应头标和状态码的方式。经常用来设置HTTP标题，添加cookie，设置响应内容的类型和状态，发送HTTP重定向和编码URL。主要影响和干涉响应 |

__示例：__

1. 跳转

    ``` java
    if (request.getParameter("aaa").equals("zzz")) {
        response.sendRedirect("a.jsp");
    }
    ```
2. 设置HTTP协议
    
    ``` java
    //设置刷新页面的时间，第隔1秒钟刷新一次
    response.setHeader("refresh", "1");
    
    // 3秒后自动转到www.163.com
    response.setHeader("Refresh", "3;URL=http://www.163.com");
    ```

##<a name="application"></a>application

| 作用域 | 功能 |
| --- | --- |
| application(服务器程序运行期） | 提供了关于服务器版本，应用级初始化参数和应用内资源绝对路径，注册信息的方式等 |

__示例：__

``` java
// 设置属性
application.setAttribute("a", "aaa")

// 获取属性
application.getAttribute("a")
```

__注意点：__

1. 由于生命周期太长，__不能随便使用__
2. 系统开销很大

##<a name="session"></a>session

| 作用域 | 功能 |
| --- | --- |
| session | 是一个类似哈希表的与单一WEB浏览器会话相关的对象，它存在于HTTP请求之间，可以存储任何类型的命名对象，主要用于跟踪对话 |

__示例：__

``` java
// 设置属性
session.setAttribute("b", "bbb")

// 获取属性
session.getAttribute("b")
```

__注意点：__

1. 生命周期从浏览器打开到关闭，系统开销较小
2. __推荐使用__

##<a name="pageContext"></a>pageContext

| 作用域 | 功能 |
| --- | --- |
| page | 提供所有四个作用域层次的属性查询和修改能力，也提供了转发请求到其它资源和包含其他资源的方法 |

__示例：__

1. 页面转向

    ``` java
    pageContext.forward("index.jsp")
    ```
    
2. 查找属性

    ``` java
    pageContext.findAttribute("a")
    ```
    
    从小到大查找，依次为__Page -> Request -> Session -> Application__
    
3. 设置属性

    ``` java
    pageContext.setAttribute("a", "aaa", PageContext.PAGE_SCOPE);
    pageContext.setAttribute("b", "bbb", PageContext.REQUEST_SCOPE);
    pageContext.setAttribute("c", "ccc", PageContext.SESSION_SCOPE);
    pageContext.setAttribute("d", "ddd", PageContext.APPLICATION_SCOPE);
    ```
    
4. 获取属性

    ``` java
    // PAGE_SCOPE可以省略
    pageContext.getAttribute("a", PageContext.PAGE_SCOPE);
    pageContext.getAttribute("a");
    
    pageContext.getAttribute("b", PageContext.REQUEST_SCOPE);
    pageContext.getAttribute("c", PageContext.SESSION_SCOPE);
    pageContext.getAttribute("d", PageContext.APPLICATION_SCOPE);
    ```

5. ```getServletContext()```相当于Application

    ``` java
    // application.setAttribute("a", "aaa");
    pageContext.getServletContext().setAttribute("a", "aaa");
    ```
    
6. ```getSession()```相当于Session

    ``` java
    // session.setAttribute("b", "bbb");
    pageContext.getSession().setAttribute("b", "bbb");
    ```

##<a name="others"></a>others

__exception，config，page__ 基本不用，不详讲解。
