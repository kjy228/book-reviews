# Generic

제네릭 클래스, 제네릭 인터페이스를 팝쳐 제네릭 타입이라고 한다. 예를들어 List는 원래 List<E> 이다. 

제네릭이 나오기전 코드 

```
private final Collection stamps = (Stamp)(1234);
private final Collection stamps = (Coin)(1234);
````
이렇게 코드를 작성하게되면 stamps에 어떤 타입의 객체값이 들어와더 컴파일 에러가 나지 않기때문에 위험하다. 

```
static int numElementsincommon(Set s1, Set s2){
    int result =0;
    for(object o1 : s1){
        if(s2.contains(o1))result++;
        return result;
    }
}
````
동작은 하지만 row 타입을 사용해 안전하지 않다. 따라서 비한정적 와일드카드 타입을 대신 사용하는게 좋다. 제네릭 타입을 쓰고싶지만 실제 매개변수를 신경쓰고싶지 않다면 ?를 사용하자 (Set<?>)
Set<E> 의 비한정적 와일드카드 타입은 Set<?> 이다. 
```
static int numElementsInCommon(Set<?> s1, Set<?> s2){...}
````

그렇다면 Set<?>와 Set<E> 의 차이는? 
와일드카드 타입은 안전하고 로 탕비은 안전하지 않다! -> 로 타입 컬렉션은 아무 원소나 넣을 수 있기 때문에 타입 불변식을 휴ㅔ손하기 쉽다.

<img width="532" alt="image" src="https://user-images.githubusercontent.com/43670838/178624345-e47dbad6-c9f4-4009-a6b6-1762c380a631.png">
