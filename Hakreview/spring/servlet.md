# Servlet

스프링부트를 사용하여 톰캣서버를 내장화를 통해 서블릿코드 실행

## Annotation 정리
### @ServletComponentScan
[참고]https://www.baeldung.com/spring-servletcomponentscan

@ServletComponentScan은 어느 configuration없이 사용할 수 있는데 그 이유는 내장된 서블렛컨테이너때문이다. 임베디드 컨테이너들은 @WebServlet, @WebFilter, @WebListner 를 지원하지 않기 때문에 내장컨테이너에 의존하는 스프링부트는 세로운 어노테이션인 @ServletComponentScam을 사용한다.
```java
package hello.servlet;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@ServletComponentScan // 서블릿 자동 등록
@SpringBootApplication
public class ServletApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServletApplication.class, args);
	}

}
```

메인클래스에 `@ServletComponentScan` 을 사용하면 application.class 하위의 패키지들에서 모두 사용할 수 있다. 

HTTP 요청이 오면 WAS가 request, response 객체를 만들어서 서블렛에게 전달한다.

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
        //아래 코드를 사용하여 response 객체에 값을 대입할 수 있다.
        response.getWriter().write("hello" + username);

    }
}
```

`@WebServlet`을 사용하여 HTTP요청을 통해 매핑된 URL이 호출되면 서블릿 컨테이너는 ` protected void service(HttpServletRequest request, HttpServletResponse response)` 메서드를 자동으로 호출한다. 

## Servlet container 동작 방식

<img width="659" alt="image" src="https://user-images.githubusercontent.com/43670838/209827848-9a6f703f-3e77-41bf-ac95-ef192a685fd4.png">

스프링부트로 생성한 application을 실행하면 내장톰캣버서가 서블릿 컨테이너를 띄워준다. 
그 후, 웹에서 WAS를 통해 request를 보내면 WAS는 자동으로 request, response 객체를 생성해서 url에따라 servlet을 실행하고 response에 value를 세팅하여 웹에게 내려준다. 


## HttpServletRequest
HTTP요청 메시지 파싱을 편리하게 사용할 수 있도록 개발자 대신에 HTTP요청 메시지를 파싱한다. 그 결과를 HttpServletRequest객체에 담아서 제공한다. 

` 임시 저장소 `기능을 제공한다. 
HTTP 메시지요청이 들어와 응답을 보내기까지가 HttpServletRequest객체의 생존기간인데 그동안 request.setAttribute, request.getAttribute를 사용하여 값을 저장 조회 할 수 있다. 



## JSP
servlet만 사용하게 되면 아래 사진과 같은 자바코드에 html을 사용해야 되는 끔찍한 상황이 발생하게된다. 왜 끔찍하냐면 html 태그를 사용하면서 오타가 발생할 확률이 높고 다른 개발자들이 이 코드를 보고 어떤 화면인지 파악하기 힘들기 때문에 가독성도 현저히 떨어지게 된다. 따라서 이런 불편함을 해소하기위해 jsp 를 사용 한다.
![image](https://user-images.githubusercontent.com/43670838/212692640-0c2683f3-9727-4198-95aa-b0ea2308a5d8.png)

```
build.gradle 의존성 주입
implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
implementation 'jakarta.servlet.jsp.jstl:jakarta.servlet.jsp.jstl-api'
```

###  JSP 문법
```jsp
<!--> html 태그 위에 아래 코드 필수 </-->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<!-->java 코드 import </-->
<%@ page import="hello.servlet.domain.member.Member" %>
<%@ page import="hello.servlet.domain.member.MemberRepository" %>

<!-->java 코드 </-->

<%
    //request, response 사용 가능
    MemberRepository memberRepository = MemberRepository.getInstance();

    System.out.println("MemberSaveServlet.service");
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));
%>

```
jsp에 java 코드를 작성하면 자동으로 
