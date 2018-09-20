## **postgresql query for employee table.**

**1) Manager Name, Count of Employees whose start date is after 2013**
```
select m.name as "Manager name" ,count(e.emp_id) as "Count of Employee" from employee e,employee m where e.mgr_id=m.emp_id and extract(Year from e.joining_date) > '2013' group by m.name order by m.name ;

(or)

select mgr.name as "manager name",mgr.emp_id , count(emp.emp_id) as "count_emp"  from employee emp , employee mgr where emp.mgr_id=mgr.emp_id and emp.joining_date::DATE > date '01-01-2014' group by mgr.name ,mgr.emp_id order by mgr.name ;

```
**2) Manager Name, Min(emp salary) where manager start date is after 2013**
```
select mgr.name as "manager name",mgr.emp_id , min(emp.salary) as "min salary of emp"  from employee emp , employee mgr where emp.mgr_id=mgr.emp_id and mgr.joining_date::DATE > date '01-01-2014' group by mgr.name ,mgr.emp_id order by mgr.name ;

```
**3) Manager name who atleast has two employees started after 2013**
```
select  mgr.name as "manager name", count(emp.emp_id)from employee emp ,employee mgr where emp.mgr_id=mgr.emp_id and extract(Year from emp.joining_date) > '2013' group by mgr.name  having   count(emp.name)>=2 ;

```
**4) Dept Name, Count of Employees whose start date is after 2013**
```
select   d.name as "department name"  ,count(emp.name) from employee emp ,dept d where 
emp.dept_id=d.dept_id and extract(Year from emp.joining_date) > '2013' group by  d.dept_id;

(OTHER)

select   d.name as "department name"  ,count(emp.name) from employee emp ,dept d where 
emp.dept_id=d.dept_id and emp.joining_date::DATE > date '01-01-2014'group by  d.dept_id;

```
**5) Dept Name, Min(emp salary) where manager start date is after 2013**
```
select d.name as "department name" , min(emp.salary) as "minimum salary of emp"  from employee emp , employee mgr ,dept d where emp.mgr_id=mgr.emp_id and emp.dept_id=d.dept_id and mgr.joining_date::DATE > date '01-01-2014' group by d.name ; 

```
**6) Dept Name who atleast has two employees started after 2013**
```
select d.name as "department name" ,count(emp.emp_id) from dept d ,employee emp where d.dept_id=emp.dept_id and extract(YEAR from emp.joining_date) > 2013 group by d.name having count(emp.emp_id)>=2 order by d.name;

```
**7) Number of employees per year**
```
select  extract(YEAR from joining_date) ,count(emp_id) from employee emp  group  by  extract(YEAR from joining_date) order by extract(YEAR from joining_date) ;

```
**8) Number of employees per year, per department**
```
select  extract(YEAR from joining_date) , d.name ,count(emp.emp_id) from employee emp,dept d  where d.dept_id=emp.dept_id group  by  extract(YEAR from joining_date) ,d.name order by extract(YEAR from joining_date) ;

```
**9) List of Months (Say, Jan-2018) where number of employees joined is more than 2**
```
select concat(extract(year from joining_date),'-',extract(month from joining_date) )as "year-month" ,count(emp_id) as Count from employee group by extract(year from joining_date),extract(month from joining_date) having count(emp_id)>2;

```
**10) List of Managers who has employees from more than one department**
```
select e.name , count(d.dept_id) from employee e ,dept d where e.dept_id=d.dept_id group by e.name having count(d.dept_id);

```
**11) List of Departments who has atleast two managers**
```
select d.name as "department name " ,count(distinct mgr.name) from employee mgr ,employee emp, dept d  where emp.mgr_id =mgr.emp_id and mgr.dept_id=d.dept_id  group by d.name having count( distinct mgr.name)>=2;

```
**12) Employee Names who joined after 2013 and his manager has more than two reportees**
```
select a.name from employee a,employee b where b.emp_id=a.mgr_id and a.joining_date>'01-01-2013'and (select count(a.name) from employee a,employee b where b.emp_id = a.mgr_id )>2;

```
   
**NEW:**

**1) SQL to find the missing ids from dept**
```
SELECT GENERATE_SERIES(
                       (SELECT MIN(dept_id) FROM dept),
                       (SELECT MAX(dept_id) FROM dept)
                      ) AS "MISSING ID" 
                   EXCEPT(SELECT dept_id FROM dept);

```



**20-09-2018**


**2)Manager Name, Reportee who joined first (Reportee Name - doj), Reportee who draws less sal (Reportee Name - salary)**

```

SELECT dte.MANAGER ,dte.MIN_JOINING ,sal.MIN_SALARY
FROM 
	(SELECT DISTINCT  mgr.name AS MANAGER,
	FIRST_VALUE(CONCAT(emp.name,'-',emp.joining_date)) OVER 
	(PARTITION BY mgr.emp_id 
	ORDER BY  emp.joining_date)  
	AS MIN_JOINING
	FROM employee emp  , employee mgr  
	WHERE emp.mgr_id=mgr.emp_id ) dte
INNER JOIN
	(SELECT  DISTINCT mgr.name AS MANAGER,
	FIRST_VALUE(concat(emp.name,'-',emp.salary)) OVER 
	(PARTITION BY mgr.emp_id 
	ORDER BY  emp.salary)
	AS MIN_SALARY 
	FROM employee emp  , employee mgr  
	WHERE emp.mgr_id=mgr.emp_id ) sal

ON dte.MANAGER=sal.MANAGER;

```

**Find the list of employee records where salary data is missing**
```
SELECT concat(sd.START_DATE,'-',ed.END_DATE) Missing_Date FROM   

(SELECT START_DATE FROM GENERATE_SERIES(
                       (SELECT MIN(start_date) FROM salary_history),
                       (SELECT MAX(start_date) FROM salary_history)
                      ) AS START_DATE  
                   EXCEPT (SELECT start_date FROM salary_history)) sd

INNER JOIN

(SELECT END_DATE  FROM GENERATE_SERIES(
                       (SELECT MIN(end_date) FROM salary_history),
                       (SELECT MAX(end_date) FROM salary_history)
                      ) AS END_DATE 
                   EXCEPT (SELECT end_date FROM salary_history)) ed

on ed.END_DATE-sd.START_DATE > 1 ;
```








