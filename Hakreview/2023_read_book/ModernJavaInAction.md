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

