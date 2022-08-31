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