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
- service()�� �������� `DispatcherServlet.doDispatch() �� ȣ��ȴ�. 

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
- `ReuqestMappingHandlerMapping` �� ������ �� �߿��� `@RequestMapping`, `@Controller` �� Ŭ���� ������ �پ��ִ� ��� ���������� �ν��Ѵ�.

## RequestMapping Ư¡
```java
@RequestMapping(value = "/new-form")
    public String newForm() {
        return "new-form";
    }
```
���� api�� postman�� ����Ͽ� ��û�� ������ Get, Post, delete ��� http Request����� �����ϴ�. ���� �̷������� �����ϱ� ���ؼ� �Ʒ� �ڵ�� �����ϸ�ȴ�. 

```java
@RequestMapping(value = "/new-form", method = RequestMethod.GET)
    public String newForm() {
        return "new-form";
    }
```

## @Controller�� @RestController �� ����

@Controller
- �⺻������ view �̸��� ��ȯ�ϱ����� ����Ѵ�.
- view�� �������ϱ����� viewResolver�� ���Ǹ� �������°� view�� ã�� �������Ѵ�.

@Restcontroller
- dataȤ�� json���� return�� �� �ֵ��� �Ѵ�.

---

## Log ���� 
- Log level : trace - debug - info - warn -error
- ���߼��� : debug���
- � ���� : info ��� ( info, warn, error ������ log ��µ�)

## Log ��½� ���� ����
```java
log.info("info long= {}", name); // �̷��� ���ߵ�
log.info("info long= "+ name); // �̷��� X
```
�� ������ `log.info("info long= "+ name)` �� ����ϸ� ������ ���������� ��Ʈ���� ���ϴ� ����(infolong=name)�� �Ͽ� �޸𸮸� ��Ƹ԰� �ȴ�. ��, ������� ���ҽ��� ����ϰ� �ȴ�. 

-----
## QueryParam vs PathVariable ����
@QueryParam
- �� ������̼��� ����ϸ� url ��ο� ? �� �ٴ´�

@PathVariable
```java

