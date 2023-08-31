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

## MVC 한계
컨트로러는 중복이 많고 피요하지 않은 코드가 많다. 

`포워드 중복` 
View로 이동하는 코드가 중복호출된다. 메서드로 공통화해도 되지만 해당 메서드도 항상 직접 호출해야한다. 
```java
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request,response);
```

`viewPath 중복`
```java
    String viewPath = "/WEB-INF/views/members.jsp";
```
viewPath 에서 jsp가 아니라 thymeleaf같은 다른 뷰로 변경한다면 전체 코드를 다 변경해야한다. 

기능이 복잡해 질수록 컨트롤러에서 공통으로 처리햐아 하는 부분이 점점 더 많이 증가할 것이다. 단순히 공통 기능을 메서드로 뽑으면 될 것 같지만, 결과적으로 해당 메서드를 항상 호출해야 하고 실수로 호출하지 않으면 문제가된다. 또한 호출하는것 자체가 중복이다. 



## Adapter Pattern
- 기존 코드를 재사용하기위해 내적, 외적 구조를 변환하는 작업을 처리하며 인터페이스를 처리하고 인터페이스를 활용해 보정코드를 사용한다. 
기능상 문제 이 동작하는 코드가 단지 인터페이스 차이 때문에 사용할 수 없는 경우 많이 응용되는 패턴이다. 
- 오래된 레거시 코드나 라이브러리ㅡㄹ 재사용할 때 유용한 패턴이다. 서로 호환되지 않는 인터페이스를 가진 코드를 결함하여 응용 프로그램에서 동작할 수 있도록 도와준다.

## DispatcherServlet 살펴보기
스프링 MVC도 프론트컨트롤러 패턴으로 구현되어있따. 
스프링 MVC 컨트롤러가 바로 DispatcherSErvlet이다. 
이 DispatcherServlet이 바로 MVC의 핵심이다. 

스프링 부트는 dispatcherServlet을 서블릿으로 자동으로 등록하면서 모든경로`urlPatters="/"` 에 대해서 매핑한다. 

