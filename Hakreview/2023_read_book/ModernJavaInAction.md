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

- isPresnet() : Optional���� �����ϸ� true, ������ false 
- T get() : ���� �����ϸ� ���� ��ȯ, ���� ������ noSuchelementException
- T orEle() : ���� ������ ��ȯ�ϰ� ������ �⺻���� ��ȯ�Ѵ�.

5.4.4 ù���� ��� ã��

``` java

List<Integer> somNumbrs = Arrays.asList(1,2,3,4,5);
Optional<Integer> firstSquareDivisibleByThree = someNumbers.stream().map(n-> n*n)
                                                .filter(n-> n%3 ==0)
                                                .findFirst();

```

5.5 Reducing
���ེ�� ����Ҷ��� '�޴��� ��� Į�θ� ��', '�޴����� Į�θ��� ���� ���� �丮��?' ���� ��Ʈ�� ��Ҹ� �����ؼ� �� ������ ���Ǹ� ǥ���ϴ� ����̴�. 
5.5.1 ����� ��
```java
int sun =0;
for(int x : numbers){
    sum +=x;
}
```

���� for-each ���� ������ ������ �ΰ�����Ͽ� �ݺ������� ���ϴ� �ڵ��̴�. �� �ڵ带 ���ེ �ڵ�� ��ȯ����

```java 
List<Integer> numbers = Arrays.asList(4,5,3,9);
int sum = numbers.stream().reduce(0, (a,b) -> a+b);
```
���ེ�� �ΰ��� �μ��� ������.
- �ʱ갪 0 
- �� ��Ҹ� �����ؼ� ���ο� ���� ����� BinaryOperator<T>. ������ ���� ǥ������ ����ߴ�. 
reduce�� ����� �ڵ忡�� ù��° �Ķ���� a�� 0 ���� �ʱ갪�� ����߰� 0 + b(4)�� ���Ŀ� ���������� +5 , +3 , +9�� �Ѵ�. 
���� �ڵ�� Integer class�� �� ���ڸ� ���ϴ� ���� �޼��带 ����ϸ� ����ȭ �� �� �ִ�. 
```java 
List<Integer> numbers = Arrays.asList(4,5,3,9);
int sum = numbers.stream().reduce(0, Integer::sum);
```

reduce�� �ʱ갪�� ���� �ʵ��� �� �� �ִµ� Optional ��ü�� ��ȯ�Ѵ�.
```java 
List<Integer> numbers = Arrays.asList(4,5,3,9);
int sum = numbers.stream().reduce((a,b) -> (a+b));
```
Optional�� ��ȯ�ϴ� ������ ��Ʈ���� �ƹ� ��ҵ� ���� ��Ȳ�϶� �ʱ갪�� �����Ƿ� reduce�� �հ踦 ��ȯ�� �� ����. ���� �հ谡 ������ ����ų�� �ֵ��� Optinal ��ü�� ���� ����� ��ȯ�Ѵ�. 
