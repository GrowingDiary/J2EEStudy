## Servlet示例

```java
package myjsp;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class Servlet1 extends HttpServlet {

  public void init() throws ServletException {
  }

  public void service(HttpServletRequest request, HttpServletResponse response) throws
ServletException, IOException {

    response.setContentType("text/html;charset=gbk");
    //response.setCharacterEncoding("GBK");
    PrintWriter out = response.getWriter();
    out.println("<html>");
    out.println("<head><title>Servlet1</title></head>");
    out.println("<body bgcolor=\"#ffffff\">");
    out.println("<p>上海</p>");
    out.println("</body></html>");
  }

  public void destroy() {
  }
}
```

##全局变量

``` java
package myjsp;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class Servlet1 extends HttpServlet {
  private static final String CONTENT_TYPE = "text/html; charset=GBK";
  
  public void init() throws ServletException {
  }
  
  public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    response.setContentType(CONTENT_TYPE);
    PrintWriter out = response.getWriter();
    out.println("<html>");
    out.println("<head><title>Servlet1</title></head>");
    out.println("<body bgcolor=\"#ffffff\">");
    out.println("<p>上海</p>");
    out.println("</body></html>");
  }
  
  public void destroy() {
  }
}
```

##doGet()

``` html
<%@ page contentType="text/html; charset=GBK" %>
<%@ page language="java"%>

<html>
<body>
<form action="Servlet1" method="get">  <!--url-pattern-->
  <input type=text name=text1>
  <input type=submit>
</form>
</body>
</html>
```
``` java
package myjsp;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class Servlet1 extends HttpServlet {
  private static final String CONTENT_TYPE = "text/html; charset=GBK";

  public void init() throws ServletException {
  }

  public void doGet(HttpServletRequest request, HttpServletResponse response) throws
ServletException, IOException {
	
      String s=request.getParameter("text1");
      if(s.equals("aaa")){
      	response.sendRedirect("a.jsp");
      }else
    	response.sendRedirect("b.jsp");

     /*
     RequestDispatcher dispatcher=request.getRequestDispatcher("/a.jsp");
     dispatcher.forward(request,response);
     */ 
  }

  public void destroy() {
  }
}
```

``` html
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>

<html>
  <body>
    <%
      String s=request.getParameter("text1");
      out.print(s);
    %>
  </body>
</html>
```

##HttpSession

``` java
package myjsp;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class Servlet1 extends HttpServlet {
	private static final String CONTENT_TYPE = "text/html; charset=GBK";

	public void init() throws ServletException {
	}

	public void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		HttpSession s;
		//参数为true时，若存在会话则返回该会话，否则新建一个会话; false时，若存在会话则返回该会话，否则应该返回一个null
		s = request.getSession(true);  //若为false，第一次访问时因session不存在会抛出异常
		s.setAttribute("aaa", 100);
		Integer n = (Integer) s.getAttribute("aaa");

		response.setContentType(CONTENT_TYPE);
		PrintWriter out = response.getWriter();
		out.println("<html>");
		out.println("<head><title>Servlet1</title></head>");
		out.println("<body bgcolor=\"#ffffff\">");
		out.println("<p>" + n + "</p>");
		out.println("</body></html>");
	}

	public void destroy() {
	}
}
```

##ServletContext

``` xml
<web-app>
  <context-param>
    <param-name>a</param-name>
    <param-value>zzz</param-value>
  </context-param>
</web-app>
```

``` java
//page pageContext是页面级的对象，在Servlet中通常不使用，获得比较麻烦
package myjsp;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;
import java.util.*;

public class Servlet1 extends HttpServlet {
    private static final String CONTENT_TYPE = "text/html; charset=GBK";

    public void init() throws ServletException {
    }

    public void doGet(HttpServletRequest request, HttpServletResponse response) throws
            ServletException, IOException {
        response.setContentType(CONTENT_TYPE);
        ServletContext application=this.getServletContext();
        String s=application.getInitParameter("z");
        application.setAttribute("a", "application");
        //String a=(String)application.getAttribute("a");
        
        PrintWriter out = response.getWriter();
        out.println("<html>");
        out.println("<head><title>Servlet1</title></head>");
        out.println("<body bgcolor=\"#ffffff\">");
        out.println("<p>"+s+"</p>");
        out.println("</body>");
        out.println("</html>");
        out.close();
    }

    public void destroy() {
    }
}
```

##Cookie

``` java
package myjsp;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class Servlet1 extends HttpServlet {
  private static final String CONTENT_TYPE = "text/html; charset=GBK";

  public void init() throws ServletException {
  }

  public void doGet(HttpServletRequest request, HttpServletResponse response) throws
ServletException, IOException {
	  Cookie c=new Cookie("h","Hello");
	  c.setMaxAge(10000); //非临时
	  response.addCookie(c);
	  Cookie[] cookies=request.getCookies();
	  for(int i=0;i<cookies.length;i++){
		  PrintWriter out=response.getWriter();
		  out.print(cookies[i].getName()+"  "+cookies[i].getValue()+"<br>");
	  }
  }

  public void destroy() {
  }
}
```




