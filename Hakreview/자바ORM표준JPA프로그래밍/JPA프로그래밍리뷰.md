

# 자바 ORM표준 JPA 프로그래밍

**Q1 : p 어떤 차이가 있는지?** 

Mybatis, JdbcTemplate을 보통SQL매퍼라고 한다. 이것은 이름 그대로 객체와 sql을 매핑한다. sql과 매핑할 객체만 지정하면 지루하게 반복되는 JDBC API사용과 응답 결과를 객체로 매핑하는 일은 sQL매퍼가 대신처리해준다. 편리하긴 하지만 결국 개발자가 SQL을 직접 작성해야 하므로 SQL에 의존하는 개발을 피할 수 없다. 반면에 ORM은 객체와 테이블을 매핑만하면 ORM프레임워크가 SQL을 만들어서 데이터베이스와 관련된 처리를 해주므로 SQL에 의존하는 개발을 피할 수 있다. 

---------

**Q2 : 주변에 mybatis만 사용하는데 왜 jpa를 사용해야 될까?**

우리나라에서만 mybatis에 대한 의존도가 높을뿐 전세계적인 통계를 보면 하이버네이트에 대한 의존도가 굉장히 높다.

------



현재 Mybatis를 사용하는 프로젝트를 진행하고있기 때문에 JPA의 .persist를 처음보게 되었다. 이를 이해하기위해 엔티티매니저 팩토리, 엔티티매니저에 대해 정리를 하고자 한다. 

JPA는 스레드가 생성될때 마다 ( 각 요청 시) EntityManagerFactory에서 EntityManager를 생성한다. EntityManager는 내부적으로 DB Connection pool을 사용한다. 

```java
private  final EntityManager em;

public void save(Member member){
    em.persist(member);
}
```

persist() 로 db에 객체로 저장하는것이아니라 컨텍스트를 통해서 엔티티를 영속화 한다는 뜻이다. 
persist()시점에는 영속성 컨텍스트에 저장한다. DB저장은 이후이다. 

현재는 하나의 엔티티매니저에 하나의 영속성 컨텍스트가 만들어 지는 코드를 작성하고 있지만 여러 엔티티 매니저가 같은 영속성 컨텍스트에 접근할 수 도 있다. 

## 엔티티 생명주기 

![image](https://user-images.githubusercontent.com/43670838/153012338-0759f451-9e2a-47f6-aa72-e2afb85b7401.png)

- 비영속
  엔티티 객체를 생성하고 순수한 객체 상태이며 아직 저장하지 않은 상태를 말한다. 

  ```java
  @Test(expected = IllegalStateException.class)
  public void 중복_회원_예외()throws Exception{
      Member mr1 = new Member();
      mr1.setName("Lee1");
      Member mr2 = new Member();
      mr1.setName("Lee1");
  ```

- 영속
  em.persist(item);
  EntityManager를 통해서 엔티티를 영속성 컨텍스트에 저장하여 관리하는 엔티티를 영속상태라고 말한다. 

- 준영속
  em.detach(), em.close(), em.clear
  EntityManaber가 관리하던 영속사태의 엔티티를 영속성 컨텍스트가 관리하지 앟는 상태 

- 삭제
  em.remove(member)
  데이터베이스와 캐시에서 삭제 

1차캐시에 저장될때 @Id, Entity의 Map형태로 저장되며 값은 "member1", memberEntity가된다. 
merge : 준영속 상태에서 다시 영속상태로 변경 

---------

# 4장 Entity Mapping

## @Entity

JPA를 사용해서 테이블과 매핑할 클래스는 이 어노테이션이 필수로 붙여야한다. 

| 속성 | 기능                                                         | 기본값                                                       |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| name | JPA에서 사용할 엔티이 이름을 지정한다. 보통 기본값인 클래스 이름을 사용한다. 만약 다른 피키지에 일므이 같은 엔티티 클래스가 있다면 이름을 지정해서 충돌하지 않도록 해야한다. | 설정하지 않으면 클래스 이름을 그대로 사용한다. <br />ex) Member |

### 주의사항

- 기본생성자는 필수
- final클래스 , enum, interface에는 사용할 수 없다. 
- 저장할 필드에 final을 사용하면 안된다. 

## @Table

