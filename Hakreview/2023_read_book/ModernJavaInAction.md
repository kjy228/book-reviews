모던자바인액션 정리글

5.1 stream filtering

Filter메서드는 predicate(boolean	반환)를 사용할 수 있으며 distinct()를 사용하여 중복제거 할 수 있다. 
```java
List<Integer> numbers = Arrays.asList(1,2,1,3,3,4);
 Numbers.stream()
.filter(i-> i%2 == 0)
.distinct()
.forEach(System.out::println);

```

5.2 stream slicing
5.2.1  slicing using predicate

```java
List<Dish> filterdMenu = 
SpecilaMenu.stream()
.filter(dish -> dish.getCalories()<3000)
.collecdt(toList());
```
filter연산을 이요하면 전체 스트림을 반복하면서 각요소에 predicate를 적용한다. 
리스트가 이미 정렬되어있다면 takeWhile을 이용하여 간단하게 처리할 수 있따. 

```java
List<Dish> slicemenu1 = specilaMenu.stream()
.takeWhile(dish -> dish.getCalories()<320)
.collect(toList());
``` 

```java
// 최초 3개만 뽑기
List<Dish> slicemenu1 = specilaMenu.stream()
.filter(dish-> dish.getCalories()>320)
.limit(3)
.collect(toList());
``` 

```java
//최초2개는 건너뛰고 slicemenu1 에 담기

List<Dish> slicemenu1 = specilaMenu.stream()
.filter(dish-> dish.getCalories()>320)
.skip(2)
.collect(toList());
``` 

5.3.2 stream 평면화

```java
List<String> uniqueCharacters = 
Words.stream()
.map(word -> word.split(“”))
.flatMap(Arrays::stream) // 생성된 스트림을 하나의 스트림으로 평면화
.distinct()
.collet(toList());
```
flatMap은 각 배열의 스틀미이 아니라 스트림의 콘텐츠로 매핑한다. 즉 map(Arrays::stream)과 달리 하나의 평면화된 스트림을 반환한다.

5.4 검색, 매칭
특정 속성이 데이터집합에 있는지 여부를 검색하는 데이터 처리를 말하는데 스트림 api는 allMatch, anyMatch, noneMatch, findFirst, findAny와 같은 다양한 유틸리티 메서드를 제공한다.

5.4.1 predicate 사용
predicate가 주어진 스트림에서 적어도 한개와 일치하는지 확인할때 anyMatch를 이용한다. 
```java
If(menu.stream.anyMatch(Dish::isVegetarian)){
System.out.printls(“the menu is egetarian freindly”);
}
```
anyMatch는 boolean을 반환하므로 최종 연산이다.

5.4.3 요소 검색
FindAny메서드는 스트림에서 임의의 요소를 반환한다.
```java
Optional<Dish> dish = 
Menu.stream()
.filter(Dish::isVegetarian)
.findAny();
```
스트림 파이프라인은 내부적으로 단일 과정으로 실행할 수 있또록 최적화 된다. 숏서킷을 이용해서 결과를 찾는 즉시 실행을 종료한다. 
Optional <T>는 값의 존재여무를 표현하는 컨테이너 클래스다. null은 쉽게 에러를 일으킬 수 있으므로 이를 이용해서 null확인 관련 버그를 피할 수 있다.

