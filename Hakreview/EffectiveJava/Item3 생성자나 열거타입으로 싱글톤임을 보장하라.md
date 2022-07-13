# Item3 생성자나 열거타입으로 싱글톤임을 보증하라

싱글턴 : 오직 한개의 인스턴스를 생성

하지만 이의 단점으로 클라단에서 테스트하기 어려워질 수 있다. 

```java
public class Elvis{
	public static final elvis Instance = new Elvis();
	private Elvis(){}

	public void method1(){}
}
```

private 생성자는 public static final필드인 Elvis.Instance를 초기화할 때 닥 한번만 호출된다. 이렇게되면 클라단에서 손쓸 방법이 없다. 

장점

- 해당 클래스가 싱글턴임이 API에 명백히 드러난다는것
- public static 필드가 final이니 절대로 다른 객체를 참조할 수 없다.
- 간결함

```java
public class Elvis{
	private static final Elvis INSTANCEW = new Elvis();
	private Elvis(){}
	public static Elvis getInstance(){return INSTANCE;}
	public void method1(){}
}
```

Elvis.getInstance는 항상 같은 객체의 참조를 반환하므로 제2의 Elvis인스턴스란 결코 만들어지지 않는다 

장점

- API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
- 유일한 인스턴스를 반환하던 팩터리 메서드가 호출하는 스레드별로다른 인스턴스를 넘겨주게 할 수있다.