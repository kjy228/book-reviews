# QueryDsl
QueryDsl은 큐타입 파싱을 통해서 이루어진다.

기본 QueryDsl 예제 테스트코드 
```java
@Test
    void startQuerydsl() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        QMember m = new QMember("m");

        Member findMember = queryFactory
                .select(m)
                .from(m)
                .where(m.username.eq("member1"))
                .fetchOne();

        assertThat(findMember.getUsername()).isEqualTo("member1");
    }
````
이코드로 알 수 있는 QueryDsl의 장점
1. `.where(m.username.eq("member1"))` 부분에서 알 수 있듯이 파라미터 바인딩을 자동으로 해준다. 
2. 오타에 대한 내용을 런타임 시점이 아닌 `컴파일 타임` 에 알 수 있다.
3. `JPAQueryFactory queryFactory = new JPAQueryFactory(em);`  부분은 멀티쓰레드 환경에서 `동시성 문제` 없이 작동하도록 설계가 되어있어 개발자 입장에서 편하다.

QueryDsl 사용법

```java
@Test
    void resultFetch(){
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        //리스트 조회 
        List<Member> fetch = queryFactory
                .selectFrom(member)
                .fetch();

        //단건 조회 
        없으면 null 반환, 1개이상이면 Exception던짐
        Member fetchOne = queryFactory
                .selectFrom(member)
                .fetchOne();
        Member fetchFirst = queryFactory
                .selectFrom(member)
                .fetchFirst();
        QueryResults<Member> results = queryFactory
                .selectFrom(member)
                .fetchResults();

        results.getTotal();
        List<Member> content = results.getResults();
        
        //아래 두개로 페이징 처리 
        results.getOffset();
        results.getLimit();
                
    }
````

    ## Join
    조인의 기본 문법은 첫번쨰 파람미터에 조인 대상을 지정하고, 두번 쨰 파라ㅏ미어테 별칭으로 사용할 Q타입을 지정하면 된다. 
    ```java
     @Test
    void join(){
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<Member> result = queryFactory
                .selectFrom(member)
                .join(member.team, team)
                .where(team.name.eq("teamA"))
                .fetch();

        assertThat(result)
                .extracting("username")
                .containsExactly("member1", "member2");

    }

    @Test
    void leftJoin(){
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<Member> result = queryFactory
                .selectFrom(member)
                .leftJoin(member.team, team)
                .where(team.name.eq("teamA"))
                .fetch();

        assertThat(result)
                .extracting("username")
                .containsExactly("member1", "member2");

    }
    ````
    
    
    ```java
    @Test
    public void theta_join() throws Exception{
        em.persist(new Member("teamA"));
        em.persist(new Member("teamB"));
        
        List<Member> result = queryRactory
            .select(member)
            .from(member, team)
            .where(member.username.eq(team.name))
            .fetch();
            
        assertThat(result)
            .extracting("username")
            .containsExactly("teamA", "teamB");
   }
   ````
   
   - from절에 여러 엔티티를 선택해서 세타조인
   - 외부조인 불가능 -> 조인on을 사용하면 외부 조인 가능
   
   ## Join on
   - 조인대상 필터링
   회원과 팀을 조인하면서 팀 이름이 teamA인 팀만 조인
   on절을 활용해 조인 대상을 필터링 할 때, 외부조인이 아니라 내부조인(inner join)을 사용하면, where 절에서 필터링 하는 것과 기능이 동일하다. 따라서 on절을 활용한 조인 대상 필터링을 사용할 때, 내부조인 이면 익숙한 where절로 해결하고, 정말 외부 조인이 필요한 경우에만 이 기능 사용하는것을 추천.


## 연관관계 없는 엔티티 외부 조인
- 회원의 이름과 팀의 이름이 같은 대상 *외부 조인*
```java
/**
     * 연관관계 없는 엔티티 외부조인
     * 예) 회원의 이름과 팀의 이름이 같은 대상 외부조인
     * JPQL : select m, t from member m left join team t on m.username = t.name
     * SQL : select m.*, t.* from member m left join Team t on m.username = t.name
     *
     * @throws Exception
     */
    @Test
    void join_on_no_relation() throws Exception {
        em.persist(new Member("teamA"));
        em.persist(new Member("teamB"));
        em.persist(new Member("teamC"));

        JPAQueryFactory queryFactory = new JPAQueryFactory(em);

        List<Tuple> result = queryFactory
                .select(member, team)
                .from(member)
                .leftJoin(team).on(member.username.eq(team.name))
                .fetch();

        for (Tuple t : result) {
            System.out.println("tuple : " + t);
        }
    }
````
- hibernate 5.1부터 `on` 을 사용해서 서로 관게 없는 필드로 외부조인하는 기능 추가됨(내부조인도 가능)
- 문법을 잘 봐야함 `leftJoin()`부분에 일반 조이노가 다르게 엔티티 하나만 들어감
    - 일반 조인 : `leftJoin(member.team, team)`
    - on 조인 : `from(member).leftJoin(team).on(xxx)``


## fetch join
페치 조인은 sql에서 제공하는 기능은 아니다. sql조인을 활용해서 연관된 엔티티를 sql한번에 조회하는 기능이다. 주로 성능 최적화에 쓰인다

        
    






























