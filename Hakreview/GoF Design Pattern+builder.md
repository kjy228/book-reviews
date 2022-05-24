패스트캠퍼스 강의를 들으면서 "builder" 가 아니라 "builder pattern"이라고 설명하는 부분이 있어서 왜 패턴이지? 라는 생각을하며 구글링을 하게 되었습니다.
제가 써본건 MVC패턴뿐인데 어떤패턴이지? 라는 궁금증이 생겨 검색해보니 GoF(Gang of Four)디자인 패턴 부터 공부가 필요할것같아 신규 입사자분들에게 조금이나마 도움이되고자 정리하기로 했습니다:)
강의에서 필요한것은 **builder pattern**에 대한 이해이므로 GoF에 대해서는 간략하게만 정리하도록 하겠습니다.

본 문서는
https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EB%94%94%EC%9E%90%EC%9D%B8_%ED%8C%A8%ED%84%B4

https://4z7l.github.io/2020/12/25/design_pattern_GoF.html
를 참고하여 작성하였습니다.

## GoF란?
디자인 패턴( 반복적으로 일어나는 문제들을 어떻게 풀어나갈 것인가에 대한 일종의 솔루션)의 한 종류입니다.
다른 디자인 패턴으로는 아래 사진과 같은 것들이 있습니다.




GoF는 creational 패턴(5개), structural패턴(7개), behavioral(11개) 패턴 3가지로 구분하고 있습니다.

### behavioral 패턴
클래스나 객체들이 서로 상호작용하는 방법이나 책임 분배 방법을 정의하는 패턴입니다.
행위 패턴은 하나의 객체로 수행할 수 없는 작업을 여러 객체로 분배하며 결합도를 최소화 할 수 있도록 도와줍니다.

- Chain of Responsibility : 요청을 처리할 수 있는 객체가 둘 이상 존재하여 한 객체가 처리하지 못하면 다음 객체로 넘어가는 형태의 패턴입니다.
- Command : 요청읠 객체의 형태로 캡슐화하여 재이용하거나 취소할 수 있도록 요청에 필요한 정보를 저장하거나 로그에 남기는 패턴입니다.
- Interpreter : 언어에 문법 표현을 정의하며 SQL, 통신 프로토콜과 같은 것을 개발할 때 사용합니다.
- Iterator : 접근이 잦은 객체에 대해 동일한 인터페이스를 사용하도록 하는 패턴입니다.
- mediator :  수 많은 객체들 간의 복잡한 상호작용을 캡슐화하여 객체로 정의하는 패턴입니다. 객체사이의 의존성을 줄여 결합도를 감소시킬 수 있습니다.
- Memento : 특정 시점에서의 객체 내부 상태를 객체화하여 이후 요청에 따라 객체를 해당 시점의 상태로 돌릴 수 있는 기능을 제공합니다.
- Observer : 한 객체의 상태가 변화하면 객체에 상속되어있는 다른 객체들에게 변화된 상태를 전달하는 하는 패턴입니다. pub,sub할때 주로 사용합니다.
- State : 객체의 상태에 따라 동일한 동작을 다르게 처리해야 할때 사용하는 패턴입니다.
- Strategy : 동일한 계열의 알고리즘들을 개별적으로 캡슐화하여 상호 교환할 수 있게 정의하는 패턴입니다.
- Template Method : 상위 클래스에 골격을 정의하고 하위클래스에서 세부처리를 구체화하는 구조의 패턴이며 대표적인 상속으로 볼 수 있습니다.
- Visitor : 각 클래스들의 데이터 ㄱ조에서 처리 기능을 분리하여 별도의 클래스로 구성하는 패턴입니다. 분리된 처리기능은 각 클래스를 방문하여 수행합니다.
### structural 패턴
클래스나 객체들을 조합하여 더 큰 구조로 만들 수 있게 해주는 패턴입니다. 이는 객체를 합성하는 방법을 정의합니다.
- Adapter : 클래스의 인터페이스를 다른 인터페이스로 변환하여 다른 클래스가 이용할 수 있도록 합니다.
- Bridge : 구현부에서 추상층을 분리하여 각자 독립적으로 확장할 수 있게 합니다.
- Composite : 객체들의 관계를 트리 구조로 구성하여 복합 객체와 단일 객체를 구분없이 다룹니다.
- Decorator : 주어진 상황 및 용도에 따라 어떤 객체에 다른 객체를 덧붙이는 방식입니다.
- Facade : 주어진 상황 및 용도에 따라 어떤 객체에 다른 객체를 덧붙이는 방식
- Flyweight : 크기가 작은 여러개의 객체를 매번 생성하지 않고 가능한 한 공유할 수 있도록 하여 메모리를 절약합니다
- Proxy : 접근이 어려운 객체로 접근을 제어하기 위해 객체의 Surrogate, Placeholder를 제공합니다.

