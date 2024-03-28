# Function

## Function<T,R>

T를 인풋으로 받아서 R을 리턴한다. 

```java

public class FunctionTest {
    public static void main(String[] args) {
        Function<Integer, String> a = (i)-> "안녕하세요 " + i;
        System.out.println(a.apply(5));
    }
}
```


## Function method

- andThen
람다함수객체의결과를 인풋으로 받아서 다른 람다 객체의 함수를 실행시킨다.


- compose
compose 의 잇풋을 실행한 결과를 다른 Function의 인풋으로 결과를 계산한다.


```java
public class FunctionTest {
    public static void main(String[] args) {
         Function<Integer, Integer> plus10 = (i)->i+10;
        Function<Integer, Integer> multiply2 = (i) -> i*2;


        System.out.println(plus10.andThen(multiply2).apply(2));

        System.out.println(plus10.compose(multiply2).apply(2));
    }
}
```
이외에도 Consumer, Supplier, Predicate등의 함수가 있다.