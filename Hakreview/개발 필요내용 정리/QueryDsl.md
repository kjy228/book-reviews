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
```

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
```


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
```
    
    
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
```
- from절에 여러 엔티티를 선택해서 세타조인
- 외부조인 불가능 -> 조인on을 사용하면 외부 조인 가능
   
### Join on
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
참고 블로그 : https://madplay.github.io/post/avoid-n+1-problem-in-jpa-using-querydsl-fetchjoin
```java
 @Test
    void fetchJoinUse() throws Exception{
        em.flush();
        em.clear();
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);

        Member findMember = queryFactory
                .selectFrom(member)
                .join(member.team, team).fetchJoin()
                .where(member.username.eq("member1"))
                .fetchOne();

        boolean loadded = emf.getPersistenceUnitUtil().isLoaded(findMember.getTeam());
        assertThat(loadded).as("패치조인 미적용").isFalse();
    }
``` 
join이든 left join이든 뒤에 fetchjoin()을 붙혀주면 된다.

        
## 서브쿼리
`com.querydsl.jpa.JPAExpressions` 사용

### from 절 subQuery 한계
JPA JPQL 서브쿼리의 한계점으로 from 절의 서브쿼리(인라인 뷰) 는 지원하지 않음, QueryDsl도 지원하지 않음.
하이버네이트 구현체를 사용하면 select절의 서브쿼리는 지원한다. querydsl도 하이버네이트 구현체를 사용하면 select절의 섭ㅡ쿼리를 지원함

- 해결방안

1. 서브쿼리를 join으로 변경(불가능한 상황 발생 할 수 있음)
2. 애플리케이션에서 쿼리를 2번 분리해서 실행
3. nativeSQL 사용하기

## Case문
select, 조건절(where)에서 사용 가능하다 

```java
/**
* case 문 사용하여 조회
*/
    @Test
    void simpleCaseQuery(){
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        List<String> result = queryFactory
                .select(member.age
                        .when(10).then("열살")
                        .when(20).then("스무살")
                        .when(30).then("늙")
                        .otherwise("더 늙ㅠㅠㅠ"))
                .from(member)
                .fetch();
    }

@Test
    void complexCaseQuery(){
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        queryFactory
                .select(new CaseBuilder()
                        .when(member.age.between(0,20)).then("0-20살")
                        .otherwise("늙~~~~!!"))
                .from(member)
                .fetch();
                
    }
````

문자더하기 예제
```java
/**
     * 문자 더하기
     */
    @Test
    void concatQuery(){
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        //{userName}_{age}
        List<String> result = queryFactory
                .select(member.username.concat("_").concat(member.age.stringValue()))
                .from(member)
                .fetch();
        for(String s : result ){
            System.out.println("s = " + s);
        }
    }
```

## 프로젝션 
프로젝션 : select 대상 지전
- 프로젝션 대상이 하나면 타입을 명확하게 지정 가능
- 대상이 둘 이상이면 튜플이다 dto로 조회

그렇다면 튜플이란 뭘까? 
- queryDSL이 여러개를 조회할때 사용할 수 있도록 만들어놓은 객체 이다.


```java
 @Test
    void simpleProjection(){
        List<String> result = queryFactory.select(member.username)
                .from(member)
                .fetch();

        //Member 객체 타입도 한개의 프로젝션이라고 한다
        List<Member> resultMember = queryFactory.select(member)
                .from(member)
                .fetch();

        for(Member s : resultMember){
            System.out.println("s = : "+ s);
        }
    }

    @Test
    void tupleProjection(){
        List<Tuple> result = queryFactory.select(member.username, member.age)
                .from(member)
                .fetch();

        for(Tuple t : result){
            String username =  t.get(member.username);
            Integer age = t.get(member.age);
            System.out.println("name = : "+ username + " age : = " + age);
        }
    }
````
프로젝트를 설게할때 튜플은 querydsl.core 라이브러리에서 지원해주는것이기 때문에 레퍼지토리 계층에서 사용하는것은 좋으나 이를 넘어서 서비스계층이나 인터페이스 계층까지 튜플을 사용하는것은 좋은 예가 아니다.

### 프로젝션 결과 반환 dto로 조회하기

<b>기존 JPQL로 dto 조회할때 </b>

```java
@Test
    void findDtoByJPQL(){
        List<MemberDto> result = em.createQuery("select new study.querydsl.dto.MemberDto(m.username, m.age) from Member m", MemberDto.class)
                .getResultList();
        System.out.println(result);
    }
````
new 오퍼레이션을 사용해서 해당 패키지 디렉토리를 select 해서 memberDto 클래스의 생성자에 직접 값을 주입해주는 방법으로 사용해야하는 번거로움이 있다. 
또한 생성자 방식만 지원하기 때문에 setter, builder와 같은 lombok에서 지원하며 개발자들이 많이사용하는 생성패턴을 사용할 수 없다......ㅠㅠㅠㅠ

<b>QueryDsl - bean으로 조회할때 </b>

querDsl로 dto를 조회하기 위해서는 dto 클래스에 기본 생성자가 반드시 존재해야한다. 
기본적인 플로우를 설명하자면 기본생성자를 생성한뒤 @data 어노테이션의 setter를 사용해서 memberDto의 각 속성에 값을 주입하게 된다.

```java
package study.querydsl.dto;

import lombok.Data;

@Data
public class MemberDto {
    private String username;
    private int age;
    //기본 생성자 
    public MemberDto() {
    }

    public MemberDto(String username, int age) {
        this.username = username;
        this.age = age;
    }

}

// Test code
@Test
    void findDtoBySetter(){
        List<MemberDto> result = queryFactory
                .select(Projections.bean(MemberDto.class,
                        member.username,
                        member.age))
                .from(member)
                .fetch();
        for(MemberDto dto : result){
            System.out.println(" dto : " + dto);
        }
    }
```

<b>QueryDsl - Field로 조회할때 </b>

field로 조회할때는 기본생성자가 필요 없으며 또한 setter도 필요없다. 
dto클래스의 각 속성에 직접 값을 주입하는것으로 생각하면 된다.
```java
 @Test
    void findDtoByField(){
        List<MemberDto> result = queryFactory
                .select(Projections.fields(MemberDto.class,
                        member.username,
                        member.age))
                .from(member)
                .fetch();
        for(MemberDto dto : result){
            System.out.println(" dto : " + dto);
        }
    }
```


































