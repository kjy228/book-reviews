

# 자바 ORM표준 JPA 프로그래밍

**Q1 : Mybatis와 어떤 차이가 있는지?** 

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