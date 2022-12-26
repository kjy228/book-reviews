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