### creational 패턴
 객체의 생성과 관련된 패턴입니다. 객체 생성과 참고 과정을 캡슐화하여 객체가 생성되거나 변경되어도 프로그램 구조에 양항을 받지 않도록 하여 프로그램에 유연성을 더할 수 있습니다.
이 패턴은 현재 상속보다 조합,복합의 방법을 사용하는 방향으로 변화되고있습니다.

 - 팩토리 패턴 : 객체를 생성하는 인터페이스는 미리 정의하되 인스턴스를 만들 클래스의 결정은 서브 클래스 쪽에서 내리는 패턴입니다. 즉 여러 개의 서브 클래스를 가진 슈퍼 클래스가 있을때 인풋에 따라 하나의 자식 클래스의 인스턴스를 리턴해주는 방식입니다.
 - 추상 팩토리 패턴 : 인풋으로 서브클래스에 대한 식별 데이터를 받는것이 아니라 또 하나의 팩토리 클래스를 받습니다.
 - 싱글톤 패턴 : 싱글톤 패턴은 어떤 클래스의 인스턴스가 오직 **하나** 임을 보장하며 이 인스턴스에 접근할 수 있는 전역적인 접촉점을 제공하는 패턴입니다.
 - 프로토타입 패턴 : 원본 객체를 복사함으로써 객체를 생성하는 패턴입니다.
 - **빌더 패턴** :복잡한 객체를 생성하는 방법을 정의하는 클래스와 표현하는 방법을 정의하는 클래스를 별도로 분리하여 서로 다른 표현이라도 이를 생성할 수 있는 동일한 절차를 제공하는 패턴입니다. 빌더 패턴에 대해서는 조금더 자세하게 알아보도록 하겠습니다.


--------------------


## Builder pattern
빌더 패턴은 생성자에 들어갈 매개 변수가 몇개이든지간에 차례대로 매개 변수를 받아들이고 모든 매개변수를 받은 뒤에 이 변수들을 통합해서 한번에 사용을 합니다.

그렇다면 생성자, setter도 있는데 왜 builder를 사용하는 걸까요?

### builder 사용 하는 이유
- 필요한 데이터만 설정할 수 있습니다.
 
Restaurant객체 사용 예제
```

```java
 Restaurant restaurant = Restaurant.builder()
                .name("BeRyong")
                .address("Busan")
                .build();
 Restaurant saved = Restaurant.builder()
                .id(1234L)
                .name("BeRyong")
                .address("Busan")
                .build();
```
Restaurant.java
```java
@Entity
@Getter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class Restaurant {
 
    @Id
    @GeneratedValue
    private Long id;
    private String name;
    private String address;
    @Transient
    private List<MenuItem> menuItems;
 
    public Restaurant(String name, String address) {
        this.name = name;
        this.address = address;
 
    }
    public Restaurant(Long id, String name, String address) {
        this.id = id;
        this.name = name;
        this.address = address;
    }
 
    public List<MenuItem>getMenuItems(){
        return menuItems;
    }
 
 
    public String getInformation() {
        return name + " in " + address;
    }
 
    public void addMenuItem(MenuItem menuItem) {
 
        menuItems.add(menuItem);
    }
 
    public void setId(long id) {
        this.id = id;
    }
 
    public void setMenuItem(List<MenuItem> menuItems) {
        this.menuItems = new ArrayList<>(menuItems);
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public void updateInformation(String name, String address) {
        this.name = name;
        this.address = address;
    }
}
```



위 코드처럼 id없이 Restaurnat 객체를 사용할 수 있습니다.
생성자를 사용하게되면 파라미터별로 생성자를 만들어줘야하는 번거로움이 있기때문에 이 부분에서 builder()를 사용하는것이 더 유용한것 같습니다.

- 가독성을 높일 수 있다.
빌더 패턴을 사용하면 매개변수가 많아져도 가독성을 높일 수 있습니다. 생성자로 객체를 생성하면 매개변수가 많을 수록 값이 어떤의미인지 알기 어렵기 때문에 코드 가독성이 좋지 않습니다.
