JPA 책 구매후 리뷰시작예정


1장
***********

객체 모델과 관계형DB는 지향하는 패러다임이 서로다름 
이때 개발자가 해당하는 모델들을 제작하는대에 있어 시간소모가 크고 
불필요한 코드가 생성될수있는 문제점이 존재함 

JPA에서는 DB를 객체지향적으로 바라볼수 있기때문에 직관적이고 패러다임의 불일치문제를 
해결해줄수있는 방법을 제공해줌 


JPA란 무엇인가? 
JPA는 ORM 기술 표준으로 애플리케이션과 JDBC사이에서 동작

ORM은 무엇인가? 
ORM(Object-Relational Mapping)은 이름 그대로 객체와 관계형 데이터 베이스를 매핑한다는 뜻임 
ORM프레임워크는 단순히 SQL을 개발자 대신 생성해주는 기능뿐만이 아닌
객체와 테이블을 매핑해서 패러다임의 불일치 문제를 개발자대신에 해결해줌
그래서 JPA를 사용시에 객체 측면에서는 객체에 좀더 집중하여 정교하게 모델링을 할 수 있고 
관계형 데이터베이스는 데이터베이스에 맞도록 모델링하면 됨


DFS , BFS
디바이드컨커
재귀가 핵심
순열
조합 순열

3장
**********************************
META-INF/persistence.xml에 있는 정보를 바탕으로 EntityManagerFactory(em)생성함 

엔티티 매니저 팩토리는 여러 스레드가 동시에 접근해도 안전하므로 서로 다른 스레드 간에 공유해도 되지만, 엔티티 매니저는 여러 스레드가 동시에 
접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안됨 


스레드 관련내용도 공부해야함....


영속성 컨텍스트 - 엔티티를 영구 저장하는 환경
persist는 엔티티 매니저를 사용해서 회원 엔티티를 영속성 컨텍스트에 저장


엔티티의 생명주기 
비영속 : 영속성 컨텍스트와 전혀 관계가 없는 상태
영속 : 영속성 컨텍스트에 저장된 상태
준영속 : 영속성 컨텍스트에 저장되었다가 분리된 상태 
삭제 : 삭제된 상태

4장
*************************************
객체와 테이블 매핑 : @Entity, @Table
기본 키 매핑 : @Id 
필드와 컬럼 매핑 : @Column
연관관계 매핑 : @ManyToOne, @JoinColumn


@Entity 관련 속성 
name - JPA에서 사용할 엔티티 이름을 지정, 보통 기본값인 클래스 이름을 사용함. 만약 다른 패키지에 이름이 같은 엔티티 클래스가 존재하면 이름을 지정해서 
       충돌하지 않도록 지정해야함
       
@Table 관련 속성 
name - 매핑할 테이블 이름 
catalog - catalog 기능이 있는 데이터베이스에서 catalog를 매핑
schema - schema 기능이 있는 데이터베이스에서 schema를 매핑 
uniqueConstraints - DDL 생성 시에 유니크 제약조건을 만듬, 2개 이상의 복합 유니크 제약조건도 만들 수 있음 참고로 이기능은 스키마 자동 생성기능을 사용해서 DDL을 만들                       때만 사용됨

hibernate.hbm2ddl.auto 속성 정리

create : 기존 테이블을 삭제하고 새로 생성 (DROP + CREATE)
create-drop : create 속성에 추가로 애플리케이션을 종료할 때 생성한 DDL을 제거함 (DROP + CREATE + DROP)

@SequenceGenerator 
name : 식별자 생성기 이름 
sequenceName : 데이터베이스에 등록되어 있는 시퀀스 이름 
initialValue : DDL 생성 시에만 사용됨, 시퀀스 DDL을 생성할 때 처음 시작하는 수를 지정함 
allocationSize : 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨)
catalog, schema : 데이터베이스 catalog, schema 이름


@TableGenerator 
name : 식별자 생성기 이름 
table : 키생성 테이블명
pkColumnName : 시퀀스 컬럼명 
valueColumnName : 시퀀스 값 컬럼명 
pkColumnValue : 키로 사용할 값 이름 
initialValue : 키로 사용할 값 이름 
allocationSiz : 초기 값, 마지막으로 생성된 값이 기준 
catalog, schema : 시퀀스 한번 호출에 증가하는수(성능 최적화에 사용)
uniqueConstraints(DDL) : 유니크 제약 조건을 지정할 수 있음.

