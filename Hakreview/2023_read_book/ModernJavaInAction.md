����ڹ��ξ׼� ������

5.1 stream filtering

Filter�޼���� predicate(boolean	��ȯ)�� ����� �� ������ distinct()�� ����Ͽ� �ߺ����� �� �� �ִ�. 
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
filter������ �̿��ϸ� ��ü ��Ʈ���� �ݺ��ϸ鼭 ����ҿ� predicate�� �����Ѵ�. 
����Ʈ�� �̹� ���ĵǾ��ִٸ� takeWhile�� �̿��Ͽ� �����ϰ� ó���� �� �ֵ�. 

```java
List<Dish> slicemenu1 = specilaMenu.stream()
.takeWhile(dish -> dish.getCalories()<320)
.collect(toList());
``` 

```java
// ���� 3���� �̱�
List<Dish> slicemenu1 = specilaMenu.stream()
.filter(dish-> dish.getCalories()>320)
.limit(3)
.collect(toList());
``` 

```java
//����2���� �ǳʶٰ� slicemenu1 �� ���

List<Dish> slicemenu1 = specilaMenu.stream()
.filter(dish-> dish.getCalories()>320)
.skip(2)
.collect(toList());
``` 

