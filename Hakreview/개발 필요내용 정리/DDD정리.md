참고 : https://happycloud-lee.tistory.com/94

## DDD의 핵심 목표 
Loosly coupling , High cohension
어플리케이션에서 모듈간의 의존성은 최소화하고, 응집성은 최대화한다. 
이론에서도 결합도는 낮추고 응집도는 높이라는 말이 있듯이 이를 따라가기위한 아키텍쳐라고 생각한다.



## DDD는 Strategic Design(개념설계)과 Tactical Design(구체적 설계)으로 나눌 수 있다

### Strategic design 

### Tactical Design



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