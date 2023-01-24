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
jsp에 java 코드를 작성하면 자동으로 servlet으로 변환되어 HttpServlet의  request, response를 사용할 수 있다.


하지만 jsp도 불완전하다. 코드를 보면 java코드, 데이터를 조회하는 레포지토리 등 다양한 코드가 모두 JSP에 노출되어있따. jsp가 너무 많은 역할을하며 유지보수 측면에서도 한 파일의 크기가 굉장히 커질 수 있따. 이러한 문제를 해결하기 위해서 비즈니스 로직은 서블릿 처럼 다른고셍서 처리하고, JSP 는 목적에 맞게 HTML로 view를 그리는 일에 집중하도록 `MVC` 패턴이 등장했다.

## MVC 패턴
` 변경의 라이프 사이클` UI 일부 수정하는 일과 비스니스 로직을 수정하는 일은 각가 다르게 발생할 가능성이 높고서로 대부분 영향을 주지 안흔다. 이렇게 변경의 라이프 사이클이 다른 부분을 하나의 코드로 관리하는것은 유지보수하기 좋지 않다. 
` 기능 특화` JSP 같은 뷰 템플릿을 화면을 렌더링 하는데 최적화 되어있기 때문에 이 부분의 업무만 담당하는것이 가장 효과적이다.

`Controller` HTTP 요청을 받아서 파라미터를 검증하고 비즈니스 로직 시행. 뷰에 전달할 결과 데이터를 받아서 모델에 답는다. 
`Model`: 뷰에 출력할 데이털르 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이다. 
`View`: 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다.

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
`dispatcher.forward()` :  다른 서블릿이나 jsp로 이동할수 있는 기능이며 서버내부에서 재호출이 발생한다. 
`/WEB-INF`: 이 경로안에 JSP가 있으면 외부에서 직접  JSP 호출할 수 없다. 

`redirect vs forward`
리다이렉ㄹ트는 실제 클라이언트에 응다빙 나갓다가, 클라이언트가 redirect경로로 재요청한다. 따라서 클라에서 인지할 수 있고 url 경로도 실제 변ㄱ여된다. 반면 포워드는 서버 내부에서 일어나느 호출이기 때문에 클라가 인지하지 못한다.