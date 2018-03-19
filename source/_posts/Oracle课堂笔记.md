title: Oracle课堂笔记
author: jackcooper
date: 2018-03-19 17:17:12
tags:
---
> oracle课堂笔记包含ddl，dml，tcl，游标，过程，函数，触发器等详细demo。志在帮助小白变大神

```sql

--创建
/*
create tablespace student1
datafile 'E:\oracle\product\10.2.0\oradata\dafiles\student.dbf'
size 10M
autoextend on

*/

drop table new_dept;

create table newdept
as select * from scott.dept;

create table salgrade
as select * from scott.salgrade;

--1.  创建一个学生表包含：学号、姓名、性别、年龄、出生日期等 字段。
 create table student(
   cno number(5) not null,
   cname varchar(10),
   sex char(2),
   age number(2),
   birthday date default sysdate
 )
 /
      
--2、修改学生表额外添加成绩字段。
alter table Student
  add (grade number(3,1));

--2-1、给学号加上主键约束。
alter table Student
  add  constraint PK_Student_cno primary key (cno);

--3、给性别加检查约束：提示 check(sex='男' or sex='女') 。
alter table Student
  add constraint CHK_Student_sex check (sex in('男','女'));

--4、年龄加检查约束，年龄在18~25之间；提示：check(age>=18 and age<=25)。
alter table Student
  add constraint CHK_Student_age check (age between 18 and 25);

--5、向表里插入记录来验证约束是否生效。
  insert into Student
         values (95009,'\张三','男',25,date'2011-02-15',96);
  insert into Student
         values (95002,'李四','女',23,date'2011-02-15',97);
  insert into Student
         values (95004,'王五','男',18,date'2019-8-15',96);
  insert into Student
         values (95003,'james','女',22,date'2019-8-15',96);
  insert into Student
         values (95005,'andy','男',18,'9-12月-14',96);

--6、查询出表中的所有记录。
select　* from Student;

--7. 查询出表中的所有记录。


--8. 选择部门为30中的所有员工
select * from new_scott;
select * from new_scott where  deptno = 30 ;
--9. 列出所有办事员（CLERK）的姓名，编号和部门编号
select distinct ename from new_scott where job='CLERK'; 

--10. 找出佣金高于薪金的员工。
select * from new_scott where comm>sal;

--11. 找出佣金高于薪金60%的员工
select * from new_scott where comm>(sal*0.6);


--12. 找出部门10中所有的（MANAGER）和部门20中所有办事员（CLERK）。
select * from new_scott 
       where (deptno=10 and job = 'MANAGER')  or   (deptno = 20 and job = 'CLERK');
--插入信息    
INSERT into new_scott values(7347,'Bob','CLERK',7876,to_date('1999-02-15','yyyy-mm-dd'),1000,0,10);
--
select * from new_scott where empno = '7347';
COMMIT;--提交
ROLLBACK;--回滚

--修改当前回话的日期格式
--alter session set sysdate_formate = 'YYYY-MM-DD  HH24:MI:SS';

--更新
update new_scott 
       set ename = 'james' where empno = 7347; 

--删除
delete new_scott
  where empno = 7347;

--DCL ||||| commit:提交   rollback：回滚     savepoint ：表即可回滚的点
      delete new_scott where ename = 'JONES';
      savepoint mark1;
      update new_scott set ename = 'chengui'
      where empno = 7369;
      savepoint mark2;
      rollback to savepoint mark1;
      
      
      
--链接符||
      select empno||'姓名'||ename||'工作'||job||'上司'||mgr||'入职时间'||hiredate||'工资'||sal||'佣金'||comm||'部门编号'||deptno from new_scott;
 
 
 --like 匹配
   select * from new_scott
   where job like '_A%';
   select * from new_scott 
   where ename like '\%' ;
   
   select ename , sal,nvl(comm,0),sal+nvl(comm,0)  salary  from new_scott;
   
   select * from new_scott 
   where mgr is not null;
   
--13. 找出收取佣金的员工的不同工作
      select distinct job from new_scott 
      where comm is not null;
      
--14. 找出不收取佣金或收取的佣金不低于100的员工
      select * from new_scott  
      where comm is null or comm<100
      order by empno

--15. 找出部门10中所有的（MANAGER）和部门20中所有办事员（CLERK）,
--和既不是经理又不是办事员但其薪金大于或等于2000的所有员工的详细资料
     select * from new_scott
     where  (deptno = 10 and job = 'MANAGER'  or deptno = 20   AND JOB = 'CLERK' )
     OR (JOB NOT IN ('MANAGER','CLERK') AND SAL>=2000);                                                                                                                                    
                                                                                                                                                  

--16. 找出各月倒数第三天受雇的所有员工
select * from new_scott
where extract(DAY from last_day(hiredate))- extract(DAY from (hiredate))=2;


select * from new_scott 


--17. 找出早于25年前受雇佣的员工
select * from new_scott
       where extract(year from systimestamp)- extract(year from (hiredate))>25;

--18. 以首字母大写其它字母小写的方式显示所有的员工的姓名。
select empno,INITCAP(LOWER(ename)),job,mgr,hiredate,sal,comm,deptno from new_scott;


--以首字母小写其它字母大写的方式显示所有的员工的姓名。
 --CONCAT(UPPER(substr(LOWER(ename),1,1))   ,  UPPER(substr(UPPER(ename),1)))
 
select empno,CONCAT((substr(LOWER(ename),1,1))   ,  UPPER(substr(UPPER(ename),1))),
       job,mgr,hiredate,sal,comm,deptno from new_scott;


--19. 显示正好为5字符的员工的姓名
select ename from new_scott
       where LENGTH(ename)=5;

--20. 显示不带有‘R’的与员工的姓名
select ename from new_scott
       where  ename not like '%R%';
--21. 显示所有员工姓名的前三个字符。

select SUBSTR(ename,1,3) from new_scott;

--22. 显示所有员工的姓名，用 A 替换 a 
select TRANSLATE(ename,'A','a')from new_scott;



--23. 显示 满25年 服务年限的员工的姓名 和受雇日期
select ename,hiredate from new_scott
       where extract(year from systimestamp)- extract(year from (hiredate))>25;
--MONTHS_BETWEEN(sysdate,hiredate)/12>25;


--24. 显示员工的详细资料，按姓名排序,姓名相同按工资降序排序。
select * from new_scott
       order by ename ,sal desc;

--25. 显示与员工的姓名和受雇日期，根据其服务年限，将最老的员工排在最前面。
select ename,hiredate , (extract(year from systimestamp)- extract(year from (hiredate))) time from  new_scott
       order by time  desc;



--26. 显示所有员工的姓名，工作和薪金，按工作的降序排序，若工作相同按薪金排序
select ename,job,sal from new_scott
       order by job desc,sal desc;


--select * from new_scott;

--decode用法（条件，属性1，值1，属性2，值2，……，默认值）
    select ename,decode(job,'CLERK','办事员','MANAGER','经理',
    'SALESMAN','销售员','ANALYST','分析师','老板') from new_scott;
--27. 显示所有员工的姓名，加入公司的年份和月份，按受雇日期所在的月排序，若月份相同，则将最早的年份的员工排在最前面
select ename,to_char(hiredate,'yyyy"年"mm"月"') from new_scott
order by extract(MONTH from hiredate) ,extract(YEAR from hiredate)
;
--经典方法
select ename,job,TO_CHAR(hiredate,'yyyy')year,to_char(hiredate,'mm')month,form employee order by month,year;

/*-------------------------------------------------------- 分组查询 -------------------------------------*/
		 
--28. 显示非销售人员工作名称以及从事同一工作雇员的月工资的总和，并且要满足从事同一工作的雇员的月工资合计大于$5000，
--输出结果按月工资排序。
select job,sum(sal) from new_scott
group by job
having sum(sal)>5000
order by sum(sal);



--29. 查询出各部门的部门编号以及各部门的总工资和平均工资。
select  deptno ,to_char(sum(sal),'L999,999,999.00'),to_char(avg(sal),'L999,999,999.00')from new_scott
group by deptno;



select * from new_scott;

--30. 按男生和女生统计JAVA和ORACLE成绩的总分和平均分？
select sum(JAVASCORE) AS java总成绩 ,AVG(JAVASCORE) java平均成绩,SUM(ORACLESCORE)oracle总成绩 ,AVG(ORACLESCORE) Oracle平均成绩 FROM STUDENT2
group by GENDER ;


--	1）	建表
create table STUDENT2
(
  STUNO       CHAR(4) not null primary key,
  STUNAME     VARCHAR2(20),
  GENDER      CHAR(2),
  JAVASCORE   INTEGER,
  ORACLESCORE INTEGER
);
--	2）	插入记录
INSERT INTO STUDENT2 VALUES('1000','JAMES','男',88,78);
INSERT INTO STUDENT2 VALUES('1001','JACK','男',86,79);
INSERT INTO STUDENT2 VALUES('1002','ANDY','女',76,78);
INSERT INTO STUDENT2 VALUES('1003','SAMMY','女',77,76);

INSERT INTO STUDENT2 VALUES('1004','frank','男',88,78);
INSERT INTO STUDENT2 VALUES('1005','bob','男',86,79);
INSERT INTO STUDENT2 VALUES('1006','july','女',76,78);
INSERT INTO STUDENT2 VALUES('1007','mark','女',77,76);
--	统计成绩
select stuno,stuname,javascore+oraclescore 总成绩,(javascore+oraclescore)/2  平均成绩 from STUDENT2
order by 总成绩 desc;

select gender,sum(javascore),avg(javascore),sum(oraclesore),avg(oraclescore) form student2 
group by gender;

create table newdept
as select * from scott.dept;


select * from newdept;
select * from new_scott;
--全链接
select * from newdept full join new_scott on new_scott.deptno = newdept.deptno;

select * from newdept left join new_scott on new_scott.deptno = newdept.deptno;

select * from newdept right join new_scott on new_scott.deptno = newdept.deptno;

--coalesce(expr1,expr2,expr3,....exprn)  NVL  同，前为空，则一直向后搜寻，最后默认为exprn

create table salgrade
as 
select * from scott.salgrade;


select e.*
 from new_scott n, salgrade s
 where e.sal between s.lowsal and  s.hisal;


select '工号为：'|| e.empno||'的员工的名字是'||e.ename||'的上司是'||temp.empno
       ||temp.ename  from new_scott e join new_scott temp on e.mgr = temp.empno;

--工资分类
select e.* ,f.* from new_scott e join salgrade f
on e.sal >f.losal and e.sal< f.hisal;


create trigger tri_salgrade_insert
after insert
on salgrade 
begin
     RAISE_APPLICATION_ERROR ('YOU MAY ERRROR');
end;


 INSERT  into salgrade 
 values(6,4000,9999)
 
 

--查询在‘NEW YORK’工作，工资高于2000的员工以及员工的工资等级。
--方法一：
select n.*,s.grade ,dept.*
 from new_scott n , salgrade s,newdept dept
 where n.deptno = dept.deptno and n.sal>s.losal and n.sal<s.hisal and n.sal>2000 and dept.loc = 'NEW YORK'
--方法二：
select n.*,s.grade,dept.*
from new_scott n join salgrade s
on n.sal>s.losal and n.sal<s.hisal
join newdept dept
on dept.deptno = n.deptno and n.sal>2000 and dept.loc like 'NEW YORK';
 



select n.empno,n.ename,sal,loc
from new_scott n, (select * from newdept where newdept.loc = 'NEW YORK' and deptno = 10 ) S         
where n.deptno = s.deptno;




 
select * from newdept;
select * from NEW_SCOTT;
select * from newdept;


/*-----------------------------------------------第三章 高级查询  --------------------------------

--A. 查询部门在‘NEW YORK’工资低于4000，不是‘CLERK’的员工？


--B. 查询部门在‘CHICAGO’，在1981年入职，工资在2000~4000的员工？


/*----------------------------------------------- 子查询 -----------------------------------------

子查询注意的问题：
	1、要将子查询发在圆括号内。

	2、子查询可出现在WHERE子句、FROM子句、SELECT列表(此处只能是一个单行子查询)、HAVING子句中。

	3、子查询不能出现在主查询的GROUP BY语句中。

	4、子查询和主查询使用表可以不同，是要子查询返回的结果能够被主查询使用即可。

	5、一般不会在子查询中使用ORDER BY语句，但在TOP-N(只需前几条记录)分析中必须使用ORDER BY语句。

	6、单行子查询只能使用单行操作符，多行子查询只能使用多行操作符。

	7、采用合理的缩进和换行来提过SQL语句的可读性。

	8、子查询中的空值问题。
*/	

--31. 查询部门名称为SALES和ACCOUNTING的员工信息
/*
1	7499	ALLEN	SALESMAN	7698	1981/2/20	1600.00	300.00	30	30	SALES	CHICAGO
2	7521	WARD	SALESMAN	7698	1981/2/22	1250.00	500.00	30	30	SALES	CHICAGO
3	7654	MARTIN	SALESMAN	7698	1981/9/28	1250.00	1400.00	30	30	SALES	CHICAGO
4	7698	BLAKE	MANAGER	7839	1981/5/1	2850.00		30	30	SALES	CHICAGO
5	7782	CLARK	MANAGER	7839	1981/6/9	2450.00		10	10	ACCOUNTING	NEW YORK
6	7839	KING	PRESIDENT		1981/11/17	5000.00		10	10	ACCOUNTING	NEW YORK
7	7844	TURNER	SALESMAN	7698	1981/9/8	1500.00	0.00	30	30	SALES	CHICAGO
8	7900	JAMES	CLERK	7698	1981/12/3	950.00		30	30	SALES	CHICAGO
9	7934	MILLER	CLERK	7782	1982/1/23	1300.00		10	10	ACCOUNTING	NEW YORK
*/
select * from new_scott e,newdept n
where e.deptno = n.deptno and n.dname in ('SALES','ACCOUNTING');
			  
      
--32. 查询不是经理的员工的信息(使用in 或 not in来做)
/*
1	7369	SMITH	CLERK	7902	1980/12/17	800.00		20
2	7499	ALLEN	SALESMAN	7698	1981/2/20	1600.00	300.00	30
3	7521	WARD	SALESMAN	7698	1981/2/22	1250.00	500.00	30
4	7654	MARTIN	SALESMAN	7698	1981/9/28	1250.00	1400.00	30
5	7844	TURNER	SALESMAN	7698	1981/9/8	1500.00	0.00	30
6	7876	ADAMS	CLERK	7788	1987/5/23	1100.00		20
7	7900	JAMES	CLERK	7698	1981/12/3	950.00		30
8	7934	MILLER	CLERK	7782	1982/1/23	1300.00		10
*/
 select * from new_scott 
 where empno not in (select distinct mgr from new_scott where mgr is not null);


--33. 查询工资比10号部门员工中任意一个低的员工信息(13)
 select * from new_scott e
 where e.sal < any (select distinct sal from new_scott where deptno = 10)


--34. 查询工资比10号部门都要低的员工信息
 select * from new_scott e
 where e.sal <all (select distinct sal from new_scott where deptno = 10)



--35. 查询出部门名称，部门员工数，部门平均工资，部门最低工资雇员的姓名，及工资等级
select n.dname,t.deptno,t.total,t.ag,t.mi,s.grade,e.ename
from new_scott e ,salgrade s,newdept n,(select deptno, count(*) total, avg(sal) ag, min(sal) mi  from new_scott  group by deptno) t
where e.deptno = t.deptno 
      and e.sal>=s.losal 
      and e.sal<=s.hisal
      and n.deptno = e.deptno
      and e.sal in (select mi from (select deptno, count(*) total, avg(sal) ag, min(sal) mi  from new_scott  group by deptno) t)
   
--36. 列出最低薪金大于1500的各种工作及此从事此工作的全部雇员人数
/*
1	ANALYST	2
2	MANAGER	3
3	PRESIDENT	1
*/
--方法一
select distinct(e.job),t.total
from new_scott e,(select job ,min(sal) mi,count(*) total from new_scott group by job)t
where e.job  = t.job  
  and t.mi > 1500;
--方法二
select e.job, count(*)人数
       from
       emp e
       group by e.job having (select min(sal) from emp where e.em)


--37. 求出在'salesman'部门工作的员工姓名，假设不知道销售部的部门编号
select distinct e.ename from new_scott e,newdept n
where e.deptno = (select distinct deptno from new_scott where job = UPPER('salesman'));



--38. 列出薪金高于公司平均薪金的所有员工，所在部门，上级领导，公司的工资等级

select e.ename,e.sal,n.dname,e.mgr,s.grade
from new_scott e,newdept n,salgrade s
where e.sal >(select avg(sal) from new_scott )
and e.deptno = n.deptno
and e.sal >s.losal and e.sal <=s.hisal;

--39. 列出于“SCOTT”从事相同工作的所有员工及部门名称

select e.ename ,n.dname 
from new_scott e,newdept n
where e.job = (select job from new_scott where ename = 'SCOTT')
            and e.deptno = n.deptno;

--40. 查询和SMITH部门相同 岗位相同的人
select * from new_scott e, newdept n
where e.deptno = (select deptno from new_scott where ename = 'SMITH' )
      and e.job = (select job from new_scott where ename = 'SMITH')
      and e.deptno = n.deptno;

--41. 和ALLEN同部门，工资高于MARTIN的雇员有哪些
      select * from new_scott e
      where e.deptno = (select deptno from new_scott where ename = 'ALLEN')
      and e.sal > (select sal from new_scott where ename = 'ALLEN')

 
--42. 比blake工资高的雇员有哪些? 
  select * from new_scott e
  where e.sal > (select sal from new_scott where ename = upper('blake'))
  


--43. 高于30部门最高工资的雇员有哪些?
select * from new_scott e
  where e.sal > (select max(sal) from new_scott where deptno = 30)



--44. 查询scott用户下的emp表中所有的经理的信息(此操作子查询会返回多行记录)
select * from new_scott 
where empno in (select mgr from new_scott);




--45. 工资高于本部门平均工资的人（拿上游工资的人）有哪些？****
select e.*
from new_scott e
where e.sal >(select avg(sal) from new_scott where deptno = e.deptno)


select e.deptno ,e.empno,e.sal,e.ename from new_scott e,(select deptno,avg(sal) ag from new_scott group by deptno ) t
where t.deptno = e.deptno and e.sal > t.ag
group by e.deptno ,e.empno,e.ename,e.sal;



--46. 工作和部门与SMITH相同，工资高于JAMES的雇员有哪些?
select  * from new_scott e,newdept n
where e.job = (select job  from new_scott where ename = 'SMITH')
    and e.deptno = n.deptno
    and n.deptno = (select new_scott.deptno from new_scott,newdept  where ename = 'SMITH' and new_scott.deptno = newdept.deptno )
    and e.sal >(select sal from new_scott where ename = 'JAMES');



/*---------------------------------------------- 多行子查询 --------------------------------------*/

--47. 列出薪金等于部门30中员工的薪金的所有员工的姓名和薪金
select e.ename,e.sal from new_scott e
where e.sal in (select sal from new_scott where deptno = 30);

--48.列出薪金大于部门30中员工的薪金的所有员工的姓名和薪金
select e.ename,e.sal from new_scott e
where e.sal>any (select sal from new_scott where deptno = 30);

--49.列出每个部门工作的员工数量，平均工资和平均服务年限
select deptno ,count(*) 员工数量 ,to_char(avg (sal),'L999,999.00'),to_char(avg(months_between(sysdate,hiredate)/12),'999.00') from new_scott
group by deptno;

select *
from new_scott e,


--删除没有员工的部门
--方法一
delete newdept
where deptno in (select deptno from newdept MINUS select distinct deptno from new_scott)and deptno <> 40;
--方法二
delete newdept
where deptno not in (select deptno from new_scott)



--创建同义词：
-- 1)创建私有同义词   注意：需要sys授权：grant create SYNONYM to stu;

create or replace public synonym emp for new_scott ;

-- 2）创建共有同义词  注意：需要sys授权：grant create public SYNONYM to stu;
create public synonym dept1 for newdept;
--drop public synonym dept;

--创建序列：
create sequence emp_seq
       start with 8002
       increment by 1
       minvalue 8002
       maxvalue 999999999999
       cycle
       cache 20
--select emp_seq.nextval from emp_seq;--执行emp_seq.currval 之前一定要执行一次 emp_seq.nextval
select emp_seq.currval from dual;
       commit;
       drop sequence emp_seq;

insert into emp(empno,ename,job,mgr,deptno)
values(emp_seq.nextval ,'james','CLERK',7698,20)

--创建视图
create view emp_view
as 
   select * from emp;
   
select * from emp_view;
--删除视图
drop  view emp_view;

select empno,ename,dname from emp e,newdept d where e.deptno = d.deptno AND e.ename<>'SCOTT' AND e.job=(select job from emp where ename='SCOTT');
--创建索引
create  index emp_job on emp(job);
--验证索引
select * from emp where job = upper('salesman');



--第一个小例子
BEGIN
   dbms_output.put_line('最简单的PL/SQL');
END;


declare 
    v_money number(4,1) := 2.3;
begin
     dbms_output.put_line(v_money);
end;

--第二个小例子
DECLARE
   v_ename VARCHAR2(20); 			   --运行时在输入
   v_ename2 v_ename%type;			   --引用另一个变量的类型
   v_empno NUMBER(4) not null := 7999; --初始化
   v_empno2 NUMBER(4);  			   --运行时在输入
   v_job emp.job%type; 		   --变量与job列的数据类型和宽度一致
   v_hiredate emp.hiredate%type default sysdate; --赋默认值
   v_sal CONSTANT emp.sal%type := 3000; 		  --常量
   
BEGIN
     --赋值方式 1：
     v_ename := '&请输入员工姓名：'; --&号表示一个替代变量，可在运行时输入值，字符串需用单引号括起来
     v_empno2 := &请输入员工编号;
     
     --赋值方式 2： select 列名列表 into 变量列表（对号入座）
	   --但是要注意现在给变量赋值不能返回多行，要处理多行的查询结果需要用到游标
     SELECT job INTO v_job FROM emp WHERE empno=v_empno2; --8001

    dbms_output.put_line('员工编号:'||v_empno2||', 员工姓名：'||v_ename||', 职务：'||v_job||', 入职时间：'||v_hiredate);
END;

--更新方式(emp 为  new_scott 的同义词 synonym)
declare
      v_empno emp.empno%TYPE;
      v_ename emp.ename%TYPE;
      v_sal   emp.sal  %TYPE;
begin
      v_empno:=&员工工号;
      select ename,sal into v_ename,v_sal from emp where emp.empno = v_empno;
      dbms_output.put_line(v_empno||'   的名字是：'||v_ename||'    薪水是：'||v_sal);
      if v_sal < 3000 then
      update emp 
             set sal = sal +500 where empno  = v_empno;
             dbms_output.put_line(v_ename||'加薪完成！');
      else
			       dbms_output.put_line('薪水已经很高了！');
      end if;  
end;


--IF-ELSE案例
--create or replace procedure emp_add_sal
--is
     -- begin 
      declare 
         v_empno emp.empno%
         rec_emp emp%rowtype;
      begin
         v_empno := &员工编号;
         --更新前
         select * into rec_emp from emp where empno = v_empno;
         dbms_output.put_line('更新前：员工编号：'||rec_emp.empno||', 员工名字：'||rec_emp.ename||'， 员工工资：'||rec_emp.sal);
               if(rec_emp.sal<3000) then
                    update emp set sal = sal-500 where empno = v_empno;
               else
                    update emp set sal = sal-200 where empno = v_empno;
               end if;
        -- commit;
        --更新后
        select * into rec_emp from emp where empno = v_empno;
         dbms_output.put_line('更新后：员工编号：'||rec_emp.empno||', 员工名字：'||rec_emp.ename||'， 员工工资：'||rec_emp.sal);
        --处理没有找到数据的异常
           exception 
                 when NO_DATA_FOUND   THEN
                       dbms_output.put_line('查无此人！');
                 when others then
                       dbms_output.put_line('错误代码：'||SQLCODE  ||',错误信息：'||SQLERRM);
      end;
--end emp_add_sal;
/

--drop procedure emp_add_sal;



--CASE_WHEN


/* ---------------------------------------第五章 PL/SQL 作业-----------------------------------*/

--A. 使用LOOP循环求1-100之间的素数

/*
50. 打印99乘法表
	1*1=1
	2*1=2 2*2=4
	3*1=3 3*2=6 3*3=9
*/



--第一种方式
declare 
   v_score integer default 0;
begin
   v_score:= &成绩;
   case 
        when v_score >=80 then
             dbms_output.put_line('优秀');
        when v_score >=70and v_score<80 then
             dbms_output.put_line('良好');
        when v_score >=60 and v_score <70 then
             dbms_output.put_line('一般');
        else
            dbms_output.put_line('不及格');  
    end case; 
end;

--第二种方式
declare 
   v_grade char(2);
begin
     case '&grade'  -- 此处一定注意：如果是字符型一定要加上   ''   否者报错，，，如果是数值型则不需要加 ’‘  
          when 'A'  then
             dbms_output.put_line('优秀');
          when 'B' then
             dbms_output.put_line('良好');
          when 'C' then
             dbms_output.put_line('一般');
          else
             dbms_output.put_line('不及格');
     end case;
end;


--循环
declare 
  v_row int:= &行数;
  v_counter1 int :=1;
  v_counter2 int :=1;
begin 
      loop
          v_counter1 := 1;
          loop
              dbms_output.put('*');
              exit when v_counter1=v_counter2;
              v_counter1:=v_counter1+1;
          end loop;
          dbms_output.new_line;
          v_counter2:=v_counter2+1;
          exit when v_counter2 = v_row;
      end loop;

end;

--使用LOOP循环求1-100之间的素数
declare 
   v_prime int :=2;
   v_counter int default 1;
   counter int default 0;
   num int default 0;--统计个数
begin
     loop 
          counter:=0;--计数器（每次重新开始）
          v_counter:=2;--除数 从 2~v_prime 除（每次重新开始）
          loop
              if v_prime mod v_counter = 0 then
                 counter:=counter+1;
              end if;
              
              exit when v_counter = v_prime  or counter > 2;  --内循环结束条件  （2个条件满足一个即可跳出循环）
                   v_counter:=v_counter+1;--除数自加
          end loop;
          
          if counter = 1  then--输出语句
          num:= num+1;
             if num<>1 then
                dbms_output.put(',');
             end if;
             dbms_output.put(v_prime);
          end if;
          
          exit when v_prime=100;--外循环结束条件
          v_prime:= v_prime+1;--外循环自加
     end loop;
     dbms_output.new_line;
end;




--九九乘法表
declare 
        counter1 int default 1;
        counter2 int default 1;
 begin
        counter1 :=&乘法表范围;
      for i in 1..9 loop
          for j in 1..i loop
               dbms_output.put(i||'*'||j||'='||i*j);
               if(j<>i)then dbms_output.put(',');
               end if;
          end loop;
      dbms_output.new_line;
      end loop;
 end;
 
 
--52.使用FOR循环求1-100之间的素数
declare
       counter int :=0;
begin
     for i in 2..100 loop
         counter:=0;
         for j in 2..i loop
             if mod(i,j)=0 then 
                counter:=counter+1;
                end if;
         exit when counter >1;
         end loop;
         if counter = 1 then
             dbms_output.put(i||',');
          end if;
     end loop;
     dbms_output.new_line;
end;


/*
练习：
根据部门名称，按以下格式打印"RESEARCH"部门所有人员姓名：
	部门名称：RESEARCH
	部门人员：SMITH,JONES,FORD
*/
declare
    counter int ;  
    v_ename emp.ename%type;
begin
     dbms_output.put_line('部门名称：'||'RESEARCH');
     dbms_output.put('部门人员：');
     select count(*) into counter from emp e,newdept n  --统计有多少个符合条件的
     where e.deptno = n.deptno
           and n.dname = 'RESEARCH';
           
     for i in 1..counter loop  --由于每次只能打印一个，所以用for循环
         select ename into v_ename from
         (
          select rownum r,ename  --用伪列rownum 为每一个符合的条件做个编号，以便输出
                  from emp e join newdept n
                  on e.deptno = n.deptno
                  and n.dname = 'RESEARCH'
         )a
         where a.r = i;  
         dbms_output.put(v_ename);
         if i<>counter then
            dbms_output.put(',');
         end if;
     end loop;
     dbms_output.new_line();
end;




--51. 根据工资查询员工姓名。如果此员工不存在（发出NO_DATA_FOUND异常），则打印相应的提示信息。

declare 
   v_sal emp.sal%type;
   v_ename emp.ename%type;
begin
   v_sal := &请输入工资;
   select ename into v_ename from emp
          where sal = v_sal;
   dbms_output.put_line('工资为：'||v_sal||'的员工姓名是：'||v_ename);
   exception
     when NO_DATA_FOUND then
          dbms_output.put_line('查无此人！');
end;

---自定义异常
declare 
   v_empno emp.empno%type:=&加薪员工的工号;
   v_count int default 0;
   v_sal emp.sal%type;
   invlite_sal exception;--自定义异常
   no_found exception;   --自定义异常
begin
     v_sal:=&工资;
     if v_sal<0 then 
       raise invlite_sal; -- 手动抛出异常
     end if;
     select count(*) into v_count from emp where empno = v_empno;
     if(v_count = 0) then 
        raise no_found ;  --手动抛出异常
     end if;
     exception            --捕获异常
        when no_found  then 
             dbms_output.put_line('没有符合的信息！');
        when invlite_sal then
             dbms_output.put_line('工资不可为负！');   
end;

--记录类型
declare
    type rec_emp_type is record(
         empno emp.empno%type,
         ename emp.ename%type,
         sal emp.sal%type   
    );
    no_found exception;
   rec rec_emp_type ;
   v_count int:=0;
   v_empno emp.empno%type:=&请输入员工工号;
begin
     select count(*)into v_count from emp where empno = v_empno;
     if v_count = 0 then
         raise no_found;
     end if;
     select empno,ename,sal into rec from emp where empno = v_empno;
     dbms_output.put_line('员工工号：'||rec.empno  ||'员工姓名：'||rec.ename  ||'员工薪水：'||rec.sal);
     
     exception 
     when no_found then 
    -- raise_application_error(-20010,'查无此人');
       dbms_output.put_line('查无此人');
end;
 
 
 
 -------------------------------------------第六章   游标---------------------------------------------------
 ----游标(loop访问)
 declare
        cursor  cv_emp is          --1.定义游标  cursor 游标名  is   （select  语句）
                select e.* ,rownum from emp e;
        rec_emp cv_emp%rowtype;    --定义一个游标类型的变量，保存每次结果
 begin 
        open cv_emp;               --2.打开游标  open 游标名；
        loop
            fetch cv_emp into rec_emp;  -- 3.从游标中获取数据  ： fetch  有标明   into　变量　　　
            dbms_output.put_line(rec_emp.empno||'  '||rec_emp.rownum);
          exit when cv_emp%NOTFOUND;
        end loop;
        close cv_emp;            　--4.关闭游标  close 游标名
 end; 
 --while访问
 declare
        cursor cv_emp is
               select e.*,rownum from emp e  ;--order by empno
        rec_emp cv_emp%rowtype;
 begin
        open      cv_emp;
              fetch cv_emp into  rec_emp;     --因为游标是从0开始，第零个没有数据，所以要跳过，
        while cv_emp%FOUND LOOP 
              dbms_output.put_line(rec_emp.empno||' '||REC_EMP.ROWNUM);   
              fetch cv_emp into  rec_emp;
             
        END LOOP;
        close  cv_emp;
 end;
 
  --for  
  declare
         cursor cv_emp is
         select e.*,rownum from emp e  ;--order by empno
  begin
       for rec_emp in cv_emp loop   --for 不需要特意定义   游标变量，因为   ORACLE   自动分配计数器变量
             dbms_output.put_line(rec_emp.empno||'  '||rec_emp.rownum);
       end loop ;
  end;
  
  
--for 升级版
begin 
      for rec_emp in (select e.*,rownum from emp e  )   loop   --直接用select 语句作为 匿名的  cursor 
        dbms_output.put_line(rec_emp.empno||'  '||rec_emp.rownum);
      end loop ;
end;
  
 
 
 /* ---------------------------------------------第六章 游标管理 作业----------------------------------*/

/*
（一）什么是游标：

（二）隐式游标：在 PL/SQL 程序中执行DML SQL 语句时自动创建隐式游标。 并且只能访问最近执行的一条DML语句，查询只能返回一行。

（三）显式游标用于处理返回多行的查询。
		1）无参数的显式游标
		2）带参数的显式游标
		   语法：
		      cursor 游标名(参数名  类型) is  select_statement;

（四）隐式游标的特性：
       1）在PL/SQL中使用DML语句时自动创建隐式游标
       2）隐式游标自动声明、自动打开和自动关闭，其名为: SQL
       3）通过检查隐式游标的属性可以获得最近一次执行的DML 语句的信息
       4）游标的属性有：
          （1）%FOUND – SQL语句查询或影响了一行或多行时为 TRUE
          （2）%NOTFOUND – SQL语句没有影响任何行时为 TRUE
          （3）%ROWCOUNT – SQL语句影响的行数
          （4）%ISOPEN  - 检查游标是否打开，隐式游标始终为FALSE
		  
（五）使用游标更新行
       1) 查询时使用 select .. from table where 条件 for update [of column [nowait]]子句锁定需要更新的行或列。
	     2) update employee set sal=sal-2 where current of 游标;
*/

--54. 显示EMP中的第四条记录。 如：游标%rowcount=4
declare
       cursor cv_emp is select * from emp;
       rec_emp cv_emp%rowtype;
begin
open cv_emp;
     loop 
         fetch cv_emp into rec_emp;    --先移动，再读取
         if cv_emp%rowcount = 4 then 
            dbms_output.put_line(rec_emp.empno||'   '||rec_emp.ename);
         end if;
         exit when cv_emp%rowcount = 4;
     end loop;
     close cv_emp;
end;


/*
55：针对所有部门，按以下格式打印各部门人员姓名：
	部门名称：RESEARCH
	部门人员：SMITH,JONES,FORD

	部门名称：ACCOUNTING
	部门人员：CLARK,KING,MILLER
	
	两种实现提示：
	1）循环每个部门，用其部门号作条件去查员工表
	2）用显示cursor完成
	3）要求用FOR，会用到嵌套循环。
*/

--方法一：不带参数游标
declare 
      
begin
        for rec_emp in (select dname,deptno from newdept d) loop
                    dbms_output.put_line('部门名称：'||rec_emp.dname);
                    dbms_output.put('部门人员：');
           for rec_tep in (select e.ename ename ,e.deptno deptno from emp e join newdept n on e.deptno = n.deptno ) loop 
                     if rec_tep.deptno = rec_emp.deptno then
                             dbms_output.put(rec_tep.ename||',');
                    end if;
           end loop;
                    dbms_output.new_line;
        end loop;
end;
--方式二：带参数游标
declare 
       cursor cv_dept  is
               select * from newdept ;
       cursor cv_employee (cp_deptno emp.deptno%type)--传递一个员工部门参数，根据参数锁定该部门信息
               is select * from emp where deptno = cp_deptno;
begin
     for cp_dept in cv_dept loop
          dbms_output.put_line('部门名称：'||cp_dept.dname);
                dbms_output.put('部门员工：');
                
          for cp_emp  in cv_employee(cp_dept.deptno) loop  --内循环中的参数是外循环给的，通过每一个部门参数，打印结果
              dbms_output.put(cp_emp.ename||',');      
          end loop;
          
          dbms_output.new_line;
          dbms_output.new_line;  --空一行
     end loop;
end;       
        



/*
56. 对所有员工,如果该员工职位是MANAGER，并且在DALLAS工作那么就给他薪金加15％；如果该员工职位是CLERK，并且在NEW
   YORK工作那么就给他薪金扣除5％;其他情况不作处理
*/
--方式二： 游标
declare
    cursor cv_emp(p_job emp.job%type,p_loc newdept.loc%type)is 
                        select e.sal,e.deptno,n.loc from emp e,newdept n where e.deptno = n.deptno and p_job = e.job and p_loc = n.loc for update;
    v_job emp.job%type;
    v_loc newdept.loc%type;
begin
     v_job:='&工作：';
     v_loc:='&地点：';
     for rec_emp in cv_emp(v_job,v_loc) loop 
         if v_job='MANAGER' and v_loc = 'DALLAS'  then
          update emp set sal= sal*(1+0.15) where current of cv_emp; 
         end if;
         if v_job='CLERK' and v_loc = 'NEW YORK'  then
          update emp set sal= sal*(1-0.05) where current of cv_emp; 
         end if;
     end loop;
end;

--方式一:
declare
       cursor cv_emp is select empno ,e.job job, n.loc loc from emp e  join newdept n on e.deptno = n.deptno for update;
begin
     FOR rec_emp in cv_emp loop
         if rec_emp.job ='MANAGER' and rec_emp.loc ='DALLAS'  then 
            update emp set sal= sal*(1+0.15) where empno = rec_emp.empno;   --where current of cv_emp;
             DBMS_OUTPUT.PUT_LINE('加薪10%完成！');
         else  if rec_emp.job ='CLERK' and rec_emp.loc ='NEW YORK'   then 
                update emp set sal= sal*(1-0.05) where empno = rec_emp.empno;   -- where current of cv_emp;
                   DBMS_OUTPUT.PUT_LINE('减薪5%完成！');
               end if;
         end if;
     end loop;
end;

--验证过程
select empno ,e.job job, n.loc loc ,e.sal from emp e  join newdept n on e.deptno = n.deptno
   and e.job = 'MANAGER' and n.loc = 'DALLAS';
   --1	7566	MANAGER	DALLAS	4275.00
   --1	7566	MANAGER	DALLAS	5653.69
select empno ,e.job job, n.loc loc,e.sal from emp e  join newdept n on e.deptno = n.deptno
   and e.job = 'CLERK' and n.loc = 'NEW YORK';
--1	7934	CLERK	NEW YORK	1300.00
--1	7934	CLERK	NEW YORK	1433.25



--57.(使用游标更新行) 编写一PL/SQL，对所有的"销售员"(SALESMAN)增加佣金500.
 --      1)   查询时使用 select .. from table where 条件 for update [of column [nowait]]子句锁定需要更新的行或列。
	--     2)   update employee set sal=sal-2 where current of 游标;
declare 
     cursor  cv_emp(p_job emp.job%type)  is select * from emp for update OF  sal nowait;
     v_job emp.job%type;
begin 
      v_job:= '&请输入工作';
    for rec_emp in cv_emp(v_job)  loop
           update emp set comm= comm+500 where current of cv_emp;   --update employee set sal=sal-2 where current of 游标;
    end loop;
end;


--58. 编写一PL/SQL，以提升两个资格最老的"CLERK(职员)"为"HIGHTCLERK(高级职员)"。（工作时间越长，优先级越高）
--alter table new_scott modify  ( job varchar2(10));

declare
       cursor cv_emp is select * from emp order by hiredate ;
begin
     for rec_emp in cv_emp loop
         if cv_emp%rowcount <=2 then 
            update emp set job = 'HIGHTCLERK'  where empno = rec_emp.empno;
         end if;
     end loop;
end;

--方式二：游标方式
declare
       --游标选定job为CLERK    的员工，并按受雇时间
       cursor cv_emp is 
              select hiredate ,job from emp where job = 'CLERK' order by hiredate  for update ;
begin
       for rec_emp in cv_emp loop
       if cv_emp%rowcount <3 then --限制只修改两个
           update new_scott set job = 'HIGHTCLERK' where current of cv_emp;
           end if;
       end loop; 
end;

 select * from new_scott;
/*
59. 对直接上级是'BLAKE'的所有员工，按照参加工作的时间加薪：
	81年6月以前的加薪10％
	81年6月以后的加薪5％
*/

declare
   cursor cv_emp is select * from emp where mgr = (select empno from emp where ename = 'BLAKE');
begin
      for rec_emp in cv_emp loop
        if MONTHS_BETWEEN(rec_emp.hiredate,to_date('1981/6/1','yyyy/mm/dd'))>0  then 
             update emp set sal=sal*(1+0.05) where empno = rec_emp.empno;
        else
             update emp set sal=sal*(1+0.1) where empno = rec_emp.empno ;
        end if;
      end loop;
 end;
--方式二：游标方法
declare 
        cursor cv_emp(p_ename emp.ename%type) is 
           select * from emp where mgr = (select empno from emp where ename = p_ename)  for update  of sal;
        v_ename emp.ename%type;
begin
        v_ename:= '&上级姓名';
        for rec_emp in cv_emp(v_ename) loop
            if MONTHS_BETWEEN(rec_emp.hiredate,to_date('1981/6/1','yyyy/mm/dd'))>0  then 
             update emp set sal=sal*(1+0.05) where current of cv_emp;
        else
             update emp set sal=sal*(1+0.1) where current of cv_emp ;
        end if; 
            
        end loop;
end;




/*----------------------------------------------------第7章 过程和函数----------------------------------------------*/
/*
60. 编写一个给指定雇员加薪10%的过程：Raise_Sal(p_ename)，这之后，检查如果已经雇佣该雇员超过60个月，则给他额外加薪3000.
*/
create or replace  procedure Raise_Sal(p_ename emp.ename%type)
is
  cursor cv_rise(p_ename emp.ename%type)  is select * from  emp where ename = p_ename for update of sal;
  cursor cv_emp is select * from emp where months_between(sysdate,hiredate)>60  for update of sal;--后一部分
begin
     for emp in cv_rise(p_ename) loop
          update  emp set sal=sal*(1+0.1) where current of cv_rise ;
     end loop;

     for emp in cv_emp loop
         update emp set sal = sal+3000 WHERE current of cv_emp ;
     end loop;
     --commit;
     exception 
      when others then
      rollback;
      dbms_output.put_line('操作有误，已经撤销！');
end;


--调用
declare
     v_ename emp.ename%type;
begin
     v_ename:='&需要加薪的员工的名字：';
     Raise_Sal(v_ename);
end;
--验证
select * from emp;


/*
61. 编写一个过程打印出99的乘法表。
*/
create or replace procedure pro_9_9
is

begin
   for i in 1..9 loop
       for j in 1..i loop
         dbms_output.put(i||'*'||j||'='||i*j||'  ');
       end loop;
       dbms_output.new_line;
   end loop;
end;


--调用过程
begin
     pro_9_9;
end;
/*
62.编写一个过程完成两个数字的交换
*/
create or replace procedure date_change(no1 in out int,no2 in out integer )
is 
   v_temp int ;
begin
     v_temp := no1;
     no1 := no2;
     no2 := v_temp;
end;
--调用
declare
 v_on1 int :=&请输入第一个数字;
  v_on2 int :=&请输入第二个数字;
begin 
      date_change(v_on1,v_on2);
      dbms_output.put_line('交换后：第一个数字：'||v_on1||'   第二个参数：'||v_on2);
end;



/*
                                                              --drop table log_msg cascade constraint;//删除表同时删除约束
63. 编写三个过程完成
	(1)开户功能。open_account(.....);
  
	(2)两个账户的转账功能。transaction(from帐户，to帐户,money)
       a)检查帐户是否存大？不存在的情况出异常。
       b)转入转出帐户必须要在一个事务里进行。
         
	(3)根据转出账号和交易时间段 可查询转出账号的所有交易记录。最后还需要返回该时间段转出的总金额。
      a)向“交易日志表”插入数据。
      b)交易号的生成规则：'T10000-'||trans_seq.nextval --> T10000-1000
      
  
	表结构如下：
	 1. 账户表：账号（6722 6738 xxxx yyyy）,户名，身份证号，余额，开户时间。
	    
      说明：账号后8位数随机生成。dbms_random.value函数可以得一个八位小数的随机小数。
	       select dbms_random.value from dual;
select trunc(dbms_random.value*100000000,0) from dual;
	 2. 交易日志表：交易号(T10000-999)，转出账户，转入账户，交易金额，交易时间systimestamp。
	    说明：交易号使用序列生成。
*/
--1.custome_account表
create table custome_account (
       accountno varchar2(16) not null primary key ,
       aname varchar2(6)  not null ,
       idno  varchar2(18)unique,
       money number(20,3) check (money>0),
       opentime date default systimestamp    
)



--1.1建立账号表视图
create or replace  view acc 
as
select * from custome_account;

--2.logtable 表
create table log_msg (
       tradeno varchar2(11)not null primary key,
       fromaccount varchar2(16) ,
       toaccourt   varchar2(16),
       money number(20,3),
       tradetime date default systimestamp,
       constraint  FK_ACCOUT_LOG_FROMACCOUT foreign key(fromaccount)  referenceS   custome_account(accountno),
       constraint  FK_ACCOUT_LOG_TOACCOUT foreign key(toaccourt) referenceS   custome_account(accountno)
)


--1.1建立交易表视图
create or replace  view tra 
as
select * from log_msg;


--1.2 建立开户过程
create or replace procedure open_account(p_aname acc.aname%type,aid acc.idno%type,money acc.money%type, p_accountno out acc.ACCOUNTNO%type  )
is
   v_rand number(20);
   v_num int default 0;
   v_account acc.ACCOUNTNO%type;
   id_has_exist exception;  --该身份证已经开过户
   name_has_exist exception;  --该姓名已经注册
begin
     select count(*) into v_num from acc where aid= idno;
     if v_num > 0 then 
          raise id_has_exist;
     end if;
      select count(*) into v_num from acc where ANAME= p_aname ;
     if v_num > 0 then 
          raise name_has_exist;
     end if;
     select trunc(dbms_random.value*100000000,0) into v_rand  from dual;--产生一个8位的随机数
     v_account:=CONCAT('67226738',to_char(v_rand));                     --根据前缀产生一个账号
     p_accountno:=v_account;   --作为返回值返回                      
     insert into acc values(v_account,p_aname,aid,money,sysdate);
     dbms_output.put_line(v_account||' '||p_aname||' '||aid||' '||money||' '||sysdate);--检验输出
     commit;
     exception
       when id_has_exist then 
       raise_application_error('-20012','该身份证已经开户,请换其他身份证继续！！');
       when name_has_exist then 
       raise_application_error('-20002','该客户已经存在，不能重名！！');
       when others then
       rollback;
       raise_application_error('-20011','操作失败,已撤销！！');
end open_account;


 --1.3 测试(------------------------------------------开户过程----------------------------------------)
 declare 
      aname acc.aname%type;
      aid acc.idno%type;
      money acc.money%type;
      v_account acc.ACCOUNTNO%type;--记录返回账号
  begin 
      aname:='&开户姓名';
      aid:='&身份证号';
      money:=&余额;
      open_account(aname,aid,money,v_account);
      dbms_output.put_line('恭喜您开户成功！您的账户为：'||v_account);
  end ;  
  
 
select * from acc;

--2.1 转账功能工程transaction
create or replace procedure pro_tract(p_fromaccount tra.FROMACCOUNT%type,
                            p_toaccount tra.TOACCOURT%type,p_money tra.MONEY%type)
is
   v_counter int default 0;
   v_money tra.MONEY%type;
   no_found_from exception;
   no_found_to exception;
   no_enough exception;
   data_error exception;
   no_equals exception;
begin
     
     if p_money<0 then
        raise data_error;
     end if;
     select count(*) into v_counter from acc  where  ACCOUNTNO = p_fromaccount;
     if v_counter = 0 then
         raise no_found_from;
         end if ;
     select money into v_money from acc where ACCOUNTNO = p_fromaccount;
     if  (v_money- p_money)<0 then
          raise no_enough;
     end if ;
    

     select count(*) into v_counter from acc where  ACCOUNTNO = p_toaccount;
     if v_counter = 0 then
         raise no_found_to;
     end if ;
     if p_fromaccount = p_toaccount then
        raise no_equals;
     end if;

     update acc set money = money-p_money where acc.ACCOUNTNO = p_fromaccount;
     update acc set money = money+p_money where acc.ACCOUNTNO = p_toaccount;
   
      insert into tra(tradeno,fromaccount,toaccourt,money) values ( to_char('T10000-'||to_char(sq_tra_log.nextval)) ,p_fromaccount, p_toaccount,p_money);-----------------------------------------
      dbms_output.put_line('转账成功！');
      commit;
     exception
      when no_found_from then
      raise_application_error('-20013','转出账户不存在！');
      when no_found_to then
      raise_application_error('-20014','转入账户不存在！');
      when no_enough then
      raise_application_error('-20015','转出账户余额不足！');
       when data_error then
      raise_application_error('-20016','转账数额不能为负值！');
      when no_equals then 
       raise_application_error('-20030','转出账户与转入账户相同！');
      when  others then
      raise_application_error('-20017','操作失败,动作已撤销！');
      rollback;
end pro_tract; 


select * from acc;  
select * from tra;  
--2.2 测试(-------------------------------------------------转账过程测试----------------------------------)
declare
       v_fromaccount tra.FROMACCOUNT%type;
       v_toaccount tra.TOACCOURT%type;
       v_money tra.MONEY%type;
begin
       v_fromaccount :='&转账账号';
       v_toaccount :='&收账账号';
       v_money:=&转账金额;
       pro_tract(v_fromaccount,v_toaccount,v_money);
       commit;
      /* exception              -- 加了这个异常后，raise_application_error 不在显示
        when others then 
        dbms_output.put_line('转账失败！');*/
end;

--2.3创建序列
 create sequence  sq_tra_log 
 start with 999;
 
--3.1
/*	(3)根据转出账号和交易时间段 可查询转出账号的所有交易记录。最后还需要返回该时间段转出的总金额。
      a)向“交易日志表”插入数据。
      b)交易号的生成规则：'T10000-'||trans_seq.nextval --> T10000-1000
      */
create or replace procedure pro_search_trace(p_fromaccount tra.FROMACCOUNT%type,
                                             begintime date,endtime date,
                                             totalmoney out tra.MONEY%type)
is
      cursor cv_accot is
             select * from tra where FROMACCOUNT = p_fromaccount
             and TRADETIME between begintime and endtime ;
      v_counter int default 0;
      time_error exception;
      no_found_from exception;
begin
     if months_between(begintime ,endtime)>0 then
        raise time_error;
     end if;
     
     select count(*) into v_counter from tra  where  FROMACCOUNT = p_fromaccount;
     if v_counter = 0 then
         raise no_found_from;
     end if ;
      totalmoney:=0;
        dbms_output.put_line(p_fromaccount||' 在 '||to_char(begintime,
      'yyyy"年"mm"月"dd"日"')||' ~ '||to_char(endtime,'yyyy"年"mm"月"dd"日"')||' 时间段内的交易清单：');
      dbms_output.new_line;
      for tra_accot in cv_accot loop
           totalmoney:=totalmoney+tra_accot.MONEY; 
           dbms_output.put_line(tra_accot.tradeno||'   '||tra_accot.fromaccount||
           '   '||tra_accot.toaccourt||'   '||to_char(tra_accot.money,'L999,999,999.00')||'   '||to_char(tra_accot.tradetime,'yyyy"年"mm"月"dd"日"'));
      end loop;
         dbms_output.new_line;
      exception
      when time_error then 
           raise_application_error('-20040','起始时间不能晚于结束时间！');
      when no_found_from then
           raise_application_error('-20018','没有发现该账户的交易记录！');
      when others then
           raise_application_error('-20019','查询异常！');
end pro_search_trace ;

--3.2 测试(----------------------------------------------------交易记录查询测试------------------------------------------------------)
declare
      p_fromaccount tra.FROMACCOUNT%type;
      begintime date;
      endtime date;
      v_totalmony tra.MONEY%type ;
begin
      p_fromaccount:='&请输入查询账户';
      begintime:=to_date('&请输入开始时间','yyyy/mm/dd');
      endtime:=to_date('&请输入结束时间','yyyy/mm/dd');
      pro_search_trace(p_fromaccount,begintime ,endtime,v_totalmony );
      dbms_output.put_line('合计：');
      dbms_output.put_line(p_fromaccount||' 在 '||to_char(begintime,
      'yyyy"年"mm"月"dd"日"')||' ~ '||to_char(endtime,'yyyy"年"mm"月"dd"日"')||' 时间段内的交易额为: '||to_char(v_totalmony,'L999,999.00'));
end ;

select * from tra;
select * from acc;




/*-----------------------------------------------------------------函数 -----------------------------------------------*/

--65. 基于账户表，编写一个函数通过账号获得账户余额；如果输入的账号不存在则显示相应的异常信息。
create or replace function get_balance (accno acc.ACCOUNTNO%type)
   return number
 is 
    v_balance acc.MONEY%type;
 
 begin
      select money into v_balance from acc where ACCOUNTNO = accno;
      return v_balance ;
 end;
 
 ---
 select get_balance('6722673861353700') from dual;

--66. 检测一个值是否落在了正常的员工工资范围内
create or replace function check_sal (sal salgrade.losal%type)
   return varchar2
   is
   v_losal salgrade.losal%type;
    v_hisal salgrade.losal%type;
 begin
      select min(losal)into v_losal from salgrade;
      select max(hisal) into v_hisal from salgrade;
      if sal>v_losal and sal <  v_hisal then
           return '在正常工资范围！';
      else
           return '不在正常工资范围！';
      end if ;
 end;

 select check_sal(690) from dual;
 
select * from salgrade;
--67. 编写一个函数获得指定部门的平均工资。

create or replace function get_avg_sal(p_dname newdept.dname%type)
return number
is 
   v_avg new_scott.sal%type;
   v_counter int default 0;
   no_dept exception;
begin
     select count(dname) into v_counter  from newdept where p_dname=dname;
     if(v_counter=0) then 
         raise no_dept;
     end if;
     
     select avg(sal) into v_avg from new_scott where deptno =
       (select deptno from newdept where dname = p_dname);
     return v_avg;
     
     exception
     when no_dept then 
        raise_application_error('-20048','不存在该部门');
end;

--SQL/PL语句

declare
     v_dname newdept.dname%type;
     v_avg_sal new_scott.sal%type;
begin
     v_dname:='&请输入部门名称';
     v_avg_sal:= get_avg_sal(v_dname) ;
     dbms_output.put_line(v_avg_sal);
end;


select * from newdept;


--68. 综合应用，利用第67题的函数，编写一个过程将所有部门的平均显示出来。
create or replace procedure search_avg
is
       cursor cv_dept is select dname from newdept;
       v_avg new_scott.sal%type default 0;
begin
       for rec_dname in cv_dept loop
       v_avg := get_avg_sal(rec_dname.dname);
             dbms_output.put_line(rec_dname.dname||'部门的平均工资是：'||v_avg);
       end loop;
end;

--
begin
search_avg;
end;


-------------------------------------------------触发器----------------------------------trigger


/*--------------------------------------------------(了解) 触发器 --------------------------------------------------------
 1. 什么是触发器：触发器在数据库里以独立的对象存储，它与存储过程和函数不同的是，存储过程与函数需要用户显示调用才执行，
	而触发器是由一个事件来启动运行。即触发器是当某个事件发生时自动地隐式运行。并且，触发器不能接收参数。
	所以运行触发器就叫触发或点火。
 2. ORACLE事件指的是对数据库的表进行的INSERT、UPDATE及DELETE操作或对视图进行类似的操作。
 3. 触发器不接受参数。
 
 触发器组成: 
	1)触发事件：引起触发器被触发的事件。 例如：DML语句(INSERT, UPDATE, DELETE语句对表或视图执行数据处理操作)、
	  DDL语句（如CREATE、ALTER、DROP语句在数据库中创建、修改、删除模式对象）、数据库系统事件（如系统启动或退出、异常错误）、
	  用户事件（如登录或退出数据库）。
	
	2)触发时间：即该TRIGGER 是在触发事件发生之前（BEFORE）还是之后(AFTER)触发，也就是触发事件和该TRIGGER 的操作顺序。
	
	3)触发操作：即该TRIGGER 被触发之后的目的和意图，正是触发器本身要做的事情。 例如：PL/SQL 块。
	
	4)触发对象：包括表、视图、模式、数据库。只有在这些对象上发生了符合触发条件的触发事件，才会执行触发操作。
	
	5)触发条件：由WHEN子句指定一个逻辑表达式。只有当该表达式的值为TRUE时，遇到触发事件才会自动执行触发器，
	  使其执行触发操作。
	
	6)触发频率：说明触发器内定义的动作被执行的次数。即语句级(STATEMENT)触发器和行级(ROW)触发器。
		a)语句级(STATEMENT)触发器：是指当某触发事件发生时，该触发器只执行一次；
		b)行级(ROW)触发器：是指当某触发事件发生时，对受到该操作影响的每一行数据，触发器都单独执行一次。
 
 REFERENCING 参照名称：OLD,NEW都是代表当前操作的记录行
	1) 默认的相关名称分别为OLD和NEW，参照当前DML操作的新、旧列值
	2) 触发器的PL/SQL块中应用相关名称时，必须在它们之前加冒号(:)，但在WHEN子句(触发条件)中则不能加冒号。
 
语法：
create or replace trigger 触发器名
   触发时间(BEFORE|AFTER)  触发事件(INSERT OR UPDATE OR DELETE) [of 列名] on 触发对象(表等)
      触发频率 
         when (触发条件) 
declare
   --声明变量等
   
begin
   --执行部分
   
end 触发器名;
*/

--当更新或插入员工的奖金时，其奖金comm大于1000时，sal工资自动多加10元
create or replace trigger auto_raise_sal_trg
   before insert or update of comm on employee
      for each row --表示行级触发器
         when (NEW.comm>=1000)
declare
 
begin
   --触发器里预定义了三个操作条件
   -- inserting,updating,deleting触发动作条件，其值为boolean，只能在触发器主体中使用
   if inserting then
      --触发器的PL/SQL块中应用相关名称时，必须在它们之前加冒号(:)，但在WHEN子句(触发条件)中则不能加冒号。
      :NEW.sal := :NEW.sal + 10;
   end if;
   if updating then
      :NEW.sal := :OLD.sal + 10;  
   end if;
end auto_raise_sal_trg;

--建表 test_trg
drop table test_trg;
create table test_trg(
  tid varchar2(4)
)

create sequence test_trg_seq
start with 1;

--使用触发器为表在插入数据时生成一个id值：A序号(序号用序列生成)
create table test_id(
	tid varchar2(4)
)
create sequence test_id_sq
start with 1;

create or replace trigger auto_id
    	before insert  on test_id
    	for each row 
    	when (new.tid is null)
  declare
      v_id test_id.tid%type;
begin
      select 'A'||test_id_sq.nextval into v_id from dual;
      :new.tid := v_id;
end;

--
insert into test_id values('159');
  select * from test_id;

--删除触发器
drop trigger auto_raise_sal_trg;

--禁用或启用触发器
alter trigger auto_raise_sal_trg disable; --enable


insert into employee(empno,ename,job,sal,comm,deptno) 
  values(employee_seq.nextval,'小美','CLERK',4000.0,1000,10);
  
update employee set comm=1500 where ENAME='小美';




------------------------------------------------
select * from employee WHERE ENAME='小美' order by empno DESC; 
select * from department;
select * from salgrade;

```