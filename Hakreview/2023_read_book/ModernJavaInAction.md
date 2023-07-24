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

5.3.2 stream ���ȭ

```java
List<String> uniqueCharacters = 
Words.stream()
.map(word -> word.split(����))
.flatMap(Arrays::stream) // ������ ��Ʈ���� �ϳ��� ��Ʈ������ ���ȭ
.distinct()
.collet(toList());
```
flatMap�� �� �迭�� ��Ʋ���� �ƴ϶� ��Ʈ���� �������� �����Ѵ�. �� map(Arrays::stream)�� �޸� �ϳ��� ���ȭ�� ��Ʈ���� ��ȯ�Ѵ�.

5.4 �˻�, ��Ī
Ư�� �Ӽ��� ���������տ� �ִ��� ���θ� �˻��ϴ� ������ ó���� ���ϴµ� ��Ʈ�� api�� allMatch, anyMatch, noneMatch, findFirst, findAny�� ���� �پ��� ��ƿ��Ƽ �޼��带 �����Ѵ�.

5.4.1 predicate ���
predicate�� �־��� ��Ʈ������ ��� �Ѱ��� ��ġ�ϴ��� Ȯ���Ҷ� anyMatch�� �̿��Ѵ�. 
```java
If(menu.stream.anyMatch(Dish::isVegetarian)){
System.out.printls(��the menu is egetarian freindly��);
}
```
anyMatch�� boolean�� ��ȯ�ϹǷ� ���� �����̴�.

5.4.3 ��� �˻�
FindAny�޼���� ��Ʈ������ ������ ��Ҹ� ��ȯ�Ѵ�.
```java
Optional<Dish> dish = 
Menu.stream()
.filter(Dish::isVegetarian)
.findAny();
```
��Ʈ�� ������������ ���������� ���� �������� ������ �� �ֶǷ� ����ȭ �ȴ�. ����Ŷ�� �̿��ؼ� ����� ã�� ��� ������ �����Ѵ�. 
Optional <T>�� ���� ���翩���� ǥ���ϴ� �����̳� Ŭ������. null�� ���� ������ ����ų �� �����Ƿ� �̸� �̿��ؼ� nullȮ�� ���� ���׸� ���� �� �ִ�.

