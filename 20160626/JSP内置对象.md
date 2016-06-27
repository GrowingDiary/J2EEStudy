#JSP内置对象

[TOC]

##1 输出

``` java
<%@ page contentType="text/html; charset=GBK" %>
<html>
<body>
<%
  out.print("AAA"+"<br>");
  out.print("BBB");
%>
</body>
</html>
```

##2 HTML请求

``` java
<%@ page contentType="text/html; charset=GBK" %>
<html>
<body bgcolor="#ffffff">
  <%
  	  Cookie c=new Cookie("h","Hello");
	  c.setMaxAge(10000); //非临时
	  response.addCookie(c);
  %>
<!--用户代理，使服务器能够识别客户使用的浏览器版本、操作系统、浏览器渲染引擎、浏览器语言等-->
<%=request.getHeader("User-Agent")%><br> 
  ${header["User-Agent"]}<hr>
<%=request.getHeader("Accept")%><br><!-- 客户端能处理的MIME类型 -->
  ${header.Accept }<hr>
<!-- 请求页URL,例如使用<a href="index.jsp">index.jsp</a>来请求本页 -->
<%=request.getHeader("referer")%><br>
  ${header.referer }<hr>
<%=request.getHeader("Cookie")%><br>
  ${cookie.h.value}<hr>
</body>
</html>
```

---

``` java
<%@ page contentType="text/html; charset=GBK" %>
<html>
<body bgcolor="#ffffff">
<%=request.getRemoteAddr()%><br><!-- 客户端IP -->
<%=request.getRemoteHost()%><br><!-- 客户端主机名，如无法解析则显示IP -->
<%=request.getRemotePort()%><br><!-- 客户端端口 -->
<%=request.getMethod() %><br>
<%=request.getProtocol() %>
</body>
</html>
```

##3 getParameter()

``` java
<html>
<body>
	<form action="b.jsp" method="get">
		用户名：<input type="text" name="user"/>
		密码：<input type="password" name="password"/>
		<input type="submit" value="提交">	
	</form>
</body>
</html>
```

---

``` java
<%@ page contentType="text/html; charset=GBK" %>
<html>
<body>
<%
	// 取得参数值
	out.println("用户名：" + request.getParameter("user") + "<br/>");
	out.println("密码：" + request.getParameter("password") + "<br/>");
	
	// 取得参数名
	out.print("参数名：");
	java.util.Enumeration paramerNames = request.getParameterNames();
	while(paramerNames.hasMoreElements()) {
		out.print(paramerNames.nextElement() + "&nbsp;");
	}
	out.println("<br/>");
	
	// 取得客户端请求方法
	out.println("请求方法：" + request.getMethod() + "<br/>");
	
	// 取得查询字符串
	out.println("查询字符串：" + request.getQueryString() + "<br/>");
%>
</body>
</html>
```

##4 getParameterValues()

``` java
<html>
<body>
<form action="b.jsp" method="get">
	选择喜欢的商品<br>
	<input type="checkbox" name="hobby" value="笔记" >笔记本
	<input type="checkbox" name="hobby" value="手机" >手机
	<input type="checkbox" name="hobby" value="PDA" >PDA
	<input type="checkbox" name="hobby" value="MP3" >MP3
	<input type="submit" value="提交">
</form>
</body>
</html>
```

---

``` java
<%@ page contentType="text/html; charset=GBK" %>
<html>
<body>
<%
	String[] multiValues = request.getParameterValues("hobby");
	for(int i=0;i<multiValues.length;i++)
	  out.print(new String(multiValues[i].getBytes("ISO-8859-1"))+" ");  //汉字

%>
</body>
</html>
```

##5 setAttribute()

通过\<A>，\<form>和response.sendRedirect()请求另一个页面，使用request.getAttribute()无效，pageContext.forward("a.jsp")有效。

``` java
<%@ page contentType="text/html; charset=GBK" %>
<html>
<body>
<%
	// 增加属性
	request.setAttribute("attr1","sample");
	request.setAttribute("attr2",new Integer(100));
	// 显示属性值
	out.print("属性值一：" + request.getAttribute("attr1") + "<br/>");
	out.print("属性值二：" + request.getAttribute("attr2") + "<br/>");
	// 显示属性名
	out.print("属性名：");
	java.util.Enumeration attributeNames = request.getAttributeNames();
	while(attributeNames.hasMoreElements()) {
		out.print(attributeNames.nextElement() + "&nbsp;");
	}
	out.println("<br/>");
	// 移除属性
	request.removeAttribute("attr1");
	request.removeAttribute("attr2");
%>
</body>
</html>
```
