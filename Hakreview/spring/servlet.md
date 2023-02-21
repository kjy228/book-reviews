# Servlet

��������Ʈ�� ����Ͽ� ��Ĺ������ ����ȭ�� ���� �����ڵ� ����

## Annotation ����
### @ServletComponentScan
[����]https://www.baeldung.com/spring-servletcomponentscan

@ServletComponentScan�� ��� configuration���� ����� �� �ִµ� �� ������ ����� ���������̳ʶ����̴�. �Ӻ���� �����̳ʵ��� @WebServlet, @WebFilter, @WebListner �� �������� �ʱ� ������ ���������̳ʿ� �����ϴ� ��������Ʈ�� ���ο� ������̼��� @ServletComponentScam�� ����Ѵ�.
```java
package hello.servlet;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@ServletComponentScan // ���� �ڵ� ���
@SpringBootApplication
public class ServletApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServletApplication.class, args);
	}

}
```

����Ŭ������ `@ServletComponentScan` �� ����ϸ� application.class ������ ��Ű���鿡�� ��� ����� �� �ִ�. 

HTTP ��û�� ���� WAS�� request, response ��ü�� ���� �������� �����Ѵ�.

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("HelloServlet.service");
        System.out.println("request = " + request);
        System.out.println("response = " + response);

        String username = request.getParameter("username");
        System.out.println("username = " + username);

        response.setContentType("text/plane");
        response.setCharacterEncoding("utf-8");
        //�Ʒ� �ڵ带 ����Ͽ� response ��ü�� ���� ������ �� �ִ�.
        response.getWriter().write("hello" + username);

    }
}
```

`@WebServlet`�� ����Ͽ� HTTP��û�� ���� ���ε� URL�� ȣ��Ǹ� ���� �����̳ʴ� ` protected void service(HttpServletRequest request, HttpServletResponse response)` �޼��带 �ڵ����� ȣ���Ѵ�. 

## Servlet container ���� ���

<img width="659" alt="image" src="https://user-images.githubusercontent.com/43670838/209827848-9a6f703f-3e77-41bf-ac95-ef192a685fd4.png">

��������Ʈ�� ������ application�� �����ϸ� ������Ĺ������ ���� �����̳ʸ� ����ش�. 
�� ��, ������ WAS�� ���� request�� ������ WAS�� �ڵ����� request, response ��ü�� �����ؼ� url������ servlet�� �����ϰ� response�� value�� �����Ͽ� ������ �����ش�. 


## HttpServletRequest
HTTP��û �޽��� �Ľ��� ���ϰ� ����� �� �ֵ��� ������ ��ſ� HTTP��û �޽����� �Ľ��Ѵ�. �� ����� HttpServletRequest��ü�� ��Ƽ� �����Ѵ�. 

` �ӽ� ����� `����� �����Ѵ�. 
HTTP �޽�����û�� ���� ������ ����������� HttpServletRequest��ü�� �����Ⱓ�ε� �׵��� request.setAttribute, request.getAttribute�� ����Ͽ� ���� ���� ��ȸ �� �� �ִ�. 



## JSP
servlet�� ����ϰ� �Ǹ� �Ʒ� ������ ���� �ڹ��ڵ忡 html�� ����ؾ� �Ǵ� ������ ��Ȳ�� �߻��ϰԵȴ�. �� �����ϳĸ� html �±׸� ����ϸ鼭 ��Ÿ�� �߻��� Ȯ���� ���� �ٸ� �����ڵ��� �� �ڵ带 ���� � ȭ������ �ľ��ϱ� ����� ������ �������� ������ �������� �ȴ�. ���� �̷� �������� �ؼ��ϱ����� jsp �� ��� �Ѵ�.
![image](https://user-images.githubusercontent.com/43670838/212692640-0c2683f3-9727-4198-95aa-b0ea2308a5d8.png)

```
build.gradle ������ ����
implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
implementation 'jakarta.servlet.jsp.jstl:jakarta.servlet.jsp.jstl-api'
```

###  JSP ����
```jsp
<!--> html �±� ���� �Ʒ� �ڵ� �ʼ� </-->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<!-->java �ڵ� import </-->
<%@ page import="hello.servlet.domain.member.Member" %>
<%@ page import="hello.servlet.domain.member.MemberRepository" %>

<!-->java �ڵ� </-->

<%
    //request, response ��� ����
    MemberRepository memberRepository = MemberRepository.getInstance();

    System.out.println("MemberSaveServlet.service");
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));
%>

