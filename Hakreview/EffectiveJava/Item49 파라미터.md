
자바 7 추가 메서드
requireNonNull 메서드 : 유용하고 사용하기 편하며 더이상 null검사 수동ㅇ으로 하지 않아도됨
```
this.strategy = Objects.requireNonNull(strategy, "전략");
```

## requireNonNull vs Optional

### 목적 
requireNonNull : 참조가 null일 경우 개발자에게 알리는것이 목
Optional : null일지도 모르는 값을 처리하는데 목적

### 사용 형태
Optional은 리턴타입에 사용되는 것이 권장된다.
requireNonNull은 메서드 내부에서 큰 제약 없이 사용될 수 있다.

자바9 부터는 requireNonNullElseGet 메서드추가됨으로써 optional처럼 사용 가능해졌다.

자바 9 메서드 
```
checkFromIndexSize
checkFromToIndex
checkIndex
```

# Item50_적시에 방어적 복사본을 만들라
- 클라이언트가 불변을 깨뜨리려한다는 가정하에 방어적으로 프로그래밍하라.
- 어떤 객체든 허락없이 외부에서 내부를 수정하는일 불가능하게 하라 -> 현재 setter 지양, dto감싸기, final 변수 사용하는것이 적절한것 같
불변성을 지키기위해 Date같은 늙은 API사용 하지마라잉
- 외부 공격으로부터 인스턴스 내부를 보호하기위해 생성자에서 받은 가변 파라미터 각가을 defensive copy해야한다. 그 후 원본이아닌 복사본 인스턴스를 사용하라
```
// 이렇게 무조건 사용하라는 책에서으 ㅣ설명....아?
public Period(Date start, Date end){
    this.start = new Date(start.getTime()); // 복사
    this.end = new Date(end.getTime()); // 복사

    if(this.start.compareTo(this.end) > 0)
        throw new IllegalArgumentException( this.start + " fter " + this.end);
}
```





