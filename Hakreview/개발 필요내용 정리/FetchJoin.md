# 컬렉션 조회 최적화
-> 실무에서 fetch join을 사용하게 되어 이에대해 정리해보고자 한다. 

JPA 일반적으로 연관관계를 맺은 엔티티를 조회할때 Lazy Loading으로 인해 get을 하기 전까지는 연관된 엔티티를 함께 조회하지 않는다. 그래서 N+1문제가 발생할수 있음 -> 이를 해결하기위해 fetch join으로 한번에 조회 

## Entity를 직접 노출하지 말아라 -> Dto로 한번 감싸는 방법을 추천

예제코드
```java
@Getter
    static class OrderDto{
        private Long orderId;
        private String name;
        private LocalDateTime orderDate;
        private OrderStatus orderStatus;
        private Address address;
        private List<OrderItemDto> orderItems; // OrderItem Entity도 dto로 감싸야 한다. 

        public OrderDto(Order o){
            this.orderId = o.getId();
            this.name = o.getMember().getName();
            this.orderDate = o.getOrderDate();
            this.orderStatus = o.getStatus();
            this.address = o.getDelivery().getAddress();
            this.orderItems = o.getOrderItems().stream()
                    .map(OrderItemDto::new)
                    .collect(Collectors.toList());
        }
    }
```
이 부분은 나도 간과했던 부분으로 17번쨰 라인 `private List<OrderItemDto> orderItems;` 을 보면 orderItem도 dto로 감싼것을 볼 수 있다 . 내일 출근하자마자 바꾸는걸로.....
dto로 감싸면 N+1문제가 발생할 수 있다
이를 해결하기위해 나온것이 fetch join이다.

## fetch join
- 회원을 조회하면서 연관된 팀도 함께 조회 -> N+1 문제 해결 가능

``` java
// JPQL
select m from Member m join fetch m.team

//실제 SQL
select M.*, T.* from member M 
inner join team t on M.team_id = T.id;

```
마치 즉시 로딩처렁 팀과 멤버의 모든정보를 조회해서 ( default 는 inner join ) 1차 캐시에 보관한다.

## 컬렉션 페치조인
- OneToMany관계일때 사용하며 컬렉션 페치 조인이라고한다. DB 입장에서 oneTomany 조인을 하게되면 데이터가 뻥튀기가 된다..
``` sql
// JPQL
select t
from Team t join fetch t.members
where t.name = '팀A';

//실제 SQL
select T.*, M.*
from Team T
inner join member M on T.id = M.team_id
where T.name = '팀A';
```
이렇게 JPQL을 작성하게되면 데이터가 뻥튀기 되게 된다. 
예를 들어 
team기준으로 조회 하여 '팀A에 속하는 멤버들을 출력하라' 라는 쿼리를 작성할때 멤버수만큼 조회가 되는것이다. 
이를 막기위해 fetch join과 distinct를 같이 사용하면 중복 된결과를 제거할 수 있다. 
- sql에 distinct 추가
- 애플리케이션에서 엔티티 중복 제거 
하는 방법을 사용하면된다. 
그렇다면 페치조인과 일반조인의 차이는?

## 페치조인 vs 일반 조인
일반조인
- 일반조인 실행시 연관된 엔티티를 함께 조회하지 않음
- JPQL은 결과를 반환할때 연관관계 고려하지 않음
- 단지 select절에 지정한 엔티티만 조회할뿐
- 여기서 팀엔티티만 조회하고 회원엔티티는 조회하지 않음

페치 조인
- 페치 조인을 사용할 때만 연관된 엔티티도 함께 조회`(즉시로딩)`
- 페치 조인은 객체 그래프를 sql한번에 죄하는 개념
  
## 페치조인의 특징과 한계
- 페치 조인 대상에는 별칭을 줄 수 없다 ( 하이버 네이트는 가능하지만 사용하지 마라)
- 둘 이상의 컬렉션은 페치 조인 할 수 없다. 
- 컬렉션을 페치 조인하면 페이징 api(setfirstResult, setMaxResults)사용할 수 없음
    - 일대일, 다대일 같은 단일 값 연관 필드들은 페치조인해도 페이징 가능
    - 하이버네이트는 경고 로그를 남기고 메모리에 페이징(매우 위험!!!)