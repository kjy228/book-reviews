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

