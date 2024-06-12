# Spring Security

## FilterChainProxy
FilterChainProxy는 아래 16개의 필터를 순서대로 돌면서 요청을 처리한다. 마지막 인가처리 필터까지 예외나 오류가 없으면 요청이 서블릿으로 넘어가게 된다.

```
- DisableEncodeFilter
- WebAsyncManagerIntegrationFilter
- SecurityContextHolderFilter
- HeaderWriterFilter
- CorsFilter
- CsrfFilter
- LogoutFilter
- UsernamePasswordAuthenticationFilter
- DefaultLoginPageGeneratingFilter
- DefaultLogoutPageGeneratingFilter
- BasicAuthenticationFilter
- RequestCacheAwareFilter
- SecurityContextHolderAwareReqeustFilter
- AnonymousAuthenticationFilter
- ExceptionTranslationFilter
- AuthorizationFilter
```

## 사용자 정의 보안설정 
- @EnableWebSecurity를 클래스에 선언해야한다 
- 모든 설정은 람다형식으로해야한다 (스프링 시큐리티 7버전부터는 람다 형식만 지원할 예정)
- SecurityFilterChain을 빈으로 정의하게되면 자동설정에 의한SecurityFilterChain 빈은 생성되지 않는다.

## 인증 프로세스
### Form 인증
 - http 기반의 폼로그인 인증 메커니즘을 활성화하는 API로서 사용자 인증을 위한 사용자 정의 로그인 페이지를 쉽게 구현할 수 있다.
 - 기본적으로 스프링 시큐리티가 제공하는 기본 로그인 페이지를 사용하며 사용자 이름과 비밀번호 필드가 포함된 로그인양식 제공
 - 사용자는 웹 폼을 통해 자격증명(Id, Password)을 제공하고 SpringSerucity는 HttpServletRequest에서 이 값을 읽어온다.

### Form 인증 Flow
 endPoint로 접근 -> 권한검색필터(AuthorizationFilter) -> 예외발생(AccessDeniedException) -> 예외 처리 필터(ExceptionTranslationFilter)
 -> 인증시작(AuthenticaitonEntryPoint) -> 로그인페이지로 리다이렉트

 <img width="1494" alt="image" src="https://github.com/myounghaklee/OAUTH2/assets/43670838/8d6ea29f-3d2e-405a-b9ee-05047ba609bf">

```java

@EnableWebSecurity
@Configuration
public class SecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception{
        http
                .authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
                .formLogin(form -> form.loginPage("/loginPage")
                        .loginProcessingUrl("/loginProc")
                        .defaultSuccessUrl("/", true)
                        .failureUrl("/failed")
                        .usernameParameter("userId")
                        .passwordParameter("passwd")
                        .successHandler(new AuthenticationSuccessHandler() {
                            @Override
                            public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
                                System.out.println("authentication : " + authentication);
                                response.sendRedirect("/home");
                            }
                        })
                        .failureHandler(new AuthenticationFailureHandler() {
                            @Override
                            public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {
                                System.out.println("exception : " + exception.getMessage());
                                response.sendRedirect("/loginPage");
                            }
                        })
                        .permitAll()

                );
        return http.build();
    }

    @Bean
    public UserDetailsService userDetailsService(){
        UserDetails user = User.withUsername("user1")
                .password("{noop}1111")
                .roles("USER")//ROLE_ prefix는 자동으로 붙음
                .build();
        return new InMemoryUserDetailsManager(user);
    }

}

```

### UsernamePasswordAuthenticationFilter
- AbstractAuthenticationProcessingFilter 클래스를 사용자의 자격 증명을 인증하는 기본 필터로 사용한다. 
- UsernamePasswordAuthenticationFilter는 AbstractAuthenticationProcessingFilter를 확장한 클래스로서 HttpServletRequest에서 제출된 사요자 이름과 비밀번호로 부터 인증을 수행하낟. 
- 인증 프로세스가 초기화 될대 로그인 페이지와 로그아웃 페이지 생성을 위한 DefaultLoginPageGeneratingFilter 및 DefaultLogoutPageGeneratingFilter가 초기화 된다.
<img width="1125" alt="image" src="https://github.com/myounghaklee/book-reviews/assets/43670838/248cbed3-b66d-44d4-8e68-64d1a09f734d">


### Http Basic 인증
- 액세스 제어와 인증을 위한 프레임워크를 제공하며 가장 일반적인 인증방식은 Basic 인증 방식
- 인증 프로토콜은 http 인증 헤더에 기술되어 있다.
- Http Basic 인증은 반드시 https와 같이 TLS기슬과 함꼐 사용해야한다.

<b>순서</b>
1. 클라이언트는 인증정보 없이 서버로 접속 시도 
2. 서버가 클라이언트에게 인증요구 보낼떄 401 응답과 함꼐 WWW-Authenticat헤더를 기술해어 realm(보안영역)과 basic 인증방법을 보냄
3. 클라이언트가 서버로 접속할때 base64로 username, password를 인코딩하고 Authroization헤더에 담아서 요청
4. 성공적으로 완료되면 정상적인 상태코드를 반환

### BasicAuthenticatinoFilter
- 기본 인증 서비스를 제공하는데 사용된다. 
- BasicAuthenticationConverter를 사용해서 요청 헤더에 기술된 인증 정보의 유효성을 체크하며 Base64인코딩된 username, password를 추출한다. 
- 인증 이후 세션 사용하는 경우와 사용하지 않는 경우에 따라 처리되는 흐름에 차이가 있으며 세션을 사용하는 경우 매 요청마다 인증과정을 거치지 않으나 세션을 사용하지 않는 경우 매 요청마다 인증과정을 거쳐야 한다.


### RememberMe 인증
- 사용자가 웹 사이트나 애플리케이션에 로그인할때 자동으로 인증정보 기억
- UsernamePasswordAuthenticationFilter와 함께 사용되며 AbstractAuthenticationProcessingFilter 슈퍼클래스에서 훅을 통해 구현
    - 인증 성공시 : RememberMeServices.loginSuccess()를 통해 Rememberme토큰을 생성하고 쿠키로 전달
    - 인증 실패시 : RememberMeServices.loginFail()을 통해 쿠키를 지운다
    - LogoutFilter와 연계해서 로그아웃시 쿠키를 지운다

<b>토큰 생성</b>
- 기본적으로 암호화된 토큰으로 생성되며 쿠키를 보내고, 후에 세션에서 이 쿠키를 감지하여 자동 로그인이 이루어지는 방식으로 달성된다.
<b>RememberMeServices 구현체</b>
- TokenBasedRememberMeServices : 쿠키 기반 토큰의 보안을 위해 해싱을 사용한다.
- PersistentTokneBasedRememberMeServices : 생성된 초큰을 저장하기 위해 데이터베이스나 영구 저장매체 사용
- 두 구현 모두 사용자의 정보를 검색하기 위한 UserDetailservice 필요

### RememberMeAuthenticationFilter
- SecurityContextHolder에 Authentication이 포함되지 않은경우 실행되는 필터