```
jsp�� java �ڵ带 �ۼ��ϸ� �ڵ����� servlet���� ��ȯ�Ǿ� HttpServlet��  request, response�� ����� �� �ִ�.


������ jsp�� �ҿ����ϴ�. �ڵ带 ���� java�ڵ�, �����͸� ��ȸ�ϴ� �������丮 �� �پ��� �ڵ尡 ��� JSP�� ����Ǿ��ֵ�. jsp�� �ʹ� ���� �������ϸ� �������� ���鿡���� �� ������ ũ�Ⱑ ������ Ŀ�� �� �ֵ�. �̷��� ������ �ذ��ϱ� ���ؼ� ����Ͻ� ������ ���� ó�� �ٸ���ļ� ó���ϰ�, JSP �� ������ �°� HTML�� view�� �׸��� �Ͽ� �����ϵ��� `MVC` ������ �����ߴ�.

## MVC ����
` ������ ������ ����Ŭ` UI �Ϻ� �����ϴ� �ϰ� �񽺴Ͻ� ������ �����ϴ� ���� ���� �ٸ��� �߻��� ���ɼ��� ������ ��κ� ������ ���� �����. �̷��� ������ ������ ����Ŭ�� �ٸ� �κ��� �ϳ��� �ڵ�� �����ϴ°��� ���������ϱ� ���� �ʴ�. 
` ��� Ưȭ` JSP ���� �� ���ø��� ȭ���� ������ �ϴµ� ����ȭ �Ǿ��ֱ� ������ �� �κ��� ������ ����ϴ°��� ���� ȿ�����̴�.

`Controller` HTTP ��û�� �޾Ƽ� �Ķ���͸� �����ϰ� ����Ͻ� ���� ����. �信 ������ ��� �����͸� �޾Ƽ� �𵨿� ��´�. 
`Model`: �信 ����� �����и� ��Ƶд�. �䰡 �ʿ��� �����͸� ��� �𵨿� ��Ƽ� �������ִ� ���п� ��� ����Ͻ� �����̴�. 
`View`: �𵨿� ����ִ� �����͸� ����ؼ� ȭ���� �׸��� �Ͽ� �����Ѵ�.

```java
@WebServlet(name = "mvcMemberFormServlet", urlPatterns = "/servlet-mvc/members/new-form")
public class MvcMemberFormFServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request,response);
    }
}
```
`dispatcher.forward()` :  �ٸ� �����̳� jsp�� �̵��Ҽ� �ִ� ����̸� �������ο��� ��ȣ���� �߻��Ѵ�. 
`/WEB-INF`: �� ��ξȿ� JSP�� ������ �ܺο��� ����  JSP ȣ���� �� ����. 

`redirect vs forward`
�����̷���Ʈ�� ���� Ŭ���̾�Ʈ�� ���ٺ� �����ٰ�, Ŭ���̾�Ʈ�� redirect��η� ���û�Ѵ�. ���� Ŭ�󿡼� ������ �� �ְ� url ��ε� ���� �������ȴ�. �ݸ� ������� ���� ���ο��� �Ͼ�� ȣ���̱� ������ Ŭ�� �������� ���Ѵ�.

## MVC �Ѱ�
��Ʈ�η��� �ߺ��� ���� �ǿ����� ���� �ڵ尡 ����. 

`������ �ߺ�` 
View�� �̵��ϴ� �ڵ尡 �ߺ�ȣ��ȴ�. �޼���� ����ȭ�ص� ������ �ش� �޼��嵵 �׻� ���� ȣ���ؾ��Ѵ�. 
```java
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request,response);
```

`viewPath �ߺ�`
```java
    String viewPath = "/WEB-INF/views/members.jsp";
```
viewPath ���� jsp�� �ƴ϶� thymeleaf���� �ٸ� ��� �����Ѵٸ� ��ü �ڵ带 �� �����ؾ��Ѵ�. 

����� ������ ������ ��Ʈ�ѷ����� �������� ó����� �ϴ� �κ��� ���� �� ���� ������ ���̴�. �ܼ��� ���� ����� �޼���� ������ �� �� ������, ��������� �ش� �޼��带 �׻� ȣ���ؾ� �ϰ� �Ǽ��� ȣ������ ������ �������ȴ�. ���� ȣ���ϴ°� ��ü�� �ߺ��̴�. 


## ViewResolver
