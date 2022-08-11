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

#### JPA 주요 이벤트 어노테이션 
- @PrePersist, @PostPersist
- @PreUpdate, @PostUpdate


### Sprind data Jpa 사용

설정
- @EnableJpaAuditing 사용 
- @EntityListeners(AuditingEntityListener.class) -> 엔티티에 적용
- 
```java
package study.datajpa.entity;

import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.Column;
import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
@Getter
public class BaseEntity {
    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createDate;

    @LastModifiedBy
    private LocalDateTime lastModifiedDate;
}

```
