# 자바 ORM표준 JPA 프로그래밍

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

- 영속

- 준영속

- 삭제

  