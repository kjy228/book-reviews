# Query 생성 방법
## 메서드 이름으로 쿼리생성
## NamedQuery 
장점 : 정적 쿼리이기 때문에 application loading 시점에 오타와 관련된 에러로그를 바로 뱉어낸다.
<img width="921" alt="image" src="https://user-images.githubusercontent.com/43670838/179380468-669b8620-a7d1-43c7-8a63-e5160dedcf13.png">

## repository method로 쿼리 정의
   @query사용
   ```java
    @Query("select m from Member m where m.username =: username and m.age = :age")
    List<Member> findUser(@Param("username") String username, @Param("age") int age);
   ```

   @query 사용하여 Dto조회
   ```java
    @Query("select new study.datajpa.Dto.MemberDto (m.id, m.username, t.name) from Member m join m.team t")
    List<MemberDto> findMemberDto();
```
    new operation을 꼭 추가해줘야된다. 
    QueryDSL을 사용하면 이러한 작업도 안해도 된다.


## Parameter Binding
- 위치 기반
- 이름 기반
두가지 방법이 있는데 위치기반은 코드 가독성이 떨어지기 때문에 거의 사용하지 않으며 이름기반만 사용한다고 생각하면 된다. 
```sql
select m from Member m where m.name = ?0 //위치기반
select m from Member m where m.name = :name //이름기반
```
**중요**
### Collection Binding
