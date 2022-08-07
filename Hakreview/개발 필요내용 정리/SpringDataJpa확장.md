## Auditing
엔티티 생성, 변경할 때 변경한 사람과 시간을 추적하고 싶다면? 등록일, 수정일, 등록자, 수정자를 넣어야 한다.

### 순수 Jpa사용 - 실무에서 사용 XXX
```java
package study.datajpa.entity;

import javax.persistence.Column;
import javax.persistence.MappedSuperclass;
import javax.persistence.PrePersist;
import java.time.LocalDateTime;

@MappedSuperclass
public class JpaBaseEntity {
    @Column
    private LocalDateTime createdDate;
    private LocalDateTime updatedDate;

    @PrePersist
    public void prePersist(){
        LocalDateTime now = LocalDateTime.now();
        createdDate = now;
        updatedDate =now;
    }

    public void preUpdate(){
        updatedDate = LocalDateTime.now();
    }
}

```
`@MappedSuperclass` 를 사용해야 테이블생성시 상속받은 createDate, updatedDate 컬럼을 생성할 수 있다. 
