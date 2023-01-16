### MySql数据库管理系统

```mysql
show databases; #查询所有数据库
create database db1; #创建数据库
create database if not exists db1; #创建数控并进行判断是否存在
drop database if exists db1; #删除数据库并进行判断是否存在
use database db1; #使用数据库
select database(); #当前选中的数据库
show tables; #获取数据库里所有的表
desc func #获取指定表的结构
##########################
#创建表
create table table1(
    字段名1 数据类型1,
    字段名2 数据类型2
);
drop table table1; #删除表
alter table student rename to stu; #修改表名
alter table stu add address varchar(50); #添加列
alter table stu modify address char(50); #修改列
alter table stu change address addr varchar(30); #修改列和数据类型
alter table stu drop addr; #删除列

INSERT INTO stu(id, name) VALUES(1,'张三'); #添加指定字段数据
UPDATE stu set sex = '女' where name = '张三'; #更新指定列
delete from stu where name = '张三'; #删除指定列

select * from stu where age BETWEEN 20 and 30;

select * from stu where age in (18,20,22);
		
select * from stu where english is NULL;

select name from stu where name like '马%';
	
select * from stu where name like '_花%';
	
select * from stu where name like '%德%';

select * from stu group by math desc, english asc;

select count(id) from stu;
	
select max(math) from stu;	
	
select min(math) from stu;
	
select sum(math) from stu;
	
select avg(math) from stu;
	
select min(english) from stu;
	
select sex, avg(math) from stu GROUP BY sex;

select sex, avg(math), count(*) from stu where math > 70 GROUP BY sex HAVING COUNT(*) > 2;

#构建表时添加外键
create table emp(
	id int PRIMARY KEY auto_increment,
	name varchar(20),
	age int,
	dep_id int,
    # fk_emp_dept 为外键名 dep_id 和 id 想约束
	CONSTRAINT fk_emp_dept FOREIGN KEY(dep_id) REFERENCES dept(id)
);
#删除外键
alter table emp drop FOREIGN key fk_emp_dept;
#添加外键
alter table emp add CONSTRAINT fk_emp_dept FOREIGN KEY(dep_id) REFERENCES dept(id);
# 多表查询 隐式内连接
select * from emp, dept where emp.dep_id = dept.id;
# 多表查询 查询指定列 隐式内连接（别名）
select t1.name, t1.age,t2.dep_name from emp t1, dept t2 where t1.dep_id = t2.id;
#多表查询 显式内连接
select * from emp INNER JOIN dept on emp.dep_id = dept.id;
#多表查询 左外连接
select * from emp left JOIN dept on emp.dep_id = dept.id;
#多表查询 右外连接
select * from emp right JOIN dept on emp.dep_id = dept.id;
# 子查询
select * from emp where age > (select age from emp where name = '张三');
select * from emp where dep_id in (select id from dept where dep_name = '销售部' or dep_name = '研发部');
```

