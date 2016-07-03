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

* 常见请求错误（考试会考）

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