*요청흐름*
- 서블릿이 호출되먼 `HttpServlet` 이 제공하는 service() 가 호출된다. 
- 스프링 MVC는 dispatcherSErvlet의 부모인 FrameworkServlet에서 service()를 오버라이드 해두었다. 
- service()를 시작으로 `DispatcherServlet.doDispatch() 가 호출된다. 

*동작 순서*
1. 핸들러 조회 : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러를 조회한다. 
2. 핸들러 어댑터 조회 : 핸들러를 실행할 수 있는 핸들러 어댑터를 조회.
3. 핸들러 어댑터 실행 : 핸들러 어댑터를 실행한다.
4. 핸들러 실행 : 핸들러 어댑터가 실제 핸들러를 실행한다. 
5. Model And View 반환 : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelandView로 변환해서 반환한다. 
6. viewResolver 호출 : 뷰리졸버를 찾고 실행한다. 
- jsp는 `InternalResourceViewReslover` 가 자동 등록되고 사용된다. 
7. View 반환 : 뷰리졸버는 뷰의 논리이름을 물리 이름으로 바꾸고 렌더링 역할을 담당하는 뷰 객체를 반환한다. 
8. 뷰 렌덜링 : 뷰를 통해서 뷰를 렌더링 한다.
![image](https://user-images.githubusercontent.com/43670838/221402899-67df3345-5cbc-4263-b17f-723d5644dca8.png)


## @Controller 이전의 spring Controller


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
Controller implements할때 `web.springmvc.old` 패키지 아래에있는 controller를 사용해야한다.
그 후 `@Component` 의 bean 이름을 url로 설정해야된다.


### 스프링부트가 자동으로 등록하는 핸들러 매핑과 핸들러 어댑터
**HandlerMapping**
```
0 = RequestMappingHandlerMapping : 어노테이션 기반의 컨드롤러인 @RequestMapping에서 사용
1 = BeanNameUrlHandlerMapping : 스프링 빈의 이름으로 핸들러를 찾는다.
```

**HandelrAdapter**
```
0 = RequestMappingHandlerAdapter : 어노테이션 기반의 컨드롤러인 @RequestMapping에서 사용
1 = HttpRequestHandlerAdapter : HttpRequestHandler 처리
2 = SimpleControllerHandlerAdapter : Controller 인터페이스(어노테이션x, 과거에 사용)처리
```
핸들러 매핑, 핸들러 어뎁터도 모두 순서대로 찾고 만약 없으면 `다음순서` 로 넘어 간다.

springboot의 자동 기능을사용하여 위의 controller가 어떻게 접근되는지 순서로 나타내면
1. 핸들러 매핑으로 핸들러를 조회한다. 이 경우 빈 이름으로 핸들러를 찾아야 하기 땜누에 이름 그대로 빈 이름으로 핸들러를 찾아주는 `BeanNmeUrlHandlerMapping`가 실행에 성공하고 핸들러인 `OldController`를 반환한다. 
2. 핸들러 어댑터 조회 : `HandlerAdapter`의 `supports()` 를 순서대로 호출하여 SimpleCOntrollerHandlerAdapter가 지원 대상이된다. 
3. 핸들러 어댑터 실행
디스패쳐 서블릿이 조회한 SimpleControllerHandlerAdapter를 실행하면서 핸들러 정보도 함꼐 넘겨준다. 


```java
@Component("/springmvc/request-handler")
public class MyHttpRequestHandler implements HttpRequestHandler {
    @Override
    public void handleRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("MyHttpRequestHandler.handlerRequest");
    }
}
```
위의 코드로 application 실행후 localhost:8080/springmvc/request-handler 접속시 spring내부에서 어떻게 찾을까?

`핸들러 매핑으로 핸들러 조회`
1. HandlerMapping을 순차적으로 실행해서 핸들러를 찾는다. 
2. 이 경우 빈 이름으로 핸들러를 찾아야 하기 때문에 이름 그대로 빈 이름으로 핸들러를 찾아주는 `BeanNameUrlHandlerMapping` 가 실행에 성공하고 핸들러인 `MyHttpRequestHandler` 를 반환한다. 
`핸들러 어댑터 조회`
1. `HandlerAdapter`의 `supports()를 순서대로 호출한다. 
2. `HttpRequestHandlerAdapter`가 `HttpRequestHandler 인터페이스를 지원하므로 대상이 된다. 

`핸들러 어댑터 실행`
1.디스패쳐 서블릿이 조회한 `HttpRequesthandlerAdapter를 실행하면서 핸들러 정보도 함께 넘겨준다. 
2.`HttpRequestHandlerAdapter는 핸들러인 `MyHttpRequestHandler`를 내부에서 실행하고 그결과를 반환한다.


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
prefix와 suffix를 등록하면  ModelAndView에서 jsp를 리턴할 수 있다. 그 이유는 스프링 부트에서 아래코드를 자동으로 설정해주기 때문이다.
```java
@Bean
ViewResolver internalResourceViewResolver(){
    return enw InternalResourceViewResolver("/WEB-INF/views/", ".jsp");
}
```

# SpringMVC

`@Contorller`
- 스프링이 자동으로 빈으로 등록한다.(내부에 `@Component가 있어서 컴포넌트 스캔의 대상이 됨)
- 스프링MVC에서 어노테이션 기반 컨트롤러로 인식한다.
- `ReuqestMappingHandlerMapping` 은 스프링 빈 중에서 `@RequestMapping`, `@Controller` 가 클래스 레벨에 붙어있는 경우 매핑정보로 인식한다.

## RequestMapping 특징
```java
@RequestMapping(value = "/new-form")
    public String newForm() {
        return "new-form";
    }
```
위의 api로 postman을 사용하여 요청을 보내면 Get, Post, delete 모든 http Request사용이 가능하다. 따라서 이런문제를 방지하기 위해서 아래 코드로 변경하면된다. 

```java
@RequestMapping(value = "/new-form", method = RequestMethod.GET)
    public String newForm() {
        return "new-form";
    }
```

## @Controller와 @RestController 의 차이

@Controller
- 기본적으로 view 이름을 반환하기위해 사용한다.
- view를 렌더링하기위해 viewResolver가 사용되며 설정에맞게 view를 찾아 렌더링한다.

@Restcontroller
- data혹은 json으로 return할 수 있도록 한다.

---

## Log 관련 
- Log level : trace - debug - info - warn -error
- 개발서버 : debug출력
- 운영 서버 : info 출력 ( info, warn, error 레벨의 log 출력됨)

