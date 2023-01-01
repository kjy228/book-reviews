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

