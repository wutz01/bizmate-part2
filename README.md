# Bizmates - PART 2 exam

## 1. Write a query to display the ff columns ID (shouldstartwith T + 11 digits of trn_teacher.id with leading zeroslike'T00000088424'), Nickname, Status and Roles (like Trainer/Assessor/Staff) using table trn_teacher and trn_teacher_role.

```
SELECT
  CONCAT('T', LPAD(t.id, 11, '0')) as ID,
  t.nickname as Nickname,
  CASE t.status
    WHEN 1 THEN 'ACTIVE'
    WHEN 2 THEN 'DEACTIVATED'
    WHEN 0 THEN 'DISCONTINUED'
  END as STATUS,
  GROUP_CONCAT(DISTINCT
		  CASE tr.role
		    WHEN 1 THEN 'Trainer'
		    WHEN 2 THEN 'Assessor'
		    ELSE 'Staff'
		  END
	  SEPARATOR ' / ') AS Role
FROM trn_teacher t, trn_teacher_role tr
WHERE t.id = tr.teacher_id
GROUP BY t.id;
```

## 2. Write a query to display the ff columns ID (from teacher.id),Nickname, Open (total open slots from trn_teacher_time_table),Reserved (total reserved slots from trn_teacher_time_table),Taught (total taught from trn_evaluation) and NoShow (totalno_show from trn_evaluation) using all tables above. Shouldshow only those who are active (trn_teacher.status = 1or2)and those who have both Trainer and Assessor role.

```
SELECT
	DISTINCT tr.teacher_id as ID,
	t.nickname AS Nickname,
	SUM(IF(tt.status = 1,1,0)) AS OPEN,
	SUM(IF(tt.status = 3,1,0)) AS Reserved,
	SUM(IF(te.result = 1,1,0)) AS Taught,
	SUM(IF(te.result = 2,1,0)) AS NoShow,
FROM teacher t
JOIN teacher_role tr
  ON tr.teacher_id = t.id AND (tr.role = 1 OR tr.role = 2)
JOIN time_table tt
  ON tt.teacher_id = t.id AND (tt.status = 1 OR tt.status = 3)
JOIN trn_evaluation te
  on t.id = te.teacher_id
GROUP BY tr.id
```