| 속성    | 기능                                                         | 기본값            |
| ------- | ------------------------------------------------------------ | ----------------- |
| name    | 매핑할 테이블 이름                                           | 엔티티이름을 사용 |
| catalog | catalog기능이 있는 데이터베이스에서 매핑                     |                   |
| schema  | 스키마 기능이 있는 데이터베이스에서 매핑                     |                   |
|         | DDL생성시 유니크 제약조건을 만든다. 2개 이상의 복합 유니크 제약조건도 만들 수 있다. |                   |



```java
package jpabook.jpashop.domain;

import lombok.Generated;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "orders")
@Getter @Setter
public class Order {

    @Id
    @GeneratedValue
    @Column(name = "order_id")
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "memeber_id")
    private Member member;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();
    /*
    // cascase 안쓴경우
    persist(itemA)
    persist(itemB)
    persist(itemC)
    persist(order)

    // cascade 쓴경우
    persist(order) 만 해도 된다.

     */

    @OneToOne(fetch =  FetchType.LAZY, cascade = CascadeType.ALL)
    @JoinColumn(name = "delivery_id")
    private Delivery delivery;

    private LocalDateTime orderDate; //주문시간

    @Enumerated(EnumType.STRING)
    private OrderStatus status ; //주문상태[Order, Cancle]

    //연관관계 메서드
    public void memeber(Member member){
        this.member = member;
        member.getOrders().add(this);
    }

    public void addOrderItem(OrderItem orderItem){
        orderItems.add(orderItem);
        orderItem.setOrder(this);
    }

    public void setDelivery(Delivery delivery){
        this.delivery = delivery;
        delivery.setOrder(this);
    }

    //생성 메서드//
    public static Order createOrder(Member member, Delivery delivery, OrderItem... orderItems){
        Order order = new Order();
        order.setMember(member);
        order.setDelivery(delivery);
        for(OrderItem or : orderItems){
            order.addOrderItem(or);
        }

        order.setStatus(OrderStatus.ORDER);
        order.setOrderDate(LocalDateTime.now());
        return order;

    }

    //비즈니스 로직
    /*
    주문 취소
     */
    public void cancle(){
        if(delivery.getStatus() == DeliveryStatus.COMPlETE){
            throw new IllegalStateException("이미 배송완료된 상품은 취소가 불가능합니다.");
        }

        this.setStatus(OrderStatus.CANCLE);
        for(OrderItem or : orderItems){
            or.cancle();
        }
    }

    //조회
    /*
    전체 주문가격 조회
     */
    public int getTotalPrice(){
//        int totalPrice =0;
//        for(OrderItem or : orderItems){
//            totalPrice+= or.getTotalPrice();
//        }
//        return totalPrice;
        return orderItems.stream().mapToInt(OrderItem::getTotalPrice).sum();
    }



}
```

## 그 외 Annotation

- roleType : enum을 사용해서 회원타입을 구분한다면 일반회원은 User, 관리자는 admin이다 이처럼 자바의 enum을 사용하려면 @Enumerated어노테이션으로 매핑해야 한다.
- createDate, LastModifiedDate : 자바의 날짜 타입은 @Temporal을 사용해서 매핑한다. 
- description : 회원을 설명하는 필드는 길이 제한이 없다. 따라서 데이터베이스의 VARCHAR 타입 대신에 CLOG타입으로 저장해야한다. @Lob을 사용하면 CLOB, BLOB 타입을 매핑할 수 있다.

-------

## 스키마 생성

JPA는 데이터베이스 스키마를 자동으로 생성ㅇ하는 기능을 지원한다. 클래스의 매핑정보를 보면 어떤 테이블에 어떤 컬럼을 사용하는지 알 수 있다. 

설정파일을 확인해보면 `<proprety name="hibernate.hbm2ddl.auto" value="create"/>` 이속성을 추가하면 애플리케이션 실행 시점에 데이터베이스 테이블을 자동으로 생성한다. 

### Hibernate.hbm2ddl.auto 속성

| 옵션        | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| create      | 기존테이블을 삭제하고 새로생성한다. (drop+ create)           |
| create-drop | create속성에 추가로 애프릴케이션을 종료할때 생성한 DDL을 제거한다. |
| update      | 데이터베이ㅡㅅ 테이블과 엔티티 매핑정보를 비교해서 변경사항만 수정하낟. |
| validate    | 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 차이가 잇으면 경고를 남기고 애필르케이션을 실행하지 않는다. 이설정은 DDL을 수정 x |
| none        | 자동 새성기능을 사용하지 않으려면 속성자체를 삭제하거나 유효하지 안ㅇ흔 옵션값을 주면된다. ( none은 유효하지 않은 옵션값) |

