# 实验1：SQL语句的执行计划分析与优化指导

## 201810414323 夏德成 数据库名字：xdc
---
## 实验目的:
+ 分析SQL执行计划，执行SQL语句的优化指导。
+ 理解分析SQL语句的执行计划的重要作用。

## 实验内容:
+ 对Oracle12c中的HR人力资源管理系统中的表进行查询与分析。
+ 首先运行和分析教材中的样例：本训练任务目的是查询两个部门('IT'和'Sales')的部门总人数和平均工资，以下两个查询的结果是一样的。但效率不相同。
+ 设计自己的查询语句，并作相应的分析，查询语句不能太简单。
## 实验步骤：
### 1.运行教材上的两个查询语句：
第一种方式：

    SELECT d.department_name,count(e.job_id)as "部门总人数",
    avg(e.salary)as "平均工资"
    from hr.departments d,hr.employees e
    where d.department_id = e.department_id
    and d.department_name in ('IT','Sales')
    GROUP BY d.department_name;

第二种方式：

    SELECT d.department_name,count(e.job_id)as "部门总人数",
    avg(e.salary)as "平均工资"
    FROM hr.departments d,hr.employees e
    WHERE d.department_id = e.department_id
    GROUP BY d.department_name
    HAVING d.department_name in ('IT','Sales');

第一种和第二种方式中，我认为更优的是第一种。

### 2.查看语句的优化建议

使用优化工具，查看的优化建议是添加索引。

优化建议：

![优化建议](优化建议.png)

添加索引：


![添加索引](索引.png)

### 3.编写查询语句并分析：
实验编写的SQL语句：

    SELECT d.department_name,count(e.job_id)as "部门总人数",
    avg(e.salary)as "平均工资"
    FROM hr.departments d INNER JOIN hr.employees e
    ON d.department_id = e.department_id
    and d.department_name in ('IT','Sales')
    GROUP BY d.department_name;

分析：
+ 隐性连接，在FROM过程中对所有表进行笛卡儿积，最终通过WHERE条件过滤
+ 显性连接，在每一次表连接时通过ON过滤，筛选后的结果集再和下一个表做笛卡儿积，以此循环
+ 采用显性连接，减少查询过程中产生的笛卡尔积的数量，已达到提高查询效率的目的。