## Log 출력시 주의 사항
```java
log.info("info long= {}", name); // 이렇게 찍어야됨
log.info("info long= "+ name); // 이렇게 X
```
그 이유는 `log.info("info long= "+ name)` 를 사용하면 스프링 내부적으로 스트링을 더하는 연산(infolong=name)을 하여 메모리를 잡아먹게 된다. 즉, 쓸모없는 리소스를 사용하게 된다. 

-----
## QueryParam vs PathVariable 차이
@QueryParam
- 이 어노테이션을 사용하면 url 경로에 ? 이 붙는다

@PathVariable
```java

```
이코드는 url 상에셔 `http://localhost:8080/mapping/1` 와 같이 보인다. 또한 개발자 도구를 사용하여 heaer의 경로를 보면 아래 사진과 같이 보인다. 
![image](https://user-images.githubusercontent.com/43670838/225639281-e46bbf22-d308-415c-99b4-f7456428b597.png)


## Http Request


### @ResponseBody에 대해서
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
controller를 위와 같은 코드로 작성하면 첫번?로 @Controller 어노테이션을 사용했기 때문에
"ok"를 리턴하면 viewname을 리턴하게 된다. 
하지만 `@ResponseBody`를 사용하면 String으로 리턴할 수 있다.

### @RequestParam

<b>특징</b>
- 변수명이 같으면 `@RequestParam("username") String username` -> `@RequestParam String username` 으로 생략 할 수 있다. 
- 객체가아닌 기본타입(String, int등 )을 받으면 `@RequestParam String username` -> `String username`  으로 생량할 수 있다.
- primitive type 에 null 입력 안됨. `@RequestParam(required=false) int age`를 사용했을때 int가 아니라 Integer를 사용해야 null 값을 받을 수 있다. 아니면 `defaultValue` 옵션을 사용하면 된다.


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

Stream은 항상 byte 코드이기때문에 문자열로 변환하기 위해서는 어떤 `형`으로 변환할지 지정해야된다.


## HttpEntity
Http heaer , body정보를 편리하게 조회할 수 있다. 
메세지 바디 정보를 직접 조회
요청 파라미터를 조회하는 기능과 관계 없음(@RequestParam, @ModelAttribute)
- httpentity는 응답에서도 사용가능하다. 
- 메세지 바디 정보 직접 반환 및 헤더정보 포함 가능하지만 `view 조회는 안됨`

HttpEntity를 상속받은 다음 객체들도 같은 기능을 제공한다. 
`ReqeustEntity` : HttpMethod, url 정보 추가, 요청에서 사용
`ResponseEntity`: http상태코드 설정 가능, 응답에 사용 됨 (`return new ResponseEntity<String>("Hello workd", responseHeaders, HttpStatus.CREATED))`


## @RequestBody
http 바디정보를 편리하게 조회할 수 있따. 해더정보가 필요하다면 `HttpEntity, @RequestHeader` 를 사용하면된다. 

@RequestBody 는 생략하면 `@ModelAttribute` 가 된다. 따라서 생략하면 안된다. 
- `@RequestParam`  :String, int, Integer같은 단순 타입 처리 
- `@ModelAttribute` : 그외의 처리 (argument resolver로 지정해준 타입 외)
따라서 @RequestBody를 생략하면 @ModelAttribute 가 적용되어 http 메세지 바디가 아니라 요청 파라미터를 처리하게 된다.


## Http 응답

응답 데이터를 만드는 방법
- 정적 리소스 : 웹에 정적 html, css, js를 리털할떼는 정적 리소스 사용
- 뷰 템플릿 사용 : 동적 Html 제공할때 뷰템플릿 사용
- Http 메세지 사용 : http api를 제공하는 경우 데이터를 전달해야하기 땜누에 Json형식으로 http message body에 데이터를 실어서 보냄

### 정적 리소스 
스프링 부트는 `/static, /public , /resource, /META_INF/resource ` 디렉토리에 있는 정적 리소르를 제공한다.

### 뷰 템플릿
뷰템플릿을 거쳐소 html이 생성 되고 뷰가 응답을 만들어서 전달한다. 
일반적으로 html을 동적으로 생성하는 용도로 사용하지만 뷰템플릿이 만들 수 있는것이면 뭐든 가능한다. 
스프링 부트는 `src/main/resources/templates` 경로를 기본 뷰 템플릿 경로로 제공한다.

### Thymeleaf 스프링 부트 설정
```gradle
implementation("org.springframework.boot:spring-boot-starter-thymeleaf")
```
스프링 부트가 자동으로 `ThymeleafViewResolver` 와 필요한 스플이 ㅂ니들을 등로한다. 그리고 다음 설정도 기본값으로 사용하면 변경이 필요할때만 설정하면된다. 

```properties
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```

## Http message Converter

`@ResponseBody` 사용
- http body에 문자 내용을 직접 반환
- viewResorver 대신에 HttpMessageConverter가 동작
- 기본 문자처리 : StringHttpMessageConverter
- 기본 객체 처리 : MappingJackson2HttpMessageConverter
- byte 처리 등등 기타 여러 httpMessageConverter가 기본으로 등록되어있음

응답의 경우 클라이언트의 httpAccept 헤더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서 httpMessageConverter가 선택된다.

<b>스프링 MVC는 다음의 경우 Http 메세지 컨버터를 적용한다. </b>
- Http 요청 : `@RequestBody, HttpEntity(RequestEntity)`
- Http 웅답 : `@ResponseBody, HttpEntity(ResponseEntity)`
controller의 api 가 호출되기전에 메세지 컨버터가 호출되어 @RequestBody, httpEntity가 있으면 해당 요청의 httpbody의 객체를 꺼내서 그 객체를 변환한다음에 api가 동작한다.



## 요청 매핑 핸들러 어댑터 구조
![image](https://user-images.githubusercontent.com/43670838/232226071-448206f0-b350-4872-b7b4-6f8560ef8ed8.png)

### ArguemtnResolver
어노테이션 기반 컨트롤러를 처리하는 `RequestMappingHandlerAdapter ` 는 바로 `ArgumentResolver를 호출해서 컨트롤러가 필요로하는 다양한 파라미터의 값을 생성한다. 그리고 이 파라미터의 값이 모두 준비되면 컨트롤러를 호출해서 값을 넘겨준다.
## Message처리
현재 스프링부트는 메세징 처리를 자동으로 해준다. 수동으로 메세징처리 빈을 등록하는 방법은 아래 코드를 참조하면된다. 
```java
/**
	 * 메세지 관리 Bean
	 * 현재는 spring에서 자동으로 등록한다.
	 */
	@Bean
	public MessageSource messageSource(){
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		messageSource.setBasenames("messages", "errors");
		messageSource.setDefaultEncoding("utf-8");
		return messageSource;
	}
```

`basenames`: 설정 파일의 이름을 지정한다. message로 지정하면 message.properties 파일을 읽어서 사용한다. 
파일 위치는 `/resources/messgaes.properties` 에 두면 된다. 

스프링부트에서 메세지 소스를 설정하기위해서 수동으로 빈을 등록하지않고 `application.properties ` 에 아래 소스를 설정 할 수 있따. 
```
spring.messages.basename = messages,config.i18n.messages
```
스프링 부트 메세지 소스기본값은 basename = messages 이다. 


## BindingResult

```java
@PostMapping("/add")
    public String addItemV1(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
        //검증
        if(!StringUtils.hasText(item.getItemName())){
            bindingResult.addError(new FieldError("item", "itemName", "상품이름은 필수입니다."));
        }

        if(item.getPrice() == null || item.getPrice() <1000 || item.getPrice() >1000000){
            bindingResult.addError(new FieldError("item", "price", "가격은 1,000 - 1,000,000까지 허용합니다."));
        }
        if(item.getQuantity() == null || item.getQuantity() >= 9999){
            bindingResult.addError(new FieldError("item", "quantity", "수량은 최대 9,999까지 허용합니다."));
        }
        //특정필드가 아닌 복합 룰 검증
        if(item.getPrice() != null && item.getQuantity() != null){
            int resultPrice = item.getPrice()* item.getQuantity();
            if(resultPrice <10000){
                bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이어야합니다. 현재 값 = " + resultPrice));
            }
        }

        //검증 실패하면 다시 입력 폼으로
        if(bindingResult.hasErrors()){
            log.info("errors = {}" , bindingResult);
            return "validation/v2/addForm";
        }

        //성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v2/items/{itemId}";
    }
```

`BindingResult`는 무조건 `@ModelAttribute Item item` 뒤에 와야한다. 
각 attribute에 대한 오류 : `new FieldError("item", "itemName", "상품이름은 필수입니다.")` 처럼 FieldError 사용
글로벌 오류 : `new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이어야합니다. 현재 값 = " + resultPrice)`처럼 objectError 사용

`BindingResult` 가 있으면 @ModelAttribute 에 데이터 바인딩 시 오류가 발생해도 컨트롤러가 호출된다.

@ModelAttribute에 바인딩시 타입오류가 발생하면?
- bindingResult가 없으면 400오류가 발생하면서 컨트롤러가 호출되지 않고 오류 페이지로 이동힌다. 
- bindingResult가 있으면 오류정보(FieldError)를 bindingResult에 담아서 컨트롤러를 정상 호출한다. 

<b>BindingResult에 검증오류 적용하는 방법</b>
- @ModelAttribute의 객체에 타입 오류 등으로 바인딩이 실패하는 경우 스프링이 FieldError를 생성해서 bindingResult에 넣어준다. 
- 개발자가 직접 넣어준다 
- Validator 사용 
BindingResult는 인터페이스이고 Errors를 상속 받고 있다. 
실제 넘어 오는구현체는 `BeanPropertyBindingResult`라는 것인데 둘다 구현하고 있으므로 `BindingResult` 대신에 `Errors`를 사용해도된다. 

하지만 BindingResult를 사용하여 FieldError, ObjectError에 어떤 긱체인지 넣어주는 작업은 개발자에게 꽤나 번거롭다.
```java
bindingResult.addError(new FieldError("item", "itemName", item.getItemName(), false, new String[]{"required.item.itemName"}, null, null));
```
```java
@PostMapping("/add")
    public String addItemV3(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {
    }    
```
위의 파라미터 순서를 보면 item뒤에 bindingResult가 오게된다. 이 의미는 bindingResult는 이미 검증해야될 객체가 item객체라는것을 알고 있다는 것이다.

## errors.properties
```
#Level1
required.item.itemName=상품 이름은 필수 입니다.
#Level2
required=필수 입니다.
```
위의 errors.properties를 보면 required로 시작하는 에러메세지가 2개가있다.
이럴땐 FieldError의 errorCode 파라미터에 "required" 라고 적으면 디테일한 에러로그를 1순위로 잡는다.


## Bean Validation
구현체가 아니라 Bean validation(JSR-380)이라는 기술 표준이ㅏㄷ. 검증애노테이션과 여러 인터페이스의 모음이다. 마치 JPA가 표준기술이고 그 구현체로 하이버네이트가 있는것과 같다.
`spring-boot-starter-validation` 의존성을 추가하면 자동으로 BeanValidator를 인지하고 스프링에 통합한다. 
스프링 부트는 자동으로 글로벌 Validator로 등록힌다. `@valid @Validated`만 적영하면 되며 검증오류가 발생하면 `FieldError, ObjectError`를 생성해서 `BindingResult` 에 담아준다. 

<b>바인딩에 성공한 필드만 Bean Validation 적용</b>
BeanValidator는 바인딩에 실패한 필드는 BeanValidation을 적용하지 안흔다. 
타입변환해 성공해서 바인딩에 성공한 필드여야 BeanValidation 적용이 의미 있다.

### BeanValidation Object 오류 관련

```java

@Data
public class Item {

    private Long id;
    @NotBlank(message = "공백 안됨")
    private String itemName;
    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;
}
```
위의 코드처럼 특정필드(itemName, price)에 validation이 적용된다.그렇다면 item 객체 자체에 오류가있다면 ObjectError는 어떻게 처리할 수 있을까.?

```java
@Data
@ScriptAssert(lang = "javascript", script = "_this.price*_this.quantity >= 10000")
public class Item {
}
```
`ScriptAssert`를 사용하면 된다.
하지만 현업에서는 제약이 많고 복잡하기 때문에 오브젝트 관련 오류는 직접 코드로 작성하는것이 좋다.
 
 참고 
 @ModelAttribute : http 요청 파라미터 (url 쿼리스트링, post form ) 다룰떄 사용
 @RequestBidy : api json요청 다룰때 사용 

 <b>@ModelAttribute vs @RequestBody</b>
 http 요청 파라미터를 처리하는 @ModelAttribute는 각각의 필드 단위로 적용된다. 특정 필드 타입에 맞지 안흔 오류가 ㅂ라생해도 나머지 필드는 정상처리 할 수 있다. 
 HttpMessageConverter는 @ModelAttribute와 다르게 각각의 필드단위로 적용되지 않고 전체 객체 단위로 적용된다. 
 따라서 메시지 컨버터의 작동이 성공해서 Item객체를 만들어야 @Valid, @Validated가 적용된다. 
- @ModelAttribute : 필드단위로 정교하게 바인딩이 적용된다. 특정 필드가 바인딩 되지 않아도 나머지 필드는 정상 바인딩 되고 Validator를 사용한 검증도 적용할 수 있다. 
- @RequestBody : httpMessageConverter 단계에서 json 데이터를 걱체료 변경하지 못하면 이후 단계가 진행되지 않아 예외를 발생한다.

## Cookie

### 로그인 상태 유지하기
서버에서 로그인에 성공하면 Http 응답에 쿠키를 담아서 브라우저 전달하자. 그러면 브라우저는 아픙로 해당 쿠키를 지속해서 보내준다.
영속 쿠키 : 만료날짜를 입력하면 해당 날짜까지 쿠키정보 유지
세선쿠기 : 만료날짜를 생략하면 브라우저 종료시까지 유지

### Cookie 보안 문제
- 쿠키 값은 변경 가능
    - client가 쿠키를 강제로 변경하면 다른 사용자가된다. 
- 쿠키에 보관된 정보는 훔처갈 수 있다. 
    - 쿠키에 개인정보가 있다면 심각한 이슈가된다. 
    - 이정보는 웹브라우저에도 보관되고 네트워크 요청마다 계속 클라이언트에서 서버로 전달된다. 
    - 쿠키ㄱ의 정보가 나의 로컬 pc가 털릴수도 있고 네트워크 전송 구간에서 털릴수도 있다. 

### 대안 
- 쿠키에 중요한 값을 노출 하지 않고 서용잡 ㅕㄹ로 에측불가능한 임의 토큰을 노출하고 서버에서 토큰과 사요ㅇ자 id를 매핑해서 인식한다. 그리고 서버에서 토큰을 관리한다. 
- 토큰은 해커가 임의의 값을 넣어도 찾을 수 없도록 예상 불가능해야한다
- 토큰의 만료시간을 짧게 유지한다. 또는 해킹이 의심되는 경우 서버에 해당 톸느을 강제로 제거한다.


## Session
쿠키의 단점을 보완하기위해 세션을 사용할 수 있다. 
세션 메니저의 기능
 1. 세선 생성
 2. 세션 조회
 3. 세션 만료
 3개의 기능으로 구성한다.

 ## Servlet Http 세션
 서블렛은 세션을 위해 `HttpSession` 기능을 제공해는데 직접 구현한 세션의 개념은 이미 구현되어 있다.
 ```java
 //세션이 있으면 있는 세션 반환, 없으면 신규 세션 생성
        //request.getSession() 의 default값은 true
        HttpSession session = request.getSession();
```
request.getSession(true) : 세션이 있으면 기존 세션 반환, 없으면 새로운 세션을 생성해서 반환한다. 
request.getSession(false) : 세션이 있으면 기존 세션 반환, 없으면 새로운 세션을 생성하지 않고 `null` 을 반환한다.

## Tracking lodes
로그인을 처음 시도하면 URL이 `jsessionid` 를 포함하고 있따. 
이것은 웹브라우저가 쿠키를지원하지 않을때 대신 URL을 통해서 세션을 유지하는 방법이다. 이 방법으 ㄹ사용하려면 URL에 이 값을 계속 전달해야되는데 타임리프 같은 ㅊ템플릿 엔진을 통해서 링크를 걸면 jsessionid를 자동으로 포함해준다. URL전달 방식을 끄고 항상 쿠키를 통해서만 세션을 유지하고 싶으면 다음 옵션을 넣어주면된다. 이렇게 되면 URL에 jsessionid가 노출되지 않는다. 
```
application.properties

serverlservlet.session.tracking-mode = cookie
```
### 정리
Session은 최소한의 데이터만 보관해야한다. 보관한 데이터 용량 * 사용자수 로 세션의 메모리 사용량이 급하게 늘어나서 장애로 이어질 수 있다. (기본 30분)정도로 사용 