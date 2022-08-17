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
    ```