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


## Domain Layer


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

```java
public class OrderItem {
    private UUID productId;
    private BigDecimal price;

    public OrderItem(Product product) {
        this.productId = product.getId();
        this.price = product.getPrice();
    }

    // getters
}
```


그다음으로 우리는 repository interface를 생성해야 한다. interface 구현부는 infrastructure에 작성한다.

마지막으로 Order는 항상 각각의 액션의 끝난 후에 저장되어야한다. 이것을 하기위해선 우리는 DomainService(root의 일부분이 될수 없는 로직을 포함한)를 먼저 정의해야한다.

```java
public class DomainOrderService implements OrderService {

    private final OrderRepository orderRepository;

    public DomainOrderService(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }

    @Override
    public UUID createOrder(Product product) {
        Order order = new Order(UUID.randomUUID(), product);
        orderRepository.save(order);

        return order.getId();
    }

    @Override
    public void addProduct(UUID id, Product product) {
        Order order = getOrder(id);
        order.addOrder(product);

        orderRepository.save(order);
    }

    @Override
    public void completeOrder(UUID id) {
        Order order = getOrder(id);
        order.complete();

        orderRepository.save(order);
    }

    @Override
    public void deleteProduct(UUID id, UUID productId) {
        Order order = getOrder(id);
        order.removeOrder(productId);

        orderRepository.save(order);
    }

    private Order getOrder(UUID id) {
        return orderRepository
          .findById(id)
          .orElseThrow(RuntimeException::new);
    }
}
```
Hexagonal 아키텍처에서 이 서비슨 port를 구현한 어댑터이다. 게다가 이것을 spring bean으로 등록하면 안되는데 그 이유는 도메인관점에서 이것은 내부로직이며 spring configuration은 바깥에 잇기 때문인다. 

## Application Layer

이 레이어에서 유저는 restful api 를 사용해 우리의 application과 소통할 수 있다. 
```java
@RestController
@RequestMapping("/orders")
public class OrderController {

    private OrderService orderService;

    @Autowired
    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @PostMapping
    CreateOrderResponse createOrder(@RequestBody CreateOrderRequest request) {
        UUID id = orderService.createOrder(request.getProduct());

        return new CreateOrderResponse(id);
    }

    @PostMapping(value = "/{id}/products")
    void addProduct(@PathVariable UUID id, @RequestBody AddProductRequest request) {
        orderService.addProduct(id, request.getProduct());
    }

    @DeleteMapping(value = "/{id}/products")
    void deleteProduct(@PathVariable UUID id, @RequestParam UUID productId) {
        orderService.deleteProduct(id, productId);
    }

    @PostMapping("/{id}/complete")
    void completeOrder(@PathVariable UUID id) {
        orderService.completeOrder(id);
    }
}
```
이 컨트롤러는 우리의 도메인에 외부restful 인터페이스를 붙히는 역할을하며 port역할을하는 orderService의 적절한 메서드를 호출함으로써 연결한다.

## Infrastructure Layer
이 층은 application을 실행하기위한 로직을 포함하고있다. 따라서 configuration클래스를 생성해야된다
```java
@Configuration
public class BeanConfiguration {

    @Bean
    OrderService orderService(OrderRepository orderRepository) {
        return new DomainOrderService(orderRepository);
    }
}
```
그 다음으로 spring data repository 를 사용할 수 있는 configuration을 만들어야 한다.

```java
@EnableMongoRepositories(basePackageClasses = SpringDataMongoOrderRepository.class)
public class MongoDBConfiguration {
}
```
이 클래스는 `basePackageClasses` 프로퍼티를 가지고 있어야한다. 왜냐하면 오직 인프라스트쳐에만 해당되어야 하기 때문이다. 또한 스플이이 전체 어플리케이션을 스캔할 이유가 없다. 더 나아가서 이 클래스는 몽고디비와 application을 관장하는 모든것을 관장한다.
마지막으로, orderRepository를 도메인 레이어에 만들어야된다.
```java
@Component
public class MongoDbOrderRepository implements OrderRepository {

    private SpringDataMongoOrderRepository orderRepository;

    @Autowired
    public MongoDbOrderRepository(SpringDataMongoOrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }

    @Override
    public Optional<Order> findById(UUID id) {
        return orderRepository.findById(id);
    }

    @Override
    public void save(Order order) {
        orderRepository.save(order);
    }
}
```

이런 레이어적인 접근법의 가장 큰장점은 우리가 도메인로직을 다른것들로부터 독립시킬 수 있다는 것이다.도메인 파트는 오직 비즈니스 로직만 포함해야된고 다른 환경으로 쉽게 옮길 수 있어야 한다. 



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