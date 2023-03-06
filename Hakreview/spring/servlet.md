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



## Adapter Pattern
- ���� �ڵ带 �����ϱ����� ����, ���� ������ ��ȯ�ϴ� �۾��� ó���ϸ� �������̽��� ó���ϰ� �������̽��� Ȱ���� �����ڵ带 ����Ѵ�. 
��ɻ� ���� �� �����ϴ� �ڵ尡 ���� �������̽� ���� ������ ����� �� ���� ��� ���� ����Ǵ� �����̴�. 
- ������ ���Ž� �ڵ峪 ���̺귯���Ѥ� ������ �� ������ �����̴�. ���� ȣȯ���� �ʴ� �������̽��� ���� �ڵ带 �����Ͽ� ���� ���α׷����� ������ �� �ֵ��� �����ش�.

## DispatcherServlet ���캸��
������ MVC�� ����Ʈ��Ʈ�ѷ� �������� �����Ǿ��ֵ�. 
������ MVC ��Ʈ�ѷ��� �ٷ� DispatcherSErvlet�̴�. 
�� DispatcherServlet�� �ٷ� MVC�� �ٽ��̴�. 

������ ��Ʈ�� dispatcherServlet�� �������� �ڵ����� ����ϸ鼭 �����`urlPatters="/"` �� ���ؼ� �����Ѵ�. 

*��û�帧*
- ������ ȣ��Ǹ� `HttpServlet` �� �����ϴ� service() �� ȣ��ȴ�. 
- ������ MVC�� dispatcherSErvlet�� �θ��� FrameworkServlet���� service()�� �������̵� �صξ���. 
- service())�� �������� `DispatcherServlet.doDispatch() �� ȣ��ȴ�. 

*���� ����*
1. �ڵ鷯 ��ȸ : �ڵ鷯 ������ ���� ��û URL�� ���ε� �ڵ鷯�� ��ȸ�Ѵ�. 
2. �ڵ鷯 ����� ��ȸ : �ڵ鷯�� ������ �� �ִ� �ڵ鷯 ����͸� ��ȸ.
3. �ڵ鷯 ����� ���� : �ڵ鷯 ����͸� �����Ѵ�.
4. �ڵ鷯 ���� : �ڵ鷯 ����Ͱ� ���� �ڵ鷯�� �����Ѵ�. 
5. Model And View ��ȯ : �ڵ鷯 ����ʹ� �ڵ鷯�� ��ȯ�ϴ� ������ ModelandView�� ��ȯ�ؼ� ��ȯ�Ѵ�. 
6. viewResolver ȣ�� : �丮������ ã�� �����Ѵ�. 
- jsp�� `InternalResourceViewReslover` �� �ڵ� ��ϵǰ� ���ȴ�. 
7. View ��ȯ : �丮������ ���� ���̸��� ���� �̸����� �ٲٰ� ������ ������ ����ϴ� �� ��ü�� ��ȯ�Ѵ�. 
8. �� ������ : �並 ���ؼ� �並 ������ �Ѵ�.
![image](https://user-images.githubusercontent.com/43670838/221402899-67df3345-5cbc-4263-b17f-723d5644dca8.png)


## @Controller ������ spring Controller


```java
package hello.servlet.web.springmvc.old;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

@Component("/springmvc/old-controller")
public class OldController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        return null;
    }
}
```
Controller implements�Ҷ� `web.springmvc.old` ��Ű�� �Ʒ����ִ� controller�� ����ؾ��Ѵ�.
�� �� `@Component` �� bean �̸��� url�� �����ؾߵȴ�.


### ��������Ʈ�� �ڵ����� ����ϴ� �ڵ鷯 ���ΰ� �ڵ鷯 �����
**HandlerMapping**
```
0 = RequestMappingHandlerMapping : ������̼� ����� ����ѷ��� @RequestMapping���� ���
1 = BeanNameUrlHandlerMapping : ������ ���� �̸����� �ڵ鷯�� ã�´�.
```

**HandelrAdapter**
```
0 = RequestMappingHandlerAdapter : ������̼� ����� ����ѷ��� @RequestMapping���� ���
1 = HttpRequestHandlerAdapter : HttpRequestHandler ó��
2 = SimpleControllerHandlerAdapter : Controller �������̽�(������̼�x, ���ſ� ���)ó��
```
�ڵ鷯 ����, �ڵ鷯 ��͵� ��� ������� ã�� ���� ������ `��������` �� �Ѿ� ����.

springboot�� �ڵ� ���������Ͽ� ���� controller�� ��� ���ٵǴ��� ������ ��Ÿ����
1. �ڵ鷯 �������� �ڵ鷯�� ��ȸ�Ѵ�. �� ��� �� �̸����� �ڵ鷯�� ã�ƾ� �ϱ� ������ �̸� �״�� �� �̸����� �ڵ鷯�� ã���ִ� `BeanNmeUrlHandlerMapping`�� ���࿡ �����ϰ� �ڵ鷯�� `OldController`�� ��ȯ�Ѵ�. 
2. �ڵ鷯 ����� ��ȸ : `HandlerAdapter`�� `supports()` �� ������� ȣ���Ͽ� SimpleCOntrollerHandlerAdapter�� ���� ����̵ȴ�. 
3. �ڵ鷯 ����� ����
������ ������ ��ȸ�� SimpleControllerHandlerAdapter�� �����ϸ鼭 �ڵ鷯 ������ �Բ� �Ѱ��ش�. 


```java
@Component("/springmvc/request-handler")
public class MyHttpRequestHandler implements HttpRequestHandler {
    @Override
    public void handleRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("MyHttpRequestHandler.handlerRequest");
    }
}
```
���� �ڵ�� application ������ localhost:8080/springmvc/request-handler ���ӽ� spring���ο��� ��� ã����?

`�ڵ鷯 �������� �ڵ鷯 ��ȸ`
1. HandlerMapping�� ���������� �����ؼ� �ڵ鷯�� ã�´�. 
2. �� ��� �� �̸����� �ڵ鷯�� ã�ƾ� �ϱ� ������ �̸� �״�� �� �̸����� �ڵ鷯�� ã���ִ� `BeanNameUrlHandlerMapping` �� ���࿡ �����ϰ� �ڵ鷯�� `MyHttpRequestHandler` �� ��ȯ�Ѵ�. 
`�ڵ鷯 ����� ��ȸ`
1. `HandlerAdapter`�� `supports()�� ������� ȣ���Ѵ�. 
2. `HttpRequestHandlerAdapter`�� `HttpRequestHandler �������̽��� �����ϹǷ� ����� �ȴ�. 

`�ڵ鷯 ����� ����`
1.������ ������ ��ȸ�� `HttpRequesthandlerAdapter�� �����ϸ鼭 �ڵ鷯 ������ �Բ� �Ѱ��ش�. 
2.`HttpRequestHandlerAdapter�� �ڵ鷯�� `MyHttpRequestHandler`�� ���ο��� �����ϰ� �װ���� ��ȯ�Ѵ�.


## ViewResolver

```java
@Component("/springmvc/old-controller")
public class OldController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        System.out.println("OldController.handlerRequest");

        return new ModelAndView("new-form");
    }
}
```

```
//appication.properties
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```
prefix�� suffix�� ����ϸ�  ModelAndView���� jsp�� ������ �� �ִ�. �� ������ ������ ��Ʈ���� �Ʒ��ڵ带 �ڵ����� �������ֱ� �����̴�.
```java
@Bean
ViewResolver internalResourceViewResolver(){
    return enw InternalResourceViewResolver("/WEB-INF/views/", ".jsp");
}
```

# SpringMVC

`@Contorller`
- �������� �ڵ����� ������ ����Ѵ�.(���ο� `@Component�� �־ ������Ʈ ��ĵ�� ����� ��)
- ������MVC���� ������̼� ��� ��Ʈ�ѷ��� �ν��Ѵ�.
- `ReuqestMappingHandlerMapping` �� ������ �� �߿��� `@RequestMapping`, `@Controller �� Ŭ���� ������ �پ��ִ� ��� ���������� �ν��Ѵ�.
