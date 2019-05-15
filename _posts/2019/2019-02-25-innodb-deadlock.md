---
layout: post
title: "InnoDB deadlock cases"
description: ""
category: 
tags: [mysql, interview]
---

Base case

```sql
CREATE TABLE t (
id int(10) primary key
)engine=innodb

insert into t values(1);
insert into t values(3);
insert into t values(10);

```

### Gap lock

```sql
--txn A
set session autocommit=0;
delete from t where id = 5;

--txn B
insert into t values(0); -- proceed
insert into t values(2); -- proceed
insert into t values(7); -- in conflict with gap lock (3, 10], blocked
```

In `show engine innodb status`, it will be `lock_mode x locks gap`

### S + X

```sql
--txn A
set session autocommit=0;
insert into t values(7); 

--txn B
insert into t values(7);

```

Sequence of actions:
1. A acquires S on id = 7
2. B acquires S on id = 7
3. A tries to acquire X on id = 7, but blocked by B
4. B tries to acquire X, but blocked by A

### shared gap lock
```
--txn A
delete from t where id = 6
insert into t values(5)

--txn B
delete from t where id = 7;
insert into t values(8)
```

Sequence of actions:
1. A acquires shared gap lock on (3, 10] on delete
2. B acquires S gap lock on (3, 10] on delete
3. A wants X gap lock on (3, 10], blocked
4. B wants X gap lock on (3, 10], blocked 

### Common solutions to DL
1. acquirng/relase in the same order
2. timed wait, and then release if not able to acquire (defaults to 50s in mysql)
