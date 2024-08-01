# JPA 기본 개념 test 만들어보기

### 1. 다음 실행된 쿼리를 보고 FetchType의 종류를 맞춰보세요. (보기: Lazy, Eager)

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

