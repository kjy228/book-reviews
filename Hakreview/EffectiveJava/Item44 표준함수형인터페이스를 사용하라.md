Java8부터 람다를 지원하면서 API를 작성하는 사례가 바뀌었다. 함수형 인터페이스를 들어가기전에 람다부터 설명하고 시작하겠다.

**람다식이란?**

- 함수형 프로그램이 구현방식
- 클래스를 생성하지 않고 함수의 호출만으로 기능 수행
- 함수형 인터페이스를 선언함

함수형 프로그래밍이란?

- 순수 함수(pure function)를 구현하고 호출
- 매개변수만을 사용하도록 만든 함수로 외부 자료에 부수적인 영향을 발생하지 않도록함
- 입력 받은 자료를 기반으로 수행되고 외부에 영향을 미치지않아 병렬처리가 가능함
- `@FunctionalInterFace` 를 사용하면 메서드 한개만 선언할 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/66292178-2bd9-454c-a663-e24794cb1109/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f5c99a09-59d8-4cc6-a615-a12138718e9e/Untitled.png)

java.util.function 에서 제공하는 함수적 인터페이스는 Consumer, Supplier, Function, Operator, Predicate로 구분된다. 

[참고](https://palpit.tistory.com/673)

### ConSumer 함수적 인터페이스

리턴값이 없는 accept()메서드를 가지고 잇다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e9b96d78-9d8d-4b35-86af-cdba14b04972/Untitled.png)