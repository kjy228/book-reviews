정리하기전 궁금한 내용 정리된 링크

EnumMap vs HashMap : [https://www.geeksforgeeks.org/difference-between-enummap-and-hashmap/](https://www.geeksforgeeks.org/difference-between-enummap-and-hashmap/)

EnumMap : [https://www.geeksforgeeks.org/enummap-class-java-example/](https://www.geeksforgeeks.org/enummap-class-java-example/)

 

```java
class Plant{
	enum LifeCycle{ ANNUAL, PERENNIAL, BIENNIAL}
	
	final String name;
	final LifeCycle lifeCycle;
	
	Plant(String anme, LifeCycle lifeCycle){
		this.name = name;
		this.lifeCycle = lifeCycle;
	}
	
	@Overide public Stirng toString(){
		return name;
	}
}
```

ordinal()을 배열 인덱스로 사용하지 말것!

잘못된 예시 코드 

```java
Set<Plant>[] plantsByLifeCycle = (Set<Plant>[]) new Set[Plant.LifeCycle.value().length];
for(int i =0 ; i<plantsByLifeCycle.length; i++){
	plantsByLifeCycle[i] = new HashSet<>();
}
for(Plant p : garden)
	plantsByLifeCycle[p.lifeCycle.ordinal()].add(p);

//결과
for(int i = 0 ;i<plantsByLifeCycle.length; i++)
	sout("%s: %s%n", Plant.LifeCycle.values()[i], plantsByLifyCycle[i]);
```

문제점

- 배열은 제네릭과 호환되지 않으니 비검사 형변환을 해야되는데 컴파일이 정상적으로 작동하지 않는 문제 발생
- 인덱스(정수값)을 직접 보증해야한다.

이를 해결하기 위해선? 

EnumMap을 사용하면 된다.

```java
//위와 같은 코드
Map<Plant.LifeCycle, Set<Plant>> plantByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);
for(Plant.LifeCycle lc : Plant.LifeCycle.values())
	plantsByLifeCycle.put(lc, new HashSet<>());
for(Plant p : garden)
	plantByLifeCycle.get(p.LifeCycle).add(p);
sout(plantByLifeCycle);
```

안전하지 않은 형변환은 쓰지 않고 맵의 키인 열거타입이 그 자체로 출력용 문자열을 제공해 직접 레이블을 달지 않아도 된다. 

EnumMap 이 더 안전한 이유는?

Map은 내부 구현방식을 안으로 숨겨서 타입 안정성과 성능을 모두 얻어낼 수 있다. 

```java
//stream사용 - EnumMap사용x
Sout(Arrays.stream(garden).collect(groupingBy(p->p.lifeCycle)));
```

stream을 사용하면 코드는 단순해지미나 EnumMap이 아닌 고유한 맵 구현체를 사용했기 때문에 EnumMap을서서 얻은 공간, 성능 이점이 사라지게된다. 

```java
//EnumMap을 이용해 데이터와 열거 타입 매핑
Sout(Arrays.strean(garden).collect(groupingBy(p-> p.LifeCycle,
		() -> new EnumMap<>(LifyCycle.class), toSet())));

```

EnumMap만 사용하게되면 식물의 생애주기당 하나씩 중첩 맵을 만들지만 스트림버전에서는 해당 생애주기에 속하는 생물이 있을때만 만든다. 

```java
//지양해야되는 코드 
public enum Phase{
	SOLID, LIQUID, GAS;
	public enum Transition{
	    MELT, FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;
    
	    private static final Transition[][] TRANSITIONS={
	            {null, MELT, SUBLIME},
	            {FREEZE, null, BOIL},
	            {DEPOSIT, CONDENSE,null}
	    };
    
	    //한 상태에서 다른 상태로의 전이를 반환
	    public static Transition from(Phase from , Phase to){
	        return TRANSITIONS[from.ordinal()][to.ordinal()];
	    }
	}
}
```

정원 예제와 마찬가지로 컴파일러는 ordinal, 인덱스의 관계를 알 수 없다. 

```java
public enum Phase {
		SOLID,LIQUID,GAS;
		public enum Transition {
			MELT(SOLID,LIQUID),FREEZE(LIQUID,SOLID),BOIL(LIQUID,GAS),CONDENSE(GAS,LIQUID),SUBLIME(SOLID,GAS),DEPOSIT(GAS,SOLID);
        private final Phase from;
        private final Phase to;

        Transitino(Phase from, Phase to){
            this.from = from;
            this.to = to;
        }

        private static final Map<Phase, Map<Phase, Transition>>
m= Stream.of(values().collect(groupingBy(t -> t.from,() -> new EnumMap<>(Phase.class),
                toMap(t-> t.to, t->t, (x,y) -> y, () -> new EnumMap<>(Phase.class )))));
        public static Transition from(Phase from, Phase to) {
            returnm.get(from).get(to);
        }
    }
}
//Map<Phase, Map<Phase, Transition>>  : 이전상테에서 이후 상태에서 전이정보
//groupBy : 전이를 이전 상태를 기준으로 묵고 두번째 tpMapㅇ에서 이후 상태를 전이에 대응시키는 EnumMap 생성 
//(x,y)-> y : 실제 사용 x,EnumMap을 얻기위해 맵 팩터리가 필요하고 수집기들은 점층적 펙토리를 제공하기 때문 
```

배열의 인덱스를 얻기 위해 ordinal을 쓰는 것은 일반적으로 좋지 않으니 대신 EnumMap을 사용해라 

ordinal() 사용 예제 

```java
package com.effectiveStudy;

public enum Ensemble {
SOLO,DUET,TRIO,QUARTET,QUINTET,SEXTET,SEPTET,OCTET,NONET,DECTET;
    public int numberOfMusicians(){return ordinal()+1;}
}

package com.effectiveStudy;

import static com.effectiveStudy.Ensemble.DECTET;
import static com.effectiveStudy.Ensemble.DUET;

public class testMain {
    public static void main(String[] args) {
        System.out.println(DECTET.numberOfMusicians());
    }
}

```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ed17edc2-45ea-4558-9cbb-4c9bdf2d63ae/Untitled.png)

[EnumMap, HashMap 차이](https://siyoon210.tistory.com/142)