```
���ڵ�� url �󿡼� `http://localhost:8080/mapping/1` �� ���� ���δ�. ���� ������ ������ ����Ͽ� heaer�� ��θ� ���� �Ʒ� ������ ���� ���δ�. 
![image](https://user-images.githubusercontent.com/43670838/225639281-e46bbf22-d308-415c-99b4-f7456428b597.png)


## Http Request


### @ResponseBody�� ���ؼ�
```java
@Slf4j
@Controller
public class RequestParamController {

    @ResponseBody
    @RequestMapping("/request-param-v2")
    public String requestParamV2(
            @RequestParam("username") String username,
            @RequestParam("age") Integer age) {

        log.info("username={}, age={} ", username, age);
        return "ok";
    }
}

```
controller�� ���� ���� �ڵ�� �ۼ��ϸ� ù��?�� @Controller ������̼��� ����߱� ������
"ok"�� �����ϸ� viewname�� �����ϰ� �ȴ�. 
������ `@ResponseBody`�� ����ϸ� String���� ������ �� �ִ�.

### @RequestParam

<b>Ư¡</b>
- �������� ������ `@RequestParam("username") String username` -> `@RequestParam String username` ���� ���� �� �� �ִ�. 
- ��ü���ƴ� �⺻Ÿ��(String, int�� )�� ������ `@RequestParam String username` -> `String username`  ���� ������ �� �ִ�.
- primitive type �� null �Է� �ȵ�. `@RequestParam(required=false) int age`�� ��������� int�� �ƴ϶� Integer�� ����ؾ� null ���� ���� �� �ִ�. �ƴϸ� `defaultValue` �ɼ��� ����ϸ� �ȴ�.


## @RequestBody
```java
package hello.springmvc.basic.request;

import jakarta.servlet.ServletInputStream;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.util.StreamUtils;
import org.springframework.web.bind.annotation.PostMapping;

import java.io.IOException;
import java.nio.charset.StandardCharsets;

@Slf4j
@Controller
public class RequestBodystringController {
    @PostMapping("/request-body-string-v1")
    public void requestBodyString(HttpServletRequest request, HttpServletResponse response)throws IOException{
        ServletInputStream inputStream = request.getInputStream();
        StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
    }
}

```

Stream�� �׻� byte �ڵ��̱⶧���� ���ڿ��� ��ȯ�ϱ� ���ؼ��� � `��`���� ��ȯ���� �����ؾߵȴ�.


## HttpEntity
Http heaer , body������ ���ϰ� ��ȸ�� �� �ִ�. 
�޼��� �ٵ� ������ ���� ��ȸ
��û �Ķ���͸� ��ȸ�ϴ� ��ɰ� ���� ����(@RequestParam, @ModelAttribute)
- httpentity�� ���信���� ��밡���ϴ�. 
- �޼��� �ٵ� ���� ���� ��ȯ �� ������� ���� ���������� `view ��ȸ�� �ȵ�`

HttpEntity�� ��ӹ��� ���� ��ü�鵵 ���� ����� �����Ѵ�. 
`ReqeustEntity` : HttpMethod, url ���� �߰�, ��û���� ���
`ResponseEntity`: http�����ڵ� ���� ����, ���信 ��� �� (`return new ResponseEntity<String>("Hello workd", responseHeaders, HttpStatus.CREATED))`


## @RequestBody
http �ٵ������� ���ϰ� ��ȸ�� �� �ֵ�. �ش������� �ʿ��ϴٸ� `HttpEntity, @RequestHeader` �� ����ϸ�ȴ�. 

@RequestBody �� �����ϸ� `@ModelAttribute` �� �ȴ�. ���� �����ϸ� �ȵȴ�. 
- `@RequestParam`  :String, int, Integer���� �ܼ� Ÿ�� ó�� 
- `@ModelAttribute` : �׿��� ó�� (argument resolver�� �������� Ÿ�� ��)
���� @RequestBody�� �����ϸ� @ModelAttribute �� ����Ǿ� http �޼��� �ٵ� �ƴ϶� ��û �Ķ���͸� ó���ϰ� �ȴ�.


## Http ����

���� �����͸� ����� ���
- ���� ���ҽ� : ���� ���� html, css, js�� �����Ҷ��� ���� ���ҽ� ���
- �� ���ø� ��� : ���� Html �����Ҷ� �����ø� ���
- Http �޼��� ��� : http api�� �����ϴ� ��� �����͸� �����ؾ��ϱ� ������ Json�������� http message body�� �����͸� �Ǿ ����

### ���� ���ҽ� 
������ ��Ʈ�� `/static, /public , /resource, /META_INF/resource ` ���丮�� �ִ� ���� ���Ҹ��� �����Ѵ�.

### �� ���ø�
�����ø��� ���ļ� html�� ���� �ǰ� �䰡 ������ ���� �����Ѵ�. 
�Ϲ������� html�� �������� �����ϴ� �뵵�� ��������� �����ø��� ���� �� �ִ°��̸� ���� �����Ѵ�. 
������ ��Ʈ�� `src/main/resources/templates` ��θ� �⺻ �� ���ø� ��η� �����Ѵ�.

### Thymeleaf ������ ��Ʈ ����
```gradle
implementation("org.springframework.boot:spring-boot-starter-thymeleaf")
```
������ ��Ʈ�� �ڵ����� `ThymeleafViewResolver` �� �ʿ��� ������ ���ϵ��� ����Ѵ�. �׸��� ���� ������ �⺻������ ����ϸ� ������ �ʿ��Ҷ��� �����ϸ�ȴ�. 

```properties
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```

## Http message Converter

`@ResponseBody` ���
- http body�� ���� ������ ���� ��ȯ
- viewResorver ��ſ� HttpMessageConverter�� ����
- �⺻ ����ó�� : StringHttpMessageConverter
- �⺻ ��ü ó�� : MappingJackson2HttpMessageConverter
- byte ó�� ��� ��Ÿ ���� httpMessageConverter�� �⺻���� ��ϵǾ�����

������ ��� Ŭ���̾�Ʈ�� httpAccept ����� ������ ��Ʈ�ѷ� ��ȯ Ÿ�� ���� ���� �����ؼ� httpMessageConverter�� ���õȴ�.

<b>������ MVC�� ������ ��� Http �޼��� �����͸� �����Ѵ�. </b>
- Http ��û : `@RequestBody, HttpEntity(RequestEntity)`
- Http ���� : `@ResponseBody, HttpEntity(ResponseEntity)`
controller�� api �� ȣ��Ǳ����� �޼��� �����Ͱ� ȣ��Ǿ� @RequestBody, httpEntity�� ������ �ش� ��û�� httpbody�� ��ü�� ������ �� ��ü�� ��ȯ�Ѵ����� api�� �����Ѵ�.



## ��û ���� �ڵ鷯 ����� ����
![image](https://user-images.githubusercontent.com/43670838/232226071-448206f0-b350-4872-b7b4-6f8560ef8ed8.png)

### ArguemtnResolver
������̼� ��� ��Ʈ�ѷ��� ó���ϴ� `RequestMappingHandlerAdapter ` �� �ٷ� `ArgumentResolver�� ȣ���ؼ� ��Ʈ�ѷ��� �ʿ���ϴ� �پ��� �Ķ������ ���� �����Ѵ�. �׸��� �� �Ķ������ ���� ��� �غ�Ǹ� ��Ʈ�ѷ��� ȣ���ؼ� ���� �Ѱ��ش�.
## Messageó��
���� ��������Ʈ�� �޼�¡ ó���� �ڵ����� ���ش�. �������� �޼�¡ó�� ���� ����ϴ� ����� �Ʒ� �ڵ带 �����ϸ�ȴ�. 
```java
/**
	 * �޼��� ���� Bean
	 * ����� spring���� �ڵ����� ����Ѵ�.
	 */
	@Bean
	public MessageSource messageSource(){
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		messageSource.setBasenames("messages", "errors");
		messageSource.setDefaultEncoding("utf-8");
		return messageSource;
	}
```

`basenames`: ���� ������ �̸��� �����Ѵ�. message�� �����ϸ� message.properties ������ �о ����Ѵ�. 
���� ��ġ�� `/resources/messgaes.properties` �� �θ� �ȴ�. 

��������Ʈ���� �޼��� �ҽ��� �����ϱ����ؼ� �������� ���� ��������ʰ� `application.properties ` �� �Ʒ� �ҽ��� ���� �� �� �ֵ�. 
```
spring.messages.basename = messages,config.i18n.messages
```
������ ��Ʈ �޼��� �ҽ��⺻���� basename = messages �̴�. 


## BindingResult

```java
@PostMapping("/add")
    public String addItemV1(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
        //����
        if(!StringUtils.hasText(item.getItemName())){
            bindingResult.addError(new FieldError("item", "itemName", "��ǰ�̸��� �ʼ��Դϴ�."));
        }

        if(item.getPrice() == null || item.getPrice() <1000 || item.getPrice() >1000000){
            bindingResult.addError(new FieldError("item", "price", "������ 1,000 - 1,000,000���� ����մϴ�."));
        }
        if(item.getQuantity() == null || item.getQuantity() >= 9999){
            bindingResult.addError(new FieldError("item", "quantity", "������ �ִ� 9,999���� ����մϴ�."));
        }
        //Ư���ʵ尡 �ƴ� ���� �� ����
        if(item.getPrice() != null && item.getQuantity() != null){
            int resultPrice = item.getPrice()* item.getQuantity();
            if(resultPrice <10000){
                bindingResult.addError(new ObjectError("item", "���� * ������ ���� 10,000�� �̻��̾���մϴ�. ���� �� = " + resultPrice));
            }
        }

        //���� �����ϸ� �ٽ� �Է� ������
        if(bindingResult.hasErrors()){
            log.info("errors = {}" , bindingResult);
            return "validation/v2/addForm";
        }

        //���� ����
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v2/items/{itemId}";
    }
```

`BindingResult`�� ������ `@ModelAttribute Item item` �ڿ� �;��Ѵ�. 
�� attribute�� ���� ���� : `new FieldError("item", "itemName", "��ǰ�̸��� �ʼ��Դϴ�.")` ó�� FieldError ���
�۷ι� ���� : `new ObjectError("item", "���� * ������ ���� 10,000�� �̻��̾���մϴ�. ���� �� = " + resultPrice)`ó�� objectError ���

`BindingResult` �� ������ @ModelAttribute �� ������ ���ε� �� ������ �߻��ص� ��Ʈ�ѷ��� ȣ��ȴ�.

@ModelAttribute�� ���ε��� Ÿ�Կ����� �߻��ϸ�?
- bindingResult�� ������ 400������ �߻��ϸ鼭 ��Ʈ�ѷ��� ȣ����� �ʰ� ���� �������� �̵�����. 
- bindingResult�� ������ ��������(FieldError)�� bindingResult�� ��Ƽ� ��Ʈ�ѷ��� ���� ȣ���Ѵ�. 

<b>BindingResult�� �������� �����ϴ� ���</b>
- @ModelAttribute�� ��ü�� Ÿ�� ���� ������ ���ε��� �����ϴ� ��� �������� FieldError�� �����ؼ� bindingResult�� �־��ش�. 
- �����ڰ� ���� �־��ش� 
- Validator ��� 
BindingResult�� �������̽��̰� Errors�� ��� �ް� �ִ�. 
���� �Ѿ� ���±���ü�� `BeanPropertyBindingResult`��� ���ε� �Ѵ� �����ϰ� �����Ƿ� `BindingResult` ��ſ� `Errors`�� ����ص��ȴ�. 

������ BindingResult�� ����Ͽ� FieldError, ObjectError�� � ��ü���� �־��ִ� �۾��� �����ڿ��� �ϳ� ���ŷӴ�.
```java
bindingResult.addError(new FieldError("item", "itemName", item.getItemName(), false, new String[]{"required.item.itemName"}, null, null));
```
```java
@PostMapping("/add")
    public String addItemV3(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
    }    
```
���� �Ķ���� ������ ���� item�ڿ� bindingResult�� ���Եȴ�. �� �ǹ̴� bindingResult�� �̹� �����ؾߵ� ��ü�� item��ü��°��� �˰� �ִٴ� ���̴�.

## errors.properties
```
#Level1
required.item.itemName=��ǰ �̸��� �ʼ� �Դϴ�.
#Level2
required=�ʼ� �Դϴ�.
```
���� errors.properties�� ���� required�� �����ϴ� �����޼����� 2�����ִ�.
�̷��� FieldError�� errorCode �Ķ���Ϳ� "required" ��� ������ �������� �����α׸� 1������ ��´�.


## Bean Validation
����ü�� �ƴ϶� Bean validation(JSR-380)�̶�� ��� ǥ���̤���. �����ֳ����̼ǰ� ���� �������̽��� �����̴�. ��ġ JPA�� ǥ�ر���̰� �� ����ü�� ���̹�����Ʈ�� �ִ°Ͱ� ����.
`spring-boot-starter-validation` �������� �߰��ϸ� �ڵ����� BeanValidator�� �����ϰ� �������� �����Ѵ�. 
������ ��Ʈ�� �ڵ����� �۷ι� Validator�� �������. `@valid @Validated`�� �����ϸ� �Ǹ� ���������� �߻��ϸ� `FieldError, ObjectError`�� �����ؼ� `BindingResult` �� ����ش�. 

<b>���ε��� ������ �ʵ常 Bean Validation ����</b>
BeanValidator�� ���ε��� ������ �ʵ�� BeanValidation�� �������� �����. 
Ÿ�Ժ�ȯ�� �����ؼ� ���ε��� ������ �ʵ忩�� BeanValidation ������ �ǹ� �ִ�.

### BeanValidation Object ���� ����

```java

@Data
public class Item {

    private Long id;
    @NotBlank(message = "���� �ȵ�")
    private String itemName;
    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;
}
```
���� �ڵ�ó�� Ư���ʵ�(itemName, price)�� validation�� ����ȴ�.�׷��ٸ� item ��ü ��ü�� �������ִٸ� ObjectError�� ��� ó���� �� ������.?

```java
@Data
@ScriptAssert(lang = "javascript", script = "_this.price*_this.quantity >= 10000")
public class Item {
}
```
`ScriptAssert`�� ����ϸ� �ȴ�.
������ ���������� ������ ���� �����ϱ� ������ ������Ʈ ���� ������ ���� �ڵ�� �ۼ��ϴ°��� ����.
 
 ���� 
 @ModelAttribute : http ��û �Ķ���� (url ������Ʈ��, post form ) �ٷꋚ ���
 @RequestBidy : api json��û �ٷ궧 ��� 

 <b>@ModelAttribute vs @RequestBody</b>
 http ��û �Ķ���͸� ó���ϴ� @ModelAttribute�� ������ �ʵ� ������ ����ȴ�. Ư�� �ʵ� Ÿ�Կ� ���� ���� ������ ������ص� ������ �ʵ�� ����ó�� �� �� �ִ�. 
 HttpMessageConverter�� @ModelAttribute�� �ٸ��� ������ �ʵ������ ������� �ʰ� ��ü ��ü ������ ����ȴ�. 
 ���� �޽��� �������� �۵��� �����ؼ� Item��ü�� ������ @Valid, @Validated�� ����ȴ�. 
- @ModelAttribute : �ʵ������ �����ϰ� ���ε��� ����ȴ�. Ư�� �ʵ尡 ���ε� ���� �ʾƵ� ������ �ʵ�� ���� ���ε� �ǰ� Validator�� ����� ������ ������ �� �ִ�. 
- @RequestBody : httpMessageConverter �ܰ迡�� json �����͸� ��ü�� �������� ���ϸ� ���� �ܰ谡 ������� �ʾ� ���ܸ� �߻��Ѵ�.

## Cookie

### �α��� ���� �����ϱ�
�������� �α��ο� �����ϸ� Http ���信 ��Ű�� ��Ƽ� ������ ��������. �׷��� �������� ���V�� �ش� ��Ű�� �����ؼ� �����ش�.
���� ��Ű : ���ᳯ¥�� �Է��ϸ� �ش� ��¥���� ��Ű���� ����
������� : ���ᳯ¥�� �����ϸ� ������ ����ñ��� ����

### Cookie ���� ����
- ��Ű ���� ���� ����
    - client�� ��Ű�� ������ �����ϸ� �ٸ� ����ڰ��ȴ�. 
- ��Ű�� ������ ������ ��ó�� �� �ִ�. 
    - ��Ű�� ���������� �ִٸ� �ɰ��� �̽����ȴ�. 
    - �������� ������������ �����ǰ� ��Ʈ��ũ ��û���� ��� Ŭ���̾�Ʈ���� ������ ���޵ȴ�. 
    - ��Ű���� ������ ���� ���� pc�� �и����� �ְ� ��Ʈ��ũ ���� �������� �и����� �ִ�. 

### ��� 
- ��Ű�� �߿��� ���� ���� ���� �ʰ� ������ �Ť��� �����Ұ����� ���� ��ū�� �����ϰ� �������� ��ū�� ��䤷�� id�� �����ؼ� �ν��Ѵ�. �׸��� �������� ��ū�� �����Ѵ�. 
- ��ū�� ��Ŀ�� ������ ���� �־ ã�� �� ������ ���� �Ұ����ؾ��Ѵ�
- ��ū�� ����ð��� ª�� �����Ѵ�. �Ǵ� ��ŷ�� �ǽɵǴ� ��� ������ �ش� ������ ������ �����Ѵ�.


## Session
��Ű�� ������ �����ϱ����� ������ ����� �� �ִ�. 
���� �޴����� ���
 1. ���� ����
 2. ���� ��ȸ
 3. ���� ����
 3���� ������� �����Ѵ�.

 ## Servlet Http ����
 ������ ������ ���� `HttpSession` ����� �����شµ� ���� ������ ������ ������ �̹� �����Ǿ� �ִ�.
 ```java
 //������ ������ �ִ� ���� ��ȯ, ������ �ű� ���� ����
        //request.getSession() �� default���� true
        HttpSession session = request.getSession();
```
request.getSession(true) : ������ ������ ���� ���� ��ȯ, ������ ���ο� ������ �����ؼ� ��ȯ�Ѵ�. 
request.getSession(false) : ������ ������ ���� ���� ��ȯ, ������ ���ο� ������ �������� �ʰ� `null` �� ��ȯ�Ѵ�.

## Tracking lodes
�α����� ó�� �õ��ϸ� URL�� `jsessionid` �� �����ϰ� �ֵ�. 
�̰��� ���������� ��Ű���������� ������ ��� URL�� ���ؼ� ������ �����ϴ� ����̴�. �� ����� ������Ϸ��� URL�� �� ���� ��� �����ؾߵǴµ� Ÿ�Ӹ��� ���� �����ø� ������ ���ؼ� ��ũ�� �ɸ� jsessionid�� �ڵ����� �������ش�. URL���� ����� ���� �׻� ��Ű�� ���ؼ��� ������ �����ϰ� ������ ���� �ɼ��� �־��ָ�ȴ�. �̷��� �Ǹ� URL�� jsessionid�� ������� �ʴ´�. 
```
application.properties

serverlservlet.session.tracking-mode = cookie
```
### ����
Session�� �ּ����� �����͸� �����ؾ��Ѵ�. ������ ������ �뷮 * ����ڼ� �� ������ �޸� ��뷮�� ���ϰ� �þ�� ��ַ� �̾��� �� �ִ�. (�⺻ 30��)������ ��� 