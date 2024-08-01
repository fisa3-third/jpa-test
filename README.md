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

### 문제 3

3-1 )

```bash
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor

@Getter
@Setter
@ToString

@SequenceGenerator(name = "team4_seq", sequenceName = "team4_seq_id",
				   initialValue = 1, allocationSize = 50)
@Entity
public class Team4 {
	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "team4_seq")
	@Column(name = "team_id")
	private long teamId;
	
	@NonNull
	@Column(name="team_name", length = 20)
	private String teamName;	
	
	@OneToMany(mappedBy = "teamId")
	// "teamId" 가 가지는 의미는 무엇인가?
	public List<Member4> members = new ArrayList<>();	
}

정답 : Member4에서 Team4에 join된 변수명, 자식의 참조키
```

3-2 )

```bash
@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor 
@Getter
@Setter
@ToString

@SequenceGenerator(name = "member4_seq", sequenceName = "member4_seq_member_id", 
				   allocationSize = 50, initialValue = 1)
@Table(name = "ce_member") 
@Entity
public class Member4 {

	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "member4_seq") 
	@Column(name="member_id")
	private long memberId;
	
	@NonNull
	@Column(length = 20, nullable = false) 
	private String name;

	@NonNull
	@ManyToOne(fetch = FetchType.LAZY) 
	// 문제 : fetch = FetchType.LAZY를 사용하는 이유를 설명하시오.
	@JoinColumn(name="team_id")
	private Team4 teamId;
	
}

정답 : 엔티티를 실제로 필요한 시점까지 DB에서 로딩을 하지 않고, 초기 로딩 시점의 성능을 개선하고 불필요한 데이터베이스 쿼리 실행을 방지할 수 있다. 또한 메모리 사용량을 줄일수 있다.
```

3-3)

```bash
public class Step04RunTest {
	@Test
	public void step03Test() {
		EntityManager em = DBUtil.getEntityManager();
		
		Member4 m = em.find(Member4.class, 1L);
		
		System.out.println(m.getName());
		System.out.println(m.getTeamId().getTeamName());
		// 출력문이 실행되면서 select문이 실행되는 횟수는?
		em.close();
		em = null;
	}
}

정답 : 2번
```