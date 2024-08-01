# JPA 기본 개념 test 만들어보기

### 문제 1. 다음 실행된 쿼리를 보고 FetchType의 종류를 맞춰보세요. (보기: Lazy, Eager)

1-1)

```
Hibernate: 
    select
        team3_seq_id.nextval 
    from
        dual
Hibernate: 
    select
        member4_seq_member_id.nextval 
    from
        dual
Hibernate: 
    insert 
    into
        Team4
        (team_name, team_id) 
    values
        (?, ?)
Hibernate: 
    insert 
    into
        ce_member
        (name, team_id, member_id) 
    values
        (?, ?, ?)
Hibernate: 
    select
        member4x0_.member_id as member_i1_0_0_,
        member4x0_.name as name2_0_0_,
        member4x0_.team_id as team_id3_0_0_ 
    from
        ce_member member4x0_ 
    where
        member4x0_.member_id=?
Member4 loaded: Member A
Hibernate: 
    select
        team4x0_.team_id as team_id1_1_0_,
        team4x0_.team_name as team_nam2_1_0_ 
    from
        Team4 team4x0_ 
    where
        team4x0_.team_id=?
Team4 loaded: Team A
```

1-2)

```
Hibernate: 
    select
        team3_seq_id.nextval 
    from
        dual
Hibernate: 
    select
        member4_seq_member_id.nextval 
    from
        dual
Hibernate: 
    insert 
    into
        Team4
        (team_name, team_id) 
    values
        (?, ?)
Hibernate: 
    insert 
    into
        ce_member
        (name, team_id, member_id) 
    values
        (?, ?, ?)
Hibernate: 
    select
        member4x0_.member_id as member_i1_0_0_,
        member4x0_.name as name2_0_0_,
        member4x0_.team_id as team_id3_0_0_,
        team4x1_.team_id as team_id1_1_1_,
        team4x1_.team_name as team_nam2_1_1_ 
    from
        ce_member member4x0_,
        Team4 team4x1_ 
    where
        member4x0_.team_id=team4x1_.team_id(+) 
        and member4x0_.member_id=?
Member4 loaded: Member B
Team4 loaded: Team B
```

### 문제2 

- 위 코드는 [Member.java](http://Member.java) 코드이다. 밑에 있는 코드를  엔티티 중 한 명의 OJT 상태를 "완료"로 변경하세요 .
- 변경된 정보를 데이터베이스에 반영하고, 다시 조회하여 변경된 OJT 상태를 출력하세요.

```java
package step04.onetomany;

import javax.persistence.*;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;
import lombok.Setter;
import lombok.ToString;

@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
@Getter
@Setter
@ToString

@Entity
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "member_id")
    private Long id;

    @NonNull
    @Column(name = "name", length = 50)
    private String name;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "team_id")
    private Team team;

    @NonNull
    @Column(name = "ojt_status")
    private String ojtStatus; // OJT 상태 (예: "진행 중", "완료")
}

```

```java
EntityManager em = DBUtil.getEntityManager();
EntityTransaction tx = em.getTransaction();

try {
    tx.begin();
    Member member = em.find(Member.class, 1L);
    tx.commit();
} catch (Exception e) {
    tx.rollback();
    e.printStackTrace();
} finally {
    em.close();
}

em = DBUtil.getEntityManager();

```
