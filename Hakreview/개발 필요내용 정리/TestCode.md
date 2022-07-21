## Junit
TDD
 테스트 주도 개발이라는의미
 테스트토드를 먼저 작성하고 이에 맞춰 테스트코드를 통과할 수 있도록 코드를 짜는것을 의미한다
 애자일 개발 방식죽 하나이며
 - 코드 설계시 원하는 단계적 목표에 대해 설정하여 진행하고자 하는것에 대한 결정 방향의 갭을 줄이고자함
 - 최초 목표에 맞춘 테스트를 구축하여 그에 맞게 코드를 설계하기 때문에 보다 적은 의견 충동을 기대할 수 있음.
## Junit이란?
java진영의 대표적인 test framwork
단위테스트를 위한 도구 제공
@을 기반으로 테스트 지원
Assert로 테스트 케이스의 기대값에 대해 수행결과 확인 가능
Junit 5는 Jupiter, Platform, Vintage모듈로 구성됨.
![image](https://user-images.githubusercontent.com/43670838/170420886-83a0eb93-faf3-401c-8eeb-3131d0c6409a.png)
- Junit Jupiter
TestEngine API 구현체
테스트의 실제구현체는 별도 모듈 역할을 수행하는데 그 모듈 중 하나가 Jupiter-Engine임
이 모듈은  Jupiter-API를 사용하여 작성한 테스트 코드를 발견하고 실행하는 역할을 수행
개발자가 테스트 코드를 작성할때 사요됨
- Junit Platform
Test를 실행하기 위한 뼈대
Test를 발견하고 테스트 계획을 생성하는 TestEngine 인터페이스를 가지고 있음
TestEngine을 통해 Test를 발견하고 수행 및 결과를 보고함
그리고 각종 IDE연동을 보조하는 역할을 수행( 콘솔 출력)
- Junit Vintage
TestEngine API구현체로 Junit3,4를 구현하고 있음
기존 3,4 버전으로 작성된 테스트 코드를 실행할때 사용됨
Vintage-Engin 모듈을 포함하고 있음
### Life Cycle Annotation
@Test : 테스트용 메서드를 표현하는 어노테이션
@BeforeEach : 각 테스트 메서드 시작전에 실행되어야 하는 메서드를 표현
@AfterEach : 각 테스트 메서드가 시작된 후 실행되어야 하는 메서드를 표현
@BeforeAll : 테스트 시작전에 실행되어야 하는 메서드를 표현 (static 처리 필요)
@AfterAll : 테스트 종료 후에 실행되어야 하는 메서드를 표현(static 처리 필요)
### Junit Main Annotation
@SpringBootTest
- 통합 테스트 용도로 사용됨
- @SpringBootApplication을 찾아가 하위의 모든 Bean을 스캔하여 로드함
- 그 후 test용 Application Context를 만들어 Bean을 추가하고, MockBean을 찾아 교체
@ExtendWith
- Junit4에서 @RunWith로 사용되던 어노테이션을 대채함
- @ExtendWith는 메인으로 실행될 class를 지정할 수 있음
- @SpringBootTest는 기본적으로 @ExtendsWith가 추가되어 있음
@WebMvcTest(Class명.class)
- ()에 작성된 클래스만 실제로 로드하여 테스트를 진행
- 매개변수를 지정해주지 않으면 @Controller, @RestController, @RestControllerAdvice등 컨트롤러와 연관된 Bean이 모두 로드됨
- 스프링의 모든 Bean을 로드하는 @SpringBootTest대신 컨트롤러 관련 코드만 테스트할 경우 사용
@Autowired about MockBean
- Controller API를 테스트하는 용도인 MockMvc 객체를 주입받음
- perform()메서드를 활요하여 컨드롤러의 동작을 확인할 수 있음
- .andExpect(), andDo(), andReturn()등의 메서드를 같이 활용함
@MockBean
- 테스트를 할 클래스에서 주입 받고 있는 객체에 대해 가짜 객체를 생성해주는 어노테이션
- 해당 객체는 실제 행위를 하지 않음
- given()메서드를 활용하여 가짜객체의 동작에 대해 정의하여 사용할 수 있음
@AutoConfigureMockMvc
- spring.test.mockmvc의 설정을 로드하면서 MockMvc의 의존성을 자동으로 주입
- MockMvc클래스는 REST API테스를 할 수 있는 클래스
### 단위테스트 의 FIRST원칙
 - Fast : 테스트 코드의 실행은 빠르게 진행되어야 함
 - Independent : 독립적인 테스트가 가능해야 함
 - Repeatable : 테스트는 매번 같은 결과를 만들어야 함
 - Self-Validating : 테스트는 그자체로 실행하여 결과를 확인할 수 있어야 함
 - Timely : 단위 테스트는 비즈니스 코드가 완성되기 전에 구성하고 테스트가 가능해야 함 -> 코드가 완성되기 전부터 테스트가 따라와야 한다는 TDD의 원칙을 담고 있음


