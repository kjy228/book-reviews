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
                .roles("ROLE_USER")
                .build();
        return new InMemoryUserDetailsManager(user);
    }

}

```