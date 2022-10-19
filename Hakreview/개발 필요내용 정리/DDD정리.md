참고 : https://happycloud-lee.tistory.com/94

## DDD의 핵심 목표 
Loosly coupling , High cohension
어플리케이션에서 모듈간의 의존성은 최소화하고, 응집성은 최대화한다. 
이론에서도 결합도는 낮추고 응집도는 높이라는 말이 있듯이 이를 따라가기위한 아키텍쳐라고 생각한다.

![image](https://user-images.githubusercontent.com/43670838/196368852-362b13c7-9900-4513-9eae-f910128458b9.png)

- <b>Application Layer</b> : application층을 통해 유저는 애플리케이션과 통신할 수 있다. 
이 레이어에서는 `restful controller, user interfaces, json serializatio libraries`가 포함된다. 

- <b> Domain Layer</b> : 비즈니스로직 과련된 코드를 포함한다. 우리 애플리케이션의 핵심이여 이 층에서는 application, infrastructure로 부터 고립되어야만 한다. 또한 db와 같은 외부 파트와 통신하기위한 api interface가 포함된다. 

- <b>Infrastructure Layer</b> : 도메인레이어의 interface 구현부와  db configuration, spring configuration 같은 클래스가 포함된다.


## Domain


```java
public class Order {
    private UUID id;
    private OrderStatus status;
    private List<OrderItem> orderItems;
    private BigDecimal price;

    public Order(UUID id, Product product) {
        this.id = id;
        this.orderItems = new ArrayList<>(Arrays.astList(new OrderItem(product)));
        this.status = OrderStatus.CREATED;
        this.price = product.getPrice();
    }

    public void complete() {
        validateState();
        this.status = OrderStatus.COMPLETED;
    }

    public void addOrder(Product product) {
        validateState();
        validateProduct(product);
        orderItems.add(new OrderItem(product));
        price = price.add(product.getPrice());
    }

    public void removeOrder(UUID id) {
        validateState();
        final OrderItem orderItem = getOrderItem(id);
        orderItems.remove(orderItem);

        price = price.subtract(orderItem.getPrice());
    }

    // getters
}
```

Order Entity는 `aggregate root` 이다. 비즈니스 로직에 연관댄 모든 것들은 이 클래스를 통한다. 
- order는 Id가 주어지고 한개의 product가 있을때만 생성될 수 있다. 
- order의 상태가 completed가 되면 orderitems는 사용불가능하게 변경한다.
- 외부에서 setter를 통해 Order를 변경하는것은 불가능하다.


## JPA가 지원하는 다양한 쿼리 방법
 - JPQL
 - JPA Criteria
 - QueryDSL
 - native SQL
 - JDBC api 직접사용( MyBatis, SpringJdbcTemplate)와 함께사용

### JPQL
- 엔티티 객체를 중심으로 개발
- 검색쿼리가 문제(ex) 멤버 엔티티에서 성인인 사람들을 조회해라)
- 검색할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색
- 모든 DB데이터를 객체로 변환해서 검색하는 것은 불가능
- 애플리케이션이 필요한 데이터만 DB에서 불러오려면 결구 껌색 조건이 포함된 SQL이 필요
- 테이블이 아닌 객체를 대상으로 검색하는 객체 지향 쿼리
- SQL을 추상화해서 특정 데이터 베이스 SQL에 의존하지 않는다
- JPQL을 한마디로 정의하면 객체 지향 SQL

username 설정 후 