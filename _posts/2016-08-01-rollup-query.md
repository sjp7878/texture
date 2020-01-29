---
layout: post
title:  "Etc Chart"
description: 날짜관련 connect by sql
date:   2016-08-01 23:29:44 +0530
categories: Oracle Sql
---

rollup() 쿼리

```sql
with amp as (
	select 'emp1001' empno, 'dept1001' deptno, 'CLERK' job, 1000 sal from dual
	 union all
	select 'emp1002' empno, 'dept1003' deptno, 'MANAGER' job, 1200 sal from dual
	 union all
	select 'emp1003' empno, 'dept1003' deptno, 'SALES' job, 1300 sal from dual
	 union all
	select 'emp1004' empno, 'dept1003' deptno, 'SALES' job, 1100 sal from dual
	 union all
	select 'emp1005' empno, 'dept1004' deptno, 'AAA' job, 1000 sal from dual
	 union all
	select 'emp1006' empno, 'dept1002' deptno, 'CLERK' job, 1100 sal from dual
	 union all
	select 'emp1007' empno, 'dept1002' deptno, 'CLERK' job, 1100 sal from dual
	 union all
	select 'emp1008' empno, 'dept1003' deptno, 'CLERK' job, 1100 sal from dual
	 union all
	select 'emp1009' empno, 'dept1001' deptno, 'CLERK' job, 1100 sal from dual
	 union all
	select 'emp1010' empno, 'dept1001' deptno, 'MANAGER' job, 1100 sal from dual
	 union all
	select 'emp1011' empno, 'dept1002' deptno, 'MANAGER' job, 1100 sal from dual
	 union all
	select 'emp1012' empno, 'dept1005' deptno, 'BBB' job, 1100 sal from dual)
 ,dept as (
	select 'dept1001' deptno, '연구1과' dname from dual
	 union all
	select 'dept1002' deptno, '연구2과' dname from dual
	 union all
	select 'dept1003' deptno, '연31과' dname from dual
	 union all
	select 'dept1004' deptno, '판매1과' dname from dual
	 union all
	select 'dept1005' deptno, '판매2과' dname from dual)
select decode(grp_id, 0, dname, '총 계') dname, CLERK, MANAGER, ETC, TOTAL
from
	 (select b.dname,
		 sum(a.CLERK) CLERK,
		 sum(a.MANAGER) MANAGER,
		 sum(a.ETC) ETC,
		 sum(a.TOTAL) TOTAL,
		 grouping_id(b.dname) grp_id
	 from
		 (select deptno,
			 nvl(sum(decode(job, 'CLERK', sal)), 0) CLERK,
			 nvl(sum(decode(job, 'MANAGER', sal)), 0) MANAGER,
			 nvl(sum(decode(job, 'CLERK', null, 'MANAGER', null, sal)), 0) ETC,
			 nvl(sum(sal), 0) TOTAL from emp group by deptno) a, dept b
		 where a.deptno = b.deptno
	 group by rollup(b.dname))
;
```
