# MySql 基本操作

### 语法规范
- 不区分大小写，但建议关键字大写，表名，列名小写
- 每条命令最好用分号结尾
- 每条命令根据需要，可以缩进或换行
- 注释

### 开启/关闭MySQL服务
- 开启服务： `mysql.server start`
- 关闭服务： `mysql.server stop`

### MySQL服务的登录和退出
- 登录：`mysql [-h(主机名) -P(端口号)] -u用户名 -p密码`  
- 退出：`exit` 或 `ctrl+C`

### 常见命令
- 查看当前所有的数据库：`show databases;`
- 打开指定的库：`use 库名;`
- 查看当前库：`select database();`
- 查看当前库的所有表：`show tables;`
- 查看其它库的所有表：`show tables from 库名;`
- 创建数据库：`create database 数据库名;`
- 创建表：  
  ```sql
  create table 表名(
      列名 列类型,
      列名 列类型,
      ...
  )
  ```
- 查看表结构：`desc 表名;`
- 查看版本：
  - 方式一：登录到mysql服务端 `select version();`
  - 方式二：不登录到mysql服务端 `mysql --version` 或 `mysql -V`

### 基础查询
```sql
/*
语法:
select 查询列表 from 表名;

特点：
1、查询列表可以是：表中的字段、常量值、表达式、函数
2、查询的结果是一个虚拟的表格
*/

USE employees;

#1.查询表中的单个字段
SELECT last_name FROM employees;

#2.查询表中的多个字段
SELECT last_name,salary,email FROM employees;

#3.查询表中的所有字段
SELECT * FROM employees;

#4.查询常量值
SELECT 100;
SELECT 'john';

#5.查询表达式
SELECT 100%98;

#6.查询函数
SELECT VERSION();

#7.起别名
/*
1.便于理解 
2.如果要查询的字段有重名的情况，使用别名可以区分开来
*/
#方式一
SELECT 100%98 AS 结果;
SELECT last_name AS 姓,first_name AS 名 
FROM employees;
#方式二
SELECT last_name 姓,first_name 名 
FROM employees;
#案例：查询salary，显示结果为out put(此处out为关键字)
SELECT max_salary AS "out put" 
FROM jobs;

#8.去重
#案例：查询员工表中涉及到的所有的部门编号
SELECT DISTINCT department_id 
FROM employees;

#9.+号的作用
/*
mysql中+号只有运算符的功能

SELECT 100+90; 			两个操作数都为数值型，则做加法运算
									其中一方为字符型，视图将字符型数值转换成数值型
SELECT '123'+90;		如果转换成功，则继续做加法运算
SELECT 'john'+90;		如果转换失败，则将字符型数值转换成0
SELECT NULL+10;			只要一方为null，则结果肯定为null
*/
#案例：查询员工名和姓连接成一个字段，并显示为 姓名
SELECT CONCAT('a','b','c') AS 结果;
SELECT CONCAT(last_name,first_name) AS 姓名 
FROM employees;


##练习
#1.显示出表employees的全部列，各个列之间用逗号连接，列头显示成OUT_PUT
#错误
SELECT CONCAT(`first_name`,',',`last_name`,',',`job_id`,',',commission_pct) AS out_put
FROM employees;
#正确
SELECT IFNULL(commission_pct,0) AS 奖金率,commission_pct 
FROM employees;
#----------------
SELECT CONCAT(`first_name`,',',`last_name`,',',`job_id`,',',IFNULL(commission_pct,0)) AS out_put
FROM employees;

#[补充]ifnull函数
/*
判断某字段或表达式是否为null，如果为null返回指定值，否则返回原本值
*/

#[补充]isnull函数
/*
判断某字段或表达式是否为null，如果为null返回1，否则返回0
*/
```

### 条件查询
```sql
/*
语法：
		SELECT 
				查询列表
		FROM
				表名
		WHERE
				筛选条件;


分类：
		1、按条件表达式筛选
		条件运算符： > < = != <> >= <=
		
		2.按逻辑表达式筛选
		逻辑运算符：&&   ||   !
							and  or   not 
							
		3.模糊查询
		like 
		BETWEEN AND
		IN
		IS NULL
		IS NOT NULL
*/
#1.按条件表达式筛选
#案例1: 查询工资>12000的员工信息
SELECT * 
FROM employees 
WHERE salary>12000;
#案例2: 查询部门编号不等于90号的员工名和部门编号
SELECT last_name,department_id 
FROM employees 
WHERE department_id!=90;
#----------------
SELECT last_name,department_id FROM employees WHERE department_id<>90;


#2.按逻辑表达式筛选
#案例1：查询工资在10000到20000之间的员工名、工资以及奖金
SELECT last_name,salary,commission_pct 
FROM employees 
WHERE salary>=10000 AND salary<=20000;
#----------------
SELECT last_name,salary,commission_pct 
FROM employees 
WHERE salary BETWEEN 10000 AND 20000;
#案例2：查询部门编号不是在90到110之间，或者工资高于15000的员工信息
SELECT * 
FROM employees 
WHERE NOT(department_id>=90 AND department_id<=110) OR salary>15000;


#3.模糊查询
# LIKE
/*
LIKE
特点：
1.一般和通配符搭配使用
	通配符：
			% 任意多个字符，包含0个字符
			_ 任意单个字符
*/
#案例1：查询员工名中包含字符a的员工信息
SELECT * 
FROM employees 
WHERE last_name LIKE '%a%';
#案例2：查询员工名中第三个字符为n，第五个字符为l的员工民和工资
SELECT last_name,salary 
FROM employees 
WHERE last_name LIKE "__n_l%";
#案例3：查询员工名中第二个字符为_的员工名
SELECT last_name 
FROM employees 
WHERE last_name LIKE "_\_%";
#----------------
SELECT last_name 
FROM employees 
WHERE last_name LIKE "_$_%" ESCAPE '$';

# BETWEEN AND
/*
1.包含临界值
2.两个临界值不能掉换顺序
*/
#案例1：查询员工编号在100到120之间的员工信息
SELECT * 
FROM employees 
WHERE employee_id BETWEEN 100 AND 120;

# IN
/*
1.使用IN提高语句简洁度
2.IN列表中的值类型必须一致或兼容
*/
#案例：查询员工的工种编号是IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号
SELECT last_name,employee_id 
FROM employees 
WHERE job_id IN("IT_PROG","AD_VP","AD_PRES");

# IS NULL
/*
=或<>不能用于判断null值
IS NULL 或 IS NOT NULL 可以判断null值
*/
# 案例1：查询没有奖金的员工名和奖金率
SELECT last_name, commission_pct 
FROM employees 
WHERE commission_pct IS NULL;
#----------------
SELECT last_name, commission_pct 
FROM employees 
WHERE ISNULL(commission_pct);
# 案例1：查询有奖金的员工名和奖金率
SELECT last_name, commission_pct 
FROM employees 
WHERE commission_pct IS NOT NULL;

#安全等于 <=>
# 案例1：查询没有奖金的员工名和奖金率
SELECT last_name, commission_pct 
FROM employees 
WHERE commission_pct <=> NULL;
# 案例2：查询工资为12000的员工信息
SELECT * 
FROM employees 
WHERE salary <=> 12000;

```

### 排序查询
```sql
/*
语法：
		SELECT 查询列表
		FROM 表
		[WHERE 筛选条件]
		ORDER BY 排序列表 [ASC|DESC]
特点：
		1.ASC代表的是升序，DESC代表的是降序
		如果不写，默认升序
		2.ORDER BY子句中可以支持单个字段、多个字段、表达式、函数、别名
		3.ORDER BY子句一般是放在查询语句的最后面，limit子句除外
*/
#案例1：查询员工信息，要求工资从高到低排序
SELECT * 
FROM employees 
ORDER BY salary DESC;

#案例2：查询部门编号>=90的员工信息，按入职时间的先后进行排序
SELECT * 
FROM employees 
WHERE department_id >= 90 
ORDER BY hiredate;

#案例3：按年薪的高低显示员工的信息 年薪[按表达式排序]
SELECT *,salary*12 * (1+IFNULL(commission_pct,0)) AS 年薪 
FROM employees 
ORDER BY salary*12 * (1+IFNULL(commission_pct,0)) DESC;

#案例4：按年薪的高低显示员工的信息 年薪[按别名排序]
SELECT *,salary*12 * (1+IFNULL(commission_pct,0)) AS 年薪 
FROM employees 
ORDER BY 年薪 DESC;

#案例5：按姓名的长度显示员工的姓名和工资[按函数排序]
SELECT LENGTH(last_name),last_name,salary 
FROM employees 
ORDER BY LENGTH(last_name) DESC;

#案例6：查询员工信息，要求先按工资升序，再按员工编号降序[按多个字段排序]
SELECT * 
FROM employees 
ORDER BY salary ASC,employee_id DESC;
```

### 单行函数
```sql
/*
	概念：类似于java的方法，将一组逻辑语句封装在方法体中，对外暴露方法名
	好处：1.隐藏了实现细节 2.提高代码的重用性
	调用：SELECT 函数名(实参列表) [FROM 表];
	
	分类:
			1.单行函数
			如 CONCAT, LENGTH, IFNULL等
			2.分组函数
			功能：做统计使用，又称为统计函数、聚合函数、组函数
*/

#1.字符函数
#LENGTH 获取参数值的字节个数
SELECT LENGTH("john");
SELECT LENGTH('张三丰hahaha');
SHOW VARIABLES LIKE '%char%';

#CONCAT 拼接字符串
SELECT CONCAT(last_name,'_',first_name) 姓名 
FROM employees;

#UPPER,LOWER
SELECT UPPER('John');
SELECT LOWER('John');
#示例：将姓变大写，名变小写，然后拼接
SELECT CONCAT(UPPER(last_name),'_',LOWER(first_name)) 姓名 
FROM employees;

#SUBSTR,SUBSTRING
#索引从1开始
#截取从指定索引处后面所有字符
SELECT SUBSTR('李莫愁爱上了陆展元',7) out_put;
#截取从指定索引处指定字符长度的字符
SELECT SUBSTR('李莫愁爱上了陆展元',1, 3) out_put;
#案例：姓名中首字符大写，其他字符小写，然后用_拼接
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2))) out_put 
FROM employees;

#INSTR
#返回字串第一次出现的索引，如果找不到返回0
SELECT INSTR('杨不悔爱上了殷六侠','殷六侠') AS out_put;

#TRIM
SELECT LENGTH(TRIM('     张翠山     ')) AS out_put;
SELECT TRIM('a' FROM 'aaaaa张aaa翠山aaaaaaaaa') AS out_put;

#LPAD
#用指定的字符实现左填充指定长度
SELECT LPAD('殷素素',10,'*') AS out_put;
SELECT LPAD('殷素素',2,'*') AS out_put;
#RPAD
#用指定的字符实现右填充指定长度
SELECT RPAD('殷素素',10,'ab') AS out_put;

#REPLACE替换
SELECT REPLACE('周芷若张无忌爱上了周芷若','周芷若','赵敏') AS out_put;


#2.数学函数
#ROUND 四舍五入
SELECT ROUND(-1.55);
SELECT ROUND(1.567, 2);

#CEIL 向上取整
SELECT CEIL(1.1);
SELECT CEIL(-1.1);

#FLOOR 向下取整
SELECT FLOOR(1.1);
SELECT FLOOR(-1.1);

#TRUNCATE 截断
SELECT TRUNCATE(1.6999999,1);

#MOD取余
/*
MOD(a,b) :  a-a/b*b
*/
SELECT MOD(10,3);
SELECT MOD(-10,-3);
SELECT 10%3;


#3.日期函数
#NOW 返回当前系统日期+时间
SELECT NOW();

#CURDATE 返回当前系统日期，不包含时间
SELECT CURDATE();

#CURTIME 返回当前时间，不包含日期
SELECT CURTIME();

#可以获取指定部分，年、月、日、小时、分钟、秒
SELECT YEAR(NOW()) 年;
SELECT YEAR('1998-1-1') 年;
SELECT YEAR(hiredate) 年 FROM employees;
SELECT MONTH(NOW()) 月;
SELECT MONTHNAME(NOW()) 月;

/*
	%Y ———— 四位的年份
	%y ———— 2位的年份
	%m ———— 月份(01,02...11,12)
	%c ———— 月份(1,2...11,12)
	%d ———— 日(01,02...11,12)
	%H ———— 小时(24小时)
	%h ———— 小时(12小时)
	%i ———— 分钟(00，01,...59)
	%s ———— 秒(00,01...59)
*/
# STR_TO_DATE 将字符通过指定的格式转换成日期
SELECT STR_TO_DATE('1995-5-8','%Y-%c-%d') AS out_put;
#查询入职日期为1992-4-3的员工信息
SELECT * FROM employees WHERE STR_TO_DATE(hiredate,'%Y-%c-%d') = '1992-4-3';
# DATE_FORMAT 将日期转换成字符
SELECT DATE_FORMAT(NOW(),'%y年%m月%d日') AS out_put;
#查询有奖金的员工名和入职日期(xx月/xx日 xx年)
SELECT last_name,DATE_FORMAT(hiredate,'%m月/%d日 %y年') 
FROM employees 
WHERE commission_pct IS NOT NULL;


#4.其他函数
SELECT VERSION();
SELECT DATABASE();
SELECT USER();


#5.流程控制函数
#IF函数： IF ELSE 的效果
SELECT IF(10<5,'大','小');
#----------------
SELECT last_name,commission_pct,IF(commission_pct is NULL,'没奖金，淦','有奖金') 备注 
FROM employees;

#CASE函数的使用一：switch case的效果
/*
语法：
CASE 要判断的字段或表达式
WHEN 常量1 THEN 要显示的值1或语句1;[如果是值就不需要分号]
WHEN 常量2 THEN 要显示的值2或语句2;
...
ELSE 要显示的值n或语句n;[ELSE若省略显示为null]
END
*/
#案例：查询员工的工资
/** 要求
部门号=30,显示的工资为1.1倍
部门号=40,显示的工资为1.2倍
部门号=50,显示的工资为1.3倍
其他部门，显示的工资为原工资
*/
SELECT salary 原始工资,department_id,CASE department_id
	WHEN 30 THEN salary * 1.1
	WHEN 40 THEN salary * 1.2
	WHEN 50 THEN salary * 1.3
	ELSE salary 
	END AS 新工资 
FROM employees;

#CASE函数的使用二：类似于多重if
/*
CASE
WHEN 条件1 THEN 要显示的值1或语句1;[如果是值就不需要分号]
WHEN 条件2 THEN 要显示的值2或语句2;
...
ELSE 要显示的值n或语句n;[ELSE若省略显示为null]
END
*/
#案例：查询员工的工资情况
/*
如果工资>20000,显示A级别
如果工资>15000,显示B级别
如果工资>10000,显示C级别
否则，显示D级别
*/
SELECT *,CASE 
	WHEN salary>20000 THEN 'A'
	WHEN salary>15000 THEN 'B'
	WHEN salary>10000 THEN 'C'
	ELSE 'D'	
	END AS 级别 
FROM employees;

```

### 分组函数
```sql
/*
	功能: 用作统计使用，又称为聚合函数或统计函数或组函数
	
	分类：
	SUM求和、AVG求平均值、MAX最大值、MIN最小值、COUNT计算个数
	
	特点：
	1、SUM、AVG用于处理数值型
		 MAX、MIN、COUNT可以处理任何类型
  2、以上分组函数都忽略null值
	3、可以和 DISTINCT搭配实现去重运算
	4、COUNT函数的单独介绍
	一般使用 COUNT(*)用作统计行数
	5、和分组函数一同查询的字段要求是 GROUP BY后的字段
	
*/
#1、简单的使用
SELECT SUM(salary) 总工资,ROUND(AVG(salary),2) 平均工资,MAX(salary) 最高工资,MIN(salary) 最低工资,COUNT(salary) 员工数 
FROM employees;

#2、参数支持哪些类型
SELECT SUM(last_name),AVG(last_name) FROM employees;
SELECT SUM(hiredate),AVG(hiredate) FROM employees;

SELECT MAX(last_name),MIN(last_name) FROM employees;
SELECT MAX(hiredate),MIN(hiredate) FROM employees;

SELECT COUNT(commission_pct) FROM employees;
SELECT COUNT(last_name) FROM employees;

#3、是否忽略null
SELECT SUM(commission_pct),AVG(commission_pct),SUM(commission_pct)/35,SUM(commission_pct)/107 
FROM employees;
#----------------
SELECT MAX(commission_pct), MIN(commission_pct) 
FROM employees;
#----------------
SELECT COUNT(commission_pct) 
FROM employees;
#----------------
SELECT COUNT(*) FROM employees;

#4、和 DISTINCT 搭配
SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;
SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;

#5、COUNT函数的详细介绍
SELECT COUNT(*) FROM employees;#用该方式效率最高
SELECT COUNT(1) FROM employees;

#6、和分组函数一同查询的字段有限制
SELECT AVG(salary),employee_id FROM employees;
 
```

### 分组查询
```sql
/*
	功能: 用作统计使用，又称为聚合函数或统计函数或组函数
	
	语法：
			SELECT 分组函数，列(要求出现在 GROUP BY 的后面)
			FROM 表
			[WHERE 筛选条件]
			GROUP BY 分组的列表
			ORDER BY 子句
	注意：
			查询列表比较特殊，要求是分组函数和 GROUP BY后出现的字段
	特点：
			1、分组查询中的筛选条件分为两类
									数据源						位置									关键字
			分组前筛选		原始表						GROUP BY子句的前面		WHERE
			分组后筛选		分组后的结果集			GROUP BY子句的后面		HAVING
			
			分组函数做条件肯定是放在 HAVING子句中
			i.分组函数做条件肯定是放在 HAVING子句中
			ii.能用分组前筛选的，就有限考虑使用分组前筛选
			
			
			2.GROUP BY子句支持单个字段分组，多个字段分组(多个字段之间用逗号隔开没有顺序要求)，表达式或函数(用得较少)
			3.也可以添加排序(排序放在整个分组查询的最后)
	
*/
#简单分组查询
#案例1:查询每个工种的最高工资
SELECT MAX(salary),job_id 
FROM employees 
GROUP BY job_id;
#案例2:查询每个位置上的部门个数
SELECT COUNT(*),location_id 
FROM departments 
GROUP BY location_id;


#添加分组前的筛选条件
#案例1:查询邮箱中包含a字符的，每个部门的平均工资
SELECT AVG(salary),department_id 
FROM employees 
WHERE email LIKE '%a%' 
GROUP BY department_id;
#案例2:查询有奖金的每个领导手下员工的最高工资
SELECT MAX(salary),manager_id 
FROM employees 
WHERE commission_pct IS NOT NULL 
GROUP BY manager_id;


#添加分组后的筛选条件
#案例1:查询哪个部门的员工个数>2
#步骤1，查询每个部门员工个数
SELECT COUNT(*),department_id 
FROM employees 
GROUP BY department_id;
#步骤2，根据步骤1的结果进行筛选，查询哪个部门的员工个数>2
SELECT COUNT(*),department_id 
FROM employees 
GROUP BY department_id 
HAVING COUNT(*) > 2;

#案例2:查询每个工种中，有奖金的员工且最高工资>12000的工种编号和最高工资
#步骤一，查询每个工种中，有奖金且工资最高的员工 的工种编号和最高工资
SELECT job_id,MAX(salary) 
FROM employees 
WHERE commission_pct IS NOT NULL 
GROUP BY job_id;
#步骤二，根据步骤一的结果筛选处最高工资>12000的 工种编号和最高工资
SELECT job_id,MAX(salary) 
FROM employees 
WHERE commission_pct IS NOT NULL 
GROUP BY job_id 
HAVING MAX(salary) > 12000;

#案例3:查询领导编号>102的每个领导手下最低工资>5000的员工领导编号，以及员工的最低工资
SELECT manager_id,MIN(salary) 
FROM employees 
WHERE manager_id>102 
GROUP BY manager_id 
HAVING MIN(salary)>5000;


#按表达式或函数分组
#案例：按员工姓名的长度分组，查询每一组的员工个数，筛选员工个数>5的有哪些
SELECT COUNT(*),LENGTH(last_name) 
FROM employees 
GROUP BY LENGTH(last_name) 
HAVING COUNT(*)>5;


#按多个字段分组
#案例：查询每个部门每个工种的员工平均工资
SELECT AVG(salary),department_id,job_id 
FROM employees 
GROUP BY department_id,job_id;


#添加排序
#案例：查询每个部门每个工种的员工的平均工资，并且按平均工资的高低显示
SELECT AVG(salary),department_id,job_id 
FROM employees 
GROUP BY department_id,job_id 
ORDER BY AVG(salary) DESC;
```

### 连接查询
```sql
/*
		含义：又称多表查询，当查询的字段来自于多个表时，就会用到连接查询
		
		分类：
				按年代分类：
						sql92标准：仅仅支持内连接
						sql99标准：支持内连接+外连接(左外和右外)+交叉连接
				
				按功能分类：
						内连接：
								等值连接
								非等值连接
								自连接
						外连接：
								左外连接
								右外连接
								全外连接(MySql不支持)
						交叉连接：
				
*/
SELECT name,boyName FROM boys,beauty;
SELECT `name`,boyName FROM boys,beauty WHERE beauty.boyfriend_id = boys.id;

#一、sql92标准
#1、等值连接
/*
	i.多表等值连接的结果为多表的交集部分
	ii.n表连接，至少需要n-1个连接条件
	iii.多表的顺序没有要求
	iv.一般需要为表起别名
	v.可以搭配前面介绍的所有子句使用，比如排序、分组、筛选
*/
#案例1:查询女神名和对应的男神名
SELECT `name`,boyName FROM boys,beauty WHERE beauty.boyfriend_id = boys.id;
#案例2:查询员工名和对应的部门名
SELECT last_name,department_name FROM employees,departments WHERE employees.department_id = departments.department_id;

#2、为表起别名
/*
注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定
*/
#查询员工名、工种号、工种名
SELECT last_name,e.job_id,job_title FROM employees e,jobs j WHERE e.job_id=j.job_id; 

#3、两个表的顺序可以交换

#4、可以加筛选？
#案例：查询有奖金的员工名、部门名
SELECT last_name,department_name 
FROM employees,departments 
WHERE commission_pct IS NOT NULL AND employees.department_id=departments.department_id;
#案例2:查询城市名中第二个字符为o的部门和城市名
SELECT department_name,city 
FROM departments d,locations l 
WHERE d.location_id=l.location_id AND l.city LIKE '_o%';

#5、可以加分组
#案例1:查询每个城市的部门个数
SELECT COUNT(*),l.city 
FROM departments d,locations l 
WHERE l.location_id=d.location_id 
GROUP BY l.location_id;
#案例2:查询有奖金的每个部门的 部门名，部门领导编号，该部门的最低工资
SELECT d.department_name 部门, d.manager_id 领导编号, MIN(e.salary) 最低工资
FROM employees e,departments d
WHERE e.commission_pct IS NOT NULL AND e.department_id=d.department_id
GROUP BY e.department_id;

#6、可以加排序
#案例：查询每个工种的工种名和员工的个数，并且按员工个数降序
SELECT j.job_title 工种名, COUNT(*) 员工个数
FROM jobs j,employees e
WHERE j.job_id=e.job_id
GROUP BY j.job_id
ORDER BY 员工个数 DESC;

#7、可以实现三表连接？
#案例：查询员工名、部门名和所在的城市
SELECT e.last_name 员工名,d.department_name 部门名,l.city 所在城市
FROM departments d,employees e,locations l
WHERE e.department_id=d.department_id AND d.location_id=l.location_id;


#2、非等值连接
#案例1:查询员工的工资和工资级别
SELECT e.salary, g.grade_level
FROM employees e, job_grades g
WHERE e.salary BETWEEN g.lowest_sal AND g.highest_sal;


#3、自连接
#案例：查询员工名和上级的名称
SELECT e.last_name 员工名, e2.last_name 上级
FROM employees e, employees e2
WHERE e.manager_id = e2.employee_id;



#一、sql99标准
/*
	语法：
			SELECT 查询列表
			FROM 表1 别名 [连接类型]
			JOIN 表2 别名
			ON 连接条件
			[WHERE 筛选条件]
			[GROUP BY分组]
			[HAVING 筛选条件]
			[ORDER BY排序列表]
			
	分类：
			内连接：INNER
			外连接：
					左外：LEFT [OUTER]
					右外：RIGHT [OUTER]
					全外：FULL [OUTER]
			交叉连接：CROSS
*/
#一 内连接
/*
语法：
SELECT 查询列表
FROM 表1 别名
INNER JOIN 表2 别名
ON 连接条件;

分类：
		等值
		非等值
		自连接

特点：
		1.添加排序、分组、筛选
		2.inner可以省略
		3.筛选条件放在 WHERE后面，连接条件放在 ON后面，提高分离性，便于阅读
		4.INNER JOIN连接和sql92语法中的等值连接效果是一样的，都是查询交集
*/

#1、等值连接
#案例1.查询员工名、部门名
SELECT e.last_name,d.department_name
FROM employees e
INNER JOIN departments d
ON e.department_id=d.department_id;
#案例2.查询名字中包含e的员工名和工种名
SELECT e.last_name,j.job_title
FROM employees e
INNER JOIN jobs j
ON e.job_id=j.job_id
WHERE e.last_name LIKE '%e%';
#案例3.查询部门个数>3的城市 的城市名名和部门个数
SELECT l.city,COUNT(*) 部门个数
FROM departments d
INNER JOIN locations l
ON d.location_id=l.location_id
GROUP BY l.location_id
HAVING 部门个数 > 3;
#案例4.查询哪个部门的部门员工个数>3的部门名和员工个数，并按个数降序排序
SELECT department_name,COUNT(*) 员工个数
FROM employees e
INNER JOIN departments d
ON e.department_id=d.department_id
GROUP BY e.department_id
HAVING 员工个数>3
ORDER BY 员工个数 DESC;
#案例5.查询员工名、部门名、工种名，并按部门名降序
SELECT e.last_name,d.department_name,j.job_title
FROM employees e
INNER JOIN departments d
ON e.department_id=d.department_id
INNER JOIN jobs j
ON e.job_id=j.job_id
ORDER BY d.department_name DESC;


#2、非等值连接
#查询员工的工资级别
SELECT last_name,grade_level
FROM employees e
INNER JOIN job_grades g
ON e.salary BETWEEN g.lowest_sal AND g.highest_sal;
#查询工资级别的员工个数>20，并且按工资级别降序
SELECT grade_level,COUNT(*)
FROM employees e
INNER JOIN job_grades g
ON e.salary BETWEEN g.lowest_sal AND g.highest_sal
GROUP BY grade_level
HAVING COUNT(*) > 20
ORDER BY grade_level DESC;


#3、自连接
#查询员工的名字、上级的名字
SELECT e.last_name,e2.last_name
FROM employees e
INNER JOIN employees e2
ON e.manager_id=e2.employee_id;
#查询姓名中包含字符k员工的名字、上级的名字
SELECT e.last_name,e2.last_name
FROM employees e
INNER JOIN employees e2
ON e.manager_id=e2.employee_id
WHERE e.last_name LIKE '%k%';



#二 外连接
/*
应用场景：用于查询一个表中有，另一个表没有的记录

特点：
1、	外连接的查询结果为主表中的所有记录
				如果从表中没有匹配的，显示null
2、	左外连接，LEFT JOIN左边的是主表
		右外连接，RIGHT JOIN右边的是主表
3、	左外和右外交换两个表的顺序，可以实现同样的效果
4、	全外连接=两个表做并集(内连接结果+表1有而表2没有+表2有而表1没有的)
*/
#引入：查询没有男朋友的女神名
#左外连接
SELECT w.name,m.boyName
FROM beauty w
LEFT JOIN boys m
ON w.boyfriend_id=m.id
WHERE m.id IS NOT NULL;
#右外连接
SELECT w.name,m.boyName
FROM boys m
RIGHT JOIN beauty w
ON w.boyfriend_id=m.id
WHERE m.id IS NOT NULL;

#案例1:查询那个部门没有员工
SELECT DISTINCT d.department_name
FROM departments d
LEFT JOIN employees e
ON d.department_id=e.department_id
WHERE e.employee_id IS NULL;

#全外连接(MySql不支持)
-- SELECT w.*,m.*
-- FROM beauty w
-- FULL OUTER JOIN boys m
-- ON w.boyfriend_id=m.id;

#三 交叉连接
#笛卡尔积，两个表相乘
SELECT w.*,m.*
FROM beauty w
CROSS JOIN boys m;
```

#子查询
```sql
/*
含义：
		出现在其他与居中的 SELECT语句，称为子查询或内查询
		外部的查询语句，称为主查询或外查询
		
分类：
		按子查询出现的位置：
				SELECT后面：
						仅仅支持标量子查询
						
				FROM后面：
						支持表子查询
				WHERE或HAVING后面：★
						标量子查询★
						列子查询★
						行子查询
				EXISTS后面(相关子查询)
						表子查询
		按结果集的行列数不同：
				标量子查询(结果集只有一行一列)
				列子查询(结果集只有一列多行)
				行子查询(结果集只有多列一行)
				表子查询(结果集为多行多列)
*/
#一、WHERE或 HAVING后面
/*
特点：
1.子查询放在小括号内
2.子查询一般放在条件的右侧
3.标量子查询，一般搭配着单行操作符使用
> < >= <= = <>
4.子查询要优先于主查询执行
 
列子查询，一般搭配多行操作符使用
IN、ANY/SOME、ALL
*/
#1.标量子查询
#案例1:谁的工资比Abel高
#步骤1、查询Abel的工资
SELECT salary
FROM employees e
WHERE e.last_name='Abel';
#步骤2、查询员工信息，满足salary>步骤1结果
SELECT *
FROM employees
WHERE salary>(
	SELECT salary
	FROM employees e
	WHERE e.last_name='Abel'
);

#案例2:返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id和工资
#步骤1、先查找141号员工的job_id
SELECT job_id
FROM employees
WHERE employee_id=141;
#步骤2、再找出143号员工的salary
SELECT salary
FROM employees
WHERE employee_id=143;
#步骤3、查询员工的姓名，job_id和工资，要求job_id=步骤1并且salary>步骤2
SELECT last_name,job_id,salary
FROM employees
WHERE job_id=(
	SELECT job_id
	FROM employees
	WHERE employee_id=141
) AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id=143
);

#案例3：返回公司工资最少的员工的last_name,job_id和salary
#步骤1、查询公司的最低工资
SELECT MIN(salary)
FROM employees;
#步骤2、查询last_name,job_id和salary,要求salary=步骤1
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);

#案例4: 查询最低工资大于50号部门最低工资的部门id和其最低工资
#步骤1、查询50号部门的最低工资
SELECT MIN(salary)
FROM employees
WHERE department_id=50;
#步骤2、查询每个部门的最低工资
SELECT department_id,MIN(salary) 最低工资
FROM employees
GROUP BY department_id;
#步骤3、在步骤2基础上筛选出最低工资>步骤1
SELECT department_id,MIN(salary) 最低工资
FROM employees
GROUP BY department_id
HAVING 最低工资>(
	SELECT MIN(salary)
	FROM employees
	WHERE department_id=50
);


#2.列子查询(多行子查询)
#案例1：返回location_id是1400或1700的部门中的所有员工姓名
#步骤1、查询location_id是1400或1700的部门编号
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700);
#步骤2、查询员工名，要求部门号是步骤1中的一个
SELECT last_name
FROM employees
WHERE department_id IN (
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)
);

#案例2:返回其他工种中比job_id为‘IT_PROG’任意工资员工低 的 员工的：工号、姓名、job_id 以及salary
#步骤1、查询job_id为IT_PROG所有员工的工资
SELECT DISTINCT salary
FROM employees
WHERE job_id='IT_PROG';
#步骤2、查询工号、姓名、job_id 以及salary中  工种不为IT_PROGsalary<步骤1中任意一个
SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE job_id<>'IT_PROG' AND salary<ANY(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id='IT_PROG'
);
#或
SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE job_id<>'IT_PROG' AND salary<(
	SELECT MAX(salary)
	FROM employees
	WHERE job_id='IT_PROG'
);

#案例3：返回其他工种中比job_id为‘IT_PROG’所有工资员工低 的 员工的：工号、姓名、job_id 以及salary
SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE job_id<>'IT_PROG' AND salary<ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id='IT_PROG'
);
#或
SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE job_id<>'IT_PROG' AND salary<(
	SELECT MIN(salary)
	FROM employees
	WHERE job_id='IT_PROG'
);


#3、行子查询(结果集一行多列或多行多列)
#案例：查询员工编号最小并且工资最高的员工信息
SELECT *
FROM employees
WHERE (employee_id,salary)=(
	SELECT MIN(employee_id),MAX(salary)
	FROM employees
);



#二、SELECT后面
#案例：查询departments表信息，额外增加一列员工个数
SELECT *,(
	SELECT COUNT(*)
	FROM employees e
	WHERE e.department_id=d.department_id
) 部门人数
FROM departments d;

#案例2：查询员工号=102员工所在的部门名
SELECT (
	SELECT department_name
	FROM departments
	WHERE department_id=(
		SELECT department_id
		FROM employees
		WHERE employee_id=102
	)
) 部门名;



#三、FROM后面
#案例：查询每个部门的平均工资的工资等级
#步骤1、查询每个部门的平均工资
SELECT AVG(salary) `avg`,department_id
FROM employees
GROUP BY department_id;
#步骤2、将步骤1的表和job_grades表连接，筛选出平均工资在lowest_sal和highest_sal的
SELECT `avg`,grade_level,department_id
FROM (
	SELECT AVG(salary) `avg`,department_id
	FROM employees
	GROUP BY department_id
) table1
JOIN job_grades
ON table1.`avg` BETWEEN lowest_sal AND highest_sal;



#四、EXISTS后面(相关子查询)
/*
语法：
EXISTS(完整的查询语句);
结果：
1或0
*/
#案例1:查询有员工的部门名
#IN
SELECT d.department_name
FROM departments d
WHERE d.department_id IN(
	SELECT department_id
	FROM employees
);
#EXISTS 
SELECT d.department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE e.department_id=d.department_id
);

#案例2:查询没有女朋友的男神信息
#IN
SELECT m.*
FROM boys m
WHERE m.id NOT IN (
	SELECT boyfriend_id
	FROM beauty
);
#EXISTS
SELECT m.*
FROM boys m
WHERE NOT EXISTS(
	SELECT *
	FROM beauty
	WHERE boyfriend_id=m.id
);

```

### 分页查询
```sql
/*
应用场景：当要显示的数据，一页显示不全，需要分页提交sql请求
语法：
		SELECT 查询列表
		FROM 表
		[
		(join type) JOIN 表2
		ON 连接条件
		WHERE 筛选条件
		GROUP BY 分组字段
		HAVING 分组后的筛选
		ORDER BY排序的字段
		]
		LIMIT [offset,]size;
		
		offset要显示条目的起始索引(起始索引从0开始,当offset为0时可省略)
		size要显示的条目个数
特点：
		1.limit语句放在查询语句的最后
*/
#案例1:查询前五条员工信息
SELECT * FROM employees LIMIT 0,5;

#案例2:查询第11条——第25条
SELECT * FROM employees LIMIT 10,15;

#案例3:有奖金的员工信息，并且工资较高的前10名显示出来
SELECT *
FROM employees
WHERE commission_pct IS NOT NULL
ORDER BY salary DESC
LIMIT 10;
```

### 联合查询
```sql
/*
UNION 联合 合并：将多条查询语句的结果合并成一个结果

语法：
查询语句1
UNION
查询语句2
UNION
...

应用场景：
要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致时

特点：
1、要求多条查询语句的查询列数是一致的！
2、要求多条查询语句的查询的每一列的类型和顺序最好一致
3、UNION关键字默认去重，如果使用UNION ALL可以包含重复项
*/
#引入案例:查询部门编号>90或邮箱包含a的员工信息
SELECT *
FROM employees
WHERE department_id>90 OR email LIKE '%a%';
#-------------
SELECT *
FROM employees
WHERE department_id>90
UNION
SELECT *
FROM employees
WHERE email LIKE '%a%';
```

### DML语言
```sql
/*
数据操作语言：
插入：INSERT
修改：UPDATE
删除：DELETE

*/
#一、插入语句
#方式一
/*
语法：
INSERT INTO 表名(列名1,列名2,...) VALUES(值1,值2,...);
*/
#1.插入的值的类型要与列的类型一致或兼容
INSERT INTO beauty(id,`name`,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'tangyixing','女','1990-4-23','189888888',NULL,2);

#2.不可为NULL的列必须插入值，可以为NULL的列如何插入值？
#方式一
INSERT INTO beauty(id,`name`,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'tangyixing','女','1990-4-23','189888888',NULL,2);
#方式二
INSERT INTO beauty(id,`name`,sex,phone)
VALUES(15,'nazha','女','139888888');

#3.列的顺序是否可以掉换
INSERT INTO beauty(`name`,sex,id,phone)
VALUES('jiangxing','女',16,'110');

#4.列数和值的个数必须一致

#5.可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致
INSERT INTO beauty
VALUES(17,'feifei','男','1990-4-23','129888888',NULL,2);


#方式二
/*
语法：
INSERT INTO 表名
SET 列名=值,列名=值,...
*/
INSERT INTO beauty
SET id=19,`name`='刘涛',phone='999';


#两种方式比较
#1、方式一支持插入多行，方式二不支持
INSERT INTO beauty
VALUES (23,'tangyixing','女','1990-4-23','189888888',NULL,2),
(24,'tangyixing','女','1990-4-23','189888888',NULL,2),
(25,'tangyixing','女','1990-4-23','189888888',NULL,2)

#2、方式一支持子查询，方式二不支持
INSERT INTO beauty(id,`name`,phone)
SELECT 26,'songqian','10010';
#--------------------
INSERT INTO beauty(id,`name`,phone)
SELECT id,boyName,'11809866'
FROM boys
WHERE id<3;



#二、修改语句
/*
1、修改单笔的记录

语法：
UPDATE 表名
SET 列=新值,列=新值...
WHERE 筛选条件;


2、修改多表的记录

语法：
sql92语法：
UPDATE 表1 别名,表2 别名
SET 列=值,...
WHERE 连接条件
AND 筛选条件;

sql99语法：
UPDATE 表1 别名
INNER|LEFT|RIGHT JOIN 表2 别名
ON 连接条件
SET 列=值,...
WHERE 筛选条件;

*/
#1、修改单表的记录
#案例1:修改beauty表中姓名含tang的电话为13899997777
UPDATE beauty 
SET phone='13899997777'
WHERE `name` LIKE 'tang%';

#案例2:修改boys表中id号为2的名称为张飞，魅力值10
UPDATE boys
SET userCP=10,boyName='张飞'
WHERE id=2;


#2、修改多表的记录
#案例1:修改张无忌的女朋友的手机号为114
UPDATE beauty wm
INNER JOIN boys b
ON wm.boyfriend_id=b.id
SET wm.phone='114'
WHERE b.boyName='张无忌'

#案例2:修改没有男朋友的女神的男朋友编号都为2
UPDATE beauty wm
INNER JOIN boys b
ON wm.boyfriend_id=b.id
SET wm.boyfriend_id=2
WHERE wm.boyfriend_id IS NULL



#三、删除语句
/*

方式一：DELETE
语法：

1、单表的删除
DELETE FROM 表名 [WHERE 筛选条件] [limit 条目数]
2、多表的删除
sql92:
DELETE 表1的别名，表2的别名
FROM 表1 别名,表2 别名
WHERE 连接条件 
AND 筛选条件;

sql99：
DELETE 表1的别名,表2的别名
FROM 表1 别名
INNER|LEFT|RIGHT JOIN 表2 别名 ON 连接条件
WHERE 筛选条件;

方式二：TRUNCATE
语法： TRUNCATE TABLE 表名;
*/
#方式一：DELETE
#1.单表的删除
#案例1:删除手机号以9结尾的女神信息
DELETE
FROM beauty
WHERE phone LIKE '%9';

#2.多表的删除
#案例：删除张无忌的女朋友的信息
DELETE wm
FROM beauty wm
LEFT JOIN boys m
ON wm.boyfriend_id=m.id
WHERE m.boyName='张无忌';

#案例：删除黄晓明的信息以及他女朋友的信息
DELETE wm,m
FROM beauty wm
LEFT JOIN boys m
ON wm.boyfriend_id=m.id
WHERE m.boyName='黄晓明';

#DELETE和TRUNCATE
/*
1.DELETE可以加 WHERE条件，TRUNCATE不能加
2.TRUNCATE删除，效率高一丢丢
3.假如要删除的表中有自增长列，
如果用 DELETE删除后，再插入数据，自增长列的值从断点开始，
而 TRUNCATE删除后，再插入数据，自增长列的值从1开始。
4.TRUNCATE删除没有返回值，而 DELETE删除有返回值
5.TRUNCATE删除不能回滚，DELETE删除可以回滚
*/

SELECT * FROM beauty;
SELECT * FROM boys;
```

### DDL
```sql
/*
数据定义语言

库和表的管理

一、库的管理
创建、修改、删除
二、
创建、修改、删除

创建：CREATE
修改：ALTER
删除：DROP

*/
#一、库的创建
#1、库的创建
/*
语法：
CREATE DATABASE [IF NOT EXISTS]库名;
*/
#案例：创建库Books
CREATE DATABASE IF NOT EXISTS Books;

#2、库的修改(只允许改字符集)
ALTER DATABASE Books CHARACTER SET gbk;

#3、库的删除
DROP DATABASE IF EXISTS Books;


#二、表的管理
#1.表的创建
/*
语法：
CREATE TABLE 表名(
		列名 列的类型[(长度) 约束],
		列名 列的类型[(长度) 约束],
		列名 列的类型[(长度) 约束],
		...
		列名 列的类型[(长度) 约束]
)
*/
#案例：创建表book
CREATE TABLE book(
		id INT,#编号
		`name` VARCHAR(20),#书名
		price DOUBLE,#价格
		authorId INT,#作者编号
		publichDate DATETIME#初版日期
);

#案例：创建表author
CREATE TABLE IF NOT EXISTS author(
		id INT,
		`name` VARCHAR(20),
		nation VARCHAR(20) 
)


#2.表的修改
/*
语法：
ALTER TABLE 表名 ADD|DROP|MODIFY|CHANGE COLUMN 列名 [列类型 约束];
*/
#i.修改列名
ALTER TABLE book CHANGE COLUMN publichDate pubDate DATETIME;

#ii.修改列的类型或约束
ALTER TABLE book MODIFY COLUMN pubDate TIMESTAMP;

#iii.添加新列
ALTER TABLE book ADD COLUMN annual DOUBLE;

#iv.删除列
ALTER TABLE book DROP COLUMN annual;

#v.修改表名
ALTER TABLE author RENAME TO book_author;


#3.表的删除
DROP TABLE IF EXISTS book_author;
SHOW TABLES;


#4.表的复制
INSERT INTO author VALUES
(1,'村上春树','日本'),
(2,'莫言','中国'),
(3,'冯唐','中国'),
(4,'金庸','中国');
#i.仅仅复制表的结构
CREATE TABLE copy LIKE author;
#ii.复制表的结构+数据
CREATE TABLE copy2
SELECT * FROM author;
#iii.复制部分数据
CREATE TABLE copy3
SELECT id,`name` 
FROM author
WHERE nation='中国';
#iv.仅仅复制部分字段
CREATE TABLE copy4
SELECT id,`name`
FROM author
WHERE 0;
```

### 常见的数据类型
```sql
/*
数值型：
		整型
		小数：
				定点数
				浮点数
字符型：
		较短的文本：CHAR、VARCHAR
		较长的文本：TEXT、BLOB(较长的二进制数据)
		
日期型：

*/
#一、整型
/*
分类：
TINYINT,SMALLINT,MEDIUMINT,INT/INTEGER,BIGINT
1		2		 3			4			8            字节数

特点：
1.默认是有符号，如果想设置无符号，需要添加 UNSIGNED
2.如果不设置长度会有默认长度
长度代表了显示的最大宽度，
搭配 ZEROFILL左边会填充0，如果长度不够的话
*/
#1.如何设置无符号和有符号
DROP TABLE IF EXISTS tab_int;
CREATE TABLE tab_int(
		t1 INT ZEROFILL,
		t2 INT UNSIGNED
);
DESC tab_int
INSERT INTO tab_int VALUES(2147483648,4294967296)
INSERT INTO tab_int VALUES(123,132)
SELECT * FROM tab_int


#二、小数
/*
分类：
1.浮点型
FLOAT(M,D)
DOUBLE(M,D)
2.定点型
DEC(M,D)
DECIMAL(M,D)

特点：
i.
M：整数部位+小数部位
D：小数部位

ii.
M和D都可以省略
如果是 DECIMAL，则M默认为10，D默认为0
如果是 FLOAT和 DOUBLE，则会根据插入的数值的精度来决定精度

iii.
定点型的精度较高，如果要求插入数值的精度较高如货币运算等则考虑使用

*/
#测试M和D
CREATE TABLE tab_float(
		f1 FLOAT(5,2),
		f2 DOUBLE(5,2),
		f3 DECIMAL(5,2)
);
INSERT INTO tab_float VALUES(123.45,123.45,123.45)
INSERT INTO tab_float VALUES(123.456,123.456,123.456)
INSERT INTO tab_float VALUES(123.4,123.4,123.4)
INSERT INTO tab_float VALUES(1523.4,1523.4,1523.4)

DROP TABLE IF EXISTS tab_float;
CREATE TABLE tab_float(
		f1 FLOAT,
		f2 DOUBLE,
		f3 DECIMAL
);
INSERT INTO tab_float VALUES(123.4523,123.4523,123.4523)

DESC tab_float
SELECT * FROM tab_float


#原则：
/*
所选择的类型越简单越好，能保存数值的类型越小越好
*/


#三、字符型
/*
较短的文本：
CHAR
VARCHAR

其他：
BINARY和 VARBINARY用于保存较短的二进制
ENUM用于保存枚举
SET用于保存集合

较长的文本：
text
BLOB(较大的二进制)


特点：

				写法						M的意思								特点				效率
CHAR			CHAR(M)				最大的字符数，可以省略，默认为1				 固定长度的字符			 高
VARCHAR			VARCHAR(M)			最大的字符数，不可以省略					可变长度的字符			低
*/
CREATE TABLE tab_char(
		c1 ENUM('a','b','c')
);
INSERT INTO tab_char VALUES('a');
INSERT INTO tab_char VALUES('b');
INSERT INTO tab_char VALUES('c');
INSERT INTO tab_char VALUES('A');
INSERT INTO tab_char VALUES('D');
SELECT * FROM tab_char;

CREATE TABLE tab_set(
		s1 SET('a','b','c','d')
);
INSERT INTO tab_set VALUES('a');
INSERT INTO tab_set VALUES('a,b');
INSERT INTO tab_set VALUES('A,B');
INSERT INTO tab_set VALUES('a,b,d');
SELECT * FROM tab_set;



#四、日期型
/*
分类：
DATE只保存日期
TIME只保存时间
YEAR只保存年

DATETIME保存日期+时间
TIMESTAMP保存日期+时间


特点：

					字节				范围			时区等影响
DATETIME			8				1000——9999			不受
TIMESTAMP			4				1970——2038			 受
*/
CREATE TABLE tab_data(
		t1 DATETIME,
		t2 TIMESTAMP
);
INSERT INTO tab_data VALUES(NOW(),NOW());
SELECT * FROM tab_data;

SHOW VARIABLES LIKE 'time_zone';
SET time_zone='+9:00';
SELECT * FROM tab_data;
SET time_zone='SYSTEM';
SELECT * FROM tab_data;
```

### 常见约束
```sql
/*
含义：一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性

分类：六大约束
			NOT NULL：非空，用于保证改字段的值不能为空，如姓名、学号等
			DEFAULT：默认，用于保证改字段有默认值，如性别
			PRIMARY KEY：逐渐，用于保证该字段的值具有唯一性，并且非空，如学号、员工编号等
			UNIQUE：唯一，用于保证该字段的值具有唯一型，可以为空，比如座位号
			CHECK:检查约束[mysql不支持],如年龄、性别
			FOREIGN KEY：外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值
									在从表添加外键约束，用于引用主表中某列的值
									如：学生表的专业编号，员工表的部门编号，员工表的工种编号

添加约束的实际：
			1.创建表时
			2.修改表时
			
约束的添加分类：
			列级约束：
					六大约束语法上都支持，但外键约束没有效果
			
			表级约束：
					除了非空、默认，其他的都支持
					
主键和唯一对比：
					保证唯一性					是否允许为空		一个表中可以有多个			是否允许组合
		主键			✓						  ✕					最多一个				  ✓,但不推荐
		唯一		✕(允许多个空值)					✓				  可以多个					✓,但不推荐

外键：
		1、要求在从表设置外键关系
		2、从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求
		3、主表的关联列必须是一个key(一般是主键或唯一)
		4、插入数据时，先插入主表，再插入从表
		删除数据时，先删除从表，再删除主表
		

列级约束和表级约束比较：

				位置					支持的约束类型				  是否可以起约束名
列级约束：	  	列的后面				语法都支持，但外键没有效果			不可以
表级约束：		所有列的下面			 默认和非空不支持，其他支持			 可以(主键没有效果)
			
*/
CREATE DATABASE IF NOT EXISTS students;
USE students;
#一、创建表时添加约束
#1.添加列级约束
/*
语法：
直接在字段名和类型后面追加约束类型即可。
只支持：默认、非空、主键、唯一
*/
USE students;
CREATE TABLE IF NOT EXISTS major(
		id INT PRIMARY KEY,
		majorName VARCHAR(20)
);
CREATE TABLE stuinfo(
		id INT PRIMARY KEY,#主键
		`name` VARCHAR(20) NOT NULL,#非空
		gender CHAR(1) CHECK(gender='男' OR gender='女'),#检查
		seat INT UNIQUE,#唯一
		age INT DEFAULT 18,#默认约束
		majorId INT REFERENCES major(id)#外键
);
DESC stuinfo;
#查看stuinfo表中所有的索引，包括主键、外键、唯一
SHOW INDEX FROM stuinfo;


#2.添加表级约束
/*
语法：在各个字段的最下面
[CONSTRAINT 约束名] 约束类型(字段名)  ------去掉默认字段名
*/
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
		id INT,
		`name` VARCHAR(20),
		gender CHAR(1),
		seat INT,
		age INT,
		majorid INT,
		
		CONSTRAINT pk PRIMARY KEY(id),#主键
		CONSTRAINT uq UNIQUE(seat),#唯一键
		CONSTRAINT ck CHECK(gender='男' OR gender='女'),#检查
		CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)#外键
)
SHOW INDEX FROM stuinfo;

#通用的写法：
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE IF NOT EXISTS stuinfo(
		id INT PRIMARY KEY,
		`name` VARCHAR(20) NOT NULL,
		gender CHAR,
		age INT DEFAULT 18,
		seat INT UNIQUE,
		majorid INT,
		CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)
);
SHOW INDEX FROM stuinfo;



#二、修改表时添加约束
/*
1、添加列级约束
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 新约束;

2、添加表级约束
ALTER TABLE 表名 ADD [CONSTRAINT 约束名] 约束类型(字段名) [外键的引用];
*/
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
		id INT,
		`name` VARCHAR(20),
		gender CHAR(1),
		seat INT,
		age INT,
		majorid INT
);
#1.添加非空约束
ALTER TABLE stuinfo MODIFY COLUMN `name` VARCHAR(20) NOT NULL;
#2.添加默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;
#3.添加主键
#i.列级约束
ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
#ii.表级约束
ALTER TABLE stuinfo ADD PRIMARY KEY(id);
#4.添加唯一
#i.列级约束
ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
#ii.表级约束
ALTER TABLE stuinfo ADD UNIQUE(seat);
#5.添加外键
ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id);

DESC stuinfo;



#三、修改表时删除约束
#1.删除非空约束
ALTER TABLE stuinfo MODIFY COLUMN `name` VARCHAR(20) NULL;
#2.删除默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT;
#3.删除主键
ALTER TABLE stuinfo DROP PRIMARY KEY;
#4.删除唯一
SHOW INDEX FROM stuinfo;
ALTER TABLE stuinfo DROP INDEX seat;
#5.删除外键
ALTER TABLE stuinfo DROP FOREIGN KEY fk_stuinfo_major;
ALTER TABLE stuinfo DROP INDEX fk_stuinfo_major;

DESC stuinfo;


#注意：当主表需要删除数据时，需要用到以下两种方式
#方式一、级联删除
#定义级联删除，在主表删除数据后，从表引用的数据一并删除
ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id) ON DELETE CASCADE;

#方式二、级联置空
#定义级联置空，在主表删除数据后，从表引用的数据对应关联值置空
ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id) ON DELETE SET NULL;

```

### 标识列
```sql
/*
又称为自增长列
含义：可以不用手动的插入值，系统提供默认的序列值

特点：
1、标识列必须和主键搭配吗？不一定，但要求是一个key
2、一个表可以有几个标识列？至多一个！
3、标识列的类型只能是数值型
4、标识列可以通过SET auto_increment_increment=3;设置步长
可以通过手动插入值，设置起始值
*/
#一、创建表时设置标识列
CREATE TABLE tab_identity(
		id INT PRIMARY KEY AUTO_INCREMENT,
		NAME VARCHAR(20)
);
INSERT INTO tab_identity VALUES(NULL,'john');
INSERT INTO tab_identity(NAME) VALUES('lucy');
SELECT * FROM tab_identity;

SHOW VARIABLES LIKE '%auto_increment%';
SET auto_increment_increment=3;
SET auto_increment_increment=1;

TRUNCATE tab_identity;


#二、修改表时设置标识列
DROP TABLE IF EXISTS tab_identity;
CREATE TABLE tab_identity(
		id INT,
		NAME VARCHAR(20)
);
ALTER TABLE tab_identity MODIFY COLUMN id INT PRIMARY KEY AUTO_INCREMENT;


#三、修改表时删除标识列
ALTER TABLE tab_identity MODIFY COLUMN id INT;
ALTER TABLE tab_identity DROP PRIMARY KEY #不执行此句会保留 PRIMARY KEY

SHOW INDEX FROM tab_identity

DESC tab_identity;
```

### TCL
```sql
/*
TRANSACTION Control Language事务控制语言

事务：
一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行。
案例：转账

事务的ACID属性：
1、原子性Atomicity
事务要么都发生要么都不发生
2、一致性Consistency
事务必须是数据库从一个一致性状态变换成另一个一致性状态(比如交易后的数据库金额不变)
3、隔离性Isolation
一个事务的执行不能被其他事务打扰
4、持久性Durability
指事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何影响


事务的创建：
隐式事务：事务没有明显的开启和结束的标记
1.DDL操作一旦执行，都会自动提交。
	set autocommit=0;对DDL操作失效
2.DML默认情况下一旦执行，就会自动提交。
	我们可以通过set autocommit=0 的方式取消DML操作的自动提交
3.默认在关闭连接时，会自动的提交数据 ?

显示事务：事务具有明显的开启和结束的标记
前提：必须先设置自动提交功能为禁用set autocommit=0;

步骤1:开启事务
SET autocommit=0;
START TRANSACTION;可选的,还是加上吧，删除表的时候不加有问题

步骤2:编写事务中的sql语句(SELECT INSERT UPDATE DELETE)
语句1；
语句2；
...

步骤3:结束事务
COMMIT;提交事务
ROLLBACK;回滚事务

SAVEPOINT 节点名；设置保存点


脏读：对于两个事务T1，T2，T1读取了已经被T2更新但还**没有被提交**的字段。之后T2回滚，T1读取的内容就是临时无效的。  
不可重复读：对于两个事务T1，T2，T1堆区了一个字段，然后T2**更新**了该字段。之后，T1再次读取同一个字段值，值不相同。  
幻读：对于两个事务T1，T2，T1从一个表中读取了一个字段，然后T2在该表中**插入**了一些新的行。之后，如果T1再次读取同一个表，就会多出几行  


事务的隔离级别：
						脏读			 不可重复读			  幻读(出现多数据)
read uncommitted:		✓					✓					✓	
read committed:			✕					✓					✓
repeatable read:		✕					✕					✓
serializable:			✕					✕					✕

mysql中默认第三个隔离级别	repeatable read
oracle中默认第二个隔离级别  read committed

查看隔离级别：	select @@transaction_isolation;
设置隔离级别：	set session|global transaction isolation level 隔离级别;

*/
SHOW ENGINES;
SHOW VARIABLES LIKE 'autocommit';

SET autocommit=0;
INSERT INTO tab_identity VALUES(1,'Alex');
INSERT INTO tab_identity VALUES(2,'Bob');
INSERT INTO tab_identity VALUES(3,'Candy');
COMMIT;

SET autocommit=0;
DELETE FROM tab_identity WHERE id=1;
ROLLBACK;

SELECT * FROM tab_identity


#2.DELETE和 TRUNCATE在事务使用时的区别
#演示 DELETE
DROP TABLE IF EXISTS table_test;
CREATE TABLE table_test(
		id INT PRIMARY KEY auto_increment,
		NAME CHAR(10)
);
INSERT INTO table_test(NAME) VALUES ('alex');
INSERT INTO table_test(NAME) VALUES ('bob');
INSERT INTO table_test(NAME) VALUES ('candy');
SET autocommit=0;
START TRANSACTION;
DELETE FROM table_test;
ROLLBACK;
SELECT * FROM table_test;
#演示 TRUNCATE
DROP TABLE IF EXISTS table_test;
CREATE TABLE table_test(
		id INT PRIMARY KEY auto_increment,
		NAME CHAR(10)
);
INSERT INTO table_test(NAME) VALUES ('alex');
INSERT INTO table_test(NAME) VALUES ('bob');
INSERT INTO table_test(NAME) VALUES ('candy');
SET autocommit=0;
START TRANSACTION;
TRUNCATE TABLE table_test;
ROLLBACK;
SELECT * FROM table_test;


#3.演示SAVEPOINT的使用
SET autocommit=0;
START TRANSACTION;
DELETE FROM table_test WHERE id=3;
SAVEPOINT a;
DELETE FROM table_test WHERE id=2;
ROLLBACK TO a;
SELECT * FROM table_test;

```

### 视图
```sql
/*
含义：一种虚拟存在的表，行和列的数据来自其他数据库中的表，并且是在使用视图时动态生成的，
只保存了sql逻辑，不保存查询结果

应用场景：
1.多个地方用到同样的查询结果
2.该查询结果使用的sql语句较复杂

				创建语法的关键字			是否实际占用物理空间			使用
视图			 CREATE VIEW			  只是保存了sql逻辑			 	增删改查，单一般只查
表				 CREATE TABLE			  占用						  增删改查

*/
#一、创建视图
/*
语法：
CREATE VIEW 视图名
AS
查询语句;
*/
#案例1.查询邮箱中包含a字符的员工名、部门名和工种信息
#i.创建
CREATE VIEW myView1
AS
SELECT last_name,department_name,job_title
FROM employees e
INNER JOIN departments d
ON e.department_id=d.department_id
INNER JOIN jobs j
ON e.job_id=j.job_id;
#ii.使用
SELECT * 
FROM myView1
WHERE last_name LIKE '%a%';

#案例2.查看各部门的平均工资级别
#i.创建视图查看每个部门的平均工资
CREATE VIEW myv2
AS
SELECT AVG(salary) `avg`,department_id
FROM employees e
GROUP BY e.department_id;
#ii.使用
SELECT jg.grade_level,v.department_id
FROM myv2 v
INNER JOIN job_grades jg
ON v.`avg` BETWEEN jg.lowest_sal AND jg.highest_sal;

#案例3.查询平均工资最低的部门信息
SELECT d.*
FROM departments d
INNER JOIN myv2 v
ON v.department_id=d.department_id
WHERE v.`avg`=(
	SELECT MIN(`avg`)
	FROM myv2
);

#案例4.查询平均工资最低的部门名和工资
SELECT d.department_name, v.`avg`
FROM departments d
INNER JOIN myv2 v
ON v.department_id=d.department_id
WHERE v.`avg`=(
	SELECT MIN(`avg`)
	FROM myv2
);


#二、视图的修改
#方式一：
/*
CREATE OR REPLACE VIEW 视图名
AS
查询语句;
*/
CREATE OR REPLACE VIEW myview1
AS
SELECT last_name
FROM employees;

#方式二：
/*
语法：
ALTER VIEW 视图名
AS
查询语句;
*/
ALTER VIEW myview1
AS
SELECT department_id
FROM employees;


#三、删除视图
/*
语法：
DROP VIEW 视图名1,视图名2,...;
*/


#四、查看视图结构
DESC myview1;
#一般用于命令行，名字后面+  \G会显示缩减信息
SHOW CREATE VIEW myview1;


#五、视图的更新
#注意会影响原始表
CREATE OR REPLACE VIEW myv1
AS
SELECT last_name,email
FROM employees;

SELECT * FROM myv1;
SELECT * FROM employees;
#1.插入
INSERT INTO myv1 VALUES('Clark','chenkunk@outlook.com');

#2.修改
UPDATE myv1 SET last_name='Mongo' WHERE last_name='Clark';

#3.删除
DELETE FROM myv1 WHERE last_name='Mongo';

#具备一下特点的视图不允许更新
#i.包含一下关键字的sql语句：分组函数、DISTINCT、GROUP BY、HAVING、UNION或者 UNION ALL
#ii.常量视图
#iii.SELECT 中包含子查询
#iv.JOIN
#v.FROM 一个不能跟新的视图
#vi.WHERE子句的子查询引用了FROM子句中的表

```

### 变量
```sql
/*
系统变量：
		全局变量
		会话变量
		
自定义变量：
		用户变量
		局部变量
		
*/

#一、系统变量
/*
说明：变量由系统变量，不是用户定义，属于服务器层面
使用的语法：
1、查看所有的系统变量
SHOW GLOBAL|[SESSION] VARIABLES;

2、查看部分满足条件的系统变量
SHOW GLOBAL|[SESSION] VARIABLES LIKE '%char%';

3、查看指定的某个系统变量的值
SELECT @@GLOBAL|[SESSION].系统变量名;

4、为某个系统变量赋值
方式一：
SET @@GLOBAL|[SESSION] 系统变量名=值;
方式二：
SET @@GLOBAL|[SESSION].系统变量名=值;

*/
#1.全局变量
/*
作用域：
服务器每次启动将为所有全局变量赋初始值，针对于所有的会话有效，重启后失效
*/
#i.查看所有的全局变量
SHOW GLOBAL VARIABLES;

#ii.查看部分的全局变量
SHOW GLOBAL VARIABLES LIKE '%char%';

#iii.查看指定的全局变量
SELECT @@global.autocommit;
SELECT @@global.transaction_isolation;

#iv.修改指定全局变量的值
SET @@global.autocommit=0;
SET @@global.autocommit=1;


#2、会话变量
/*
作用域：仅针对于当前会话变量
*/
#i.查看所有的会话变量
SHOW VARIABLES;
SHOW SESSION VARIABLES;

#ii.查看部分的会话变量
SHOW VARIABLES LIKE '%char%';
SHOW SESSION VARIABLES LIKE '%char%';

#iii.查看指定的某个会话变量值
SELECT @@autocommit;
SELECT @@transaction_isolation;

#iv.为某个会话变量赋值
#方式一：
SET @@session.transaction_isolation='read-uncommitted';
SET @@transaction_isolation='read-uncommitted';

#方式二：
SET transaction_isolation='read-committed';
SET SESSION transaction_isolation='read-committed';



#二、自定义变量
/*
说明：变量是用户自定义的
使用步骤：
声明
赋值
使用(查看、比较、运算等)

对比用户变量和局部变量：
						作用域						定义和使用的位置											语法
用户变量			当前会话					会话中的任何地方											必须加@符号，不用限定类型
局部变量			BEGIN END中			只能在 BEGIN END中，且为第一句话			一般不用加@符号，需要限定类型
*/
#1、用户变量
/*
作用域：针对于当前会话有效
*/
#i.声明并初始化
/*
赋值的操作符： =或:=

SET @用户变量名=值; 或
SET @用户变量名:=值; 或
SELECT @用户变量名:=值; 
*/

#ii.赋值(更新用户变量的值)
/*
方式一：
SET @用户变量名=值; 或
SET @用户变量名:=值; 或
SELECT @用户变量名:=值; 

方式二：
SELECT 字段 INTO @变量名
FROM 表;
*/
#案例
SET @name='john';
SET @name=100;
SELECT COUNT(*) INTO @count
FROM employees;

#iii.使用(查看用户变量的值)
/*
SELECT @用户变量名;
*/
SELECT @count;


#2、局部变量
/*
作用域：仅仅在定义它的b BEGIN END中有效
应用在 BEGIN END中的第一句话
*/
#i.声明
/*
DECLARE 变量名 类型;
DECLARE 变量名 类型 DEFAULT 值;
*/

#ii.赋值
/*
方式一：
SET 局部变量名=值; 或
SET 局部变量名:=值; 或
SELECT @局部变量名:=值; 

方式二：
SELECT 字段 INTO 局部变量名
FROM 表;
*/

#iii.使用
/*
SELECT 局部变量名;
*/

#案例：声明两个变量并赋初始值，求和，并打印
#1.用户变量
SET @m=1;
SET @n=1;
SET @sum=@m+@n;
SELECT @sum;
#2.局部变量
DECLARE m INT DEFAULT 1;
DECLARE n INT DEFAULT 1;
DECLARE sum INT;
SET sum=m+n;
SELECT sum;
```

### 存储过程和函数
```sql
/*
存储过程和函数：类似于java中的方法	

含义：一组预先编译好的SQL语句的集合，理解成批处理语句

一、创建语法：
CREATE PROCEDURE 存储过程名(参数列表)
BEGIN
		存储过程体(一组合法的SQL语句);
END

注意：
1、参数列表包含三部分
参数模式 参数名 参数类型
举例:
in stuname VARCHAR(20)

参数模式：
IN：该参数可以作为输入，也就是该参数需要调用方传入值
OUT：该参数可以作为输出，也就是该参数可以作为返回值
INOUT：该参数即可以作为输入又可以作为输出，也就是该参数既需要输入值，又可以返回值

2、如果存储过程体仅仅只有一句话，BEGIN END可以省略
存储过程体中的每条SQL语句的结尾要求必须加分号。


存储过程的结尾可以使用 DELIMITER 重新设置
后续的;结束符就换成定义的结束符即可
语法:
DELIMITER 结束标记;
如：
DELIMITER $;

二、调用语法：
CALL 存储过程名(实参列表);

三、删除存储过程
语法：
DROP PROCEDURE 存储过程名;

四、查看存储过程的信息
语法：
SHOW CREATE PROCEDURE 存储过程名;
*/
#1.空参列表
#案例：插入到admin表中五条记录
SELECT * FROM admin;

DELIMITER $
CREATE PROCEDURE myp1()
BEGIN
		INSERT INTO admin(username,`password`)
		VALUES('Alice','0000'),('Bob','0000'),('Can','0000'),('Dandy','0000'),('Ellen','0000');
END $
CALL myp1()$


#2.创建带 IN模式参数的存储过程
#案例1:创建存储过程实现根据女神名，查询对应的男神信息
CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
BEGIN
		SELECT bo.*
		FROM boys bo
		RIGHT JOIN beauty b 
		ON bo.id=b.boyfriend_id
		WHERE b.`name`=beautyName;
END;
CALL myp2('tangyixing');

#案例2:创建存储过程实现，用户是否登录成功
DROP PROCEDURE IF EXISTS myp3;
CREATE PROCEDURE myp3(IN username VARCHAR(10), IN `password` VARCHAR(10))
BEGIN
		DECLARE result INT DEFAULT 0;
		
		SELECT COUNT(*) INTO result
		FROM admin
		WHERE admin.username=username
		AND admin.`password`=`password`;
		
		SELECT IF(result>0,'成功','失败');
END;
CALL myp3('Bob','0000');


#3、创建带 OUT模式的存储过程
#案例1:根据女神名，返回对应的男神名
CREATE PROCEDURE myp4(IN beautyName VARCHAR(10),OUT boyName VARCHAR(10))
BEGIN
		SELECT bo.boyName INTO boyName
		FROM boys bo
		RIGHT JOIN beauty be
		ON bo.id=be.boyfriend_id
		WHERE be.`name`=beautyName;
END;
CALL myp4('jinxing',@bName);
SELECT @bName;

#案例1:根据女神名，返回对应的男神名和男神魅力值
#不加 INTO 也可以
DROP PROCEDURE myp5;
CREATE PROCEDURE myp5(IN beautyName VARCHAR(10),OUT boyName VARCHAR(10),OUT userCP INT)
BEGIN
		SELECT bo.boyName,bo.userCP INTO boyName,userCP
		FROM boys bo
		RIGHT JOIN beauty be
		ON bo.id=be.boyfriend_id
		WHERE be.`name`=beautyName;
END;
CALL myp5('王语嫣',@bName,@bCP);
SELECT @bName,@bCP;


#4.创建带 INOUT模式参数的存储过程
#案例1:传入a和b两个值，最终a和b都翻倍并返回
DROP PROCEDURE myp6;
CREATE PROCEDURE myp6(INOUT a INT, INOUT b INT)
BEGIN
		SET a=a*2;
		SET b=b*2;
END;
SET @num1=1;
SET @num2=2;
CALL myp6(@num1,@num2);
SELECT @num1,@num2;


#5.删除过程名
DROP PROCEDURE myp1;


#6.查看存储过程的信息
SHOW CREATE PROCEDURE myp2;
```

### 函数
```sql
/*
存储过程：可以有0个返回，也可以有多个返回，适合批量插入、批量更新
函数：只能有1个返回，适合做处理数据后返回一个结果

一、创建语法
CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
BEGIN
		函数体
END;

注意：
1.参数列表包含两部分：参数名 参数类型
2.函数体：肯定会有RETURN语句，如果没有会报错，
如果return语句没有放在函数体的最后也不报错，但不建议
return 值;
3.函数体中仅有一句话，则可以省略 BEGIN END

二、调用语法
SELECT 函数名(参数列表)

三、查看函数
SHOW CREATE FUNCTION 函数名;

四、删除函数
DROP FUNCTION 函数名;
*/
#1.无参返回
#案例：返回公司的员工个数
SET GLOBAL log_bin_trust_function_creators=1;
CREATE FUNCTION myf1() RETURNS INT
BEGIN
		DECLARE m INT;
		SELECT COUNT(*) INTO m
		FROM employees;
		RETURN m;
END;
SELECT myf1();

#2.有参有返回
#案例1:根据员工名，返回他的工资
DROP FUNCTION IF EXISTS myf2;
CREATE FUNCTION myf2(`name` VARCHAR(10)) RETURNS INT
BEGIN
		SET @sal=0;
		SELECT salary INTO @sal
		FROM employees
		WHERE employees.first_name=`name`;
		RETURN @sal;
END;
SELECT myf2('Valli');

#案例2:根据部门名，返回该部门的平均工资
DROP FUNCTION IF EXISTS myf3;
CREATE FUNCTION myf3(dName VARCHAR(20)) RETURNS INT
BEGIN
		SET @ret=0;
		SELECT avg(salary) INTO @ret
		FROM employees
		GROUP BY department_id
		HAVING department_id=(
				SELECT department_id
				FROM departments
				WHERE department_name=dName
		);
		RETURN @ret;
END;
SELECT myf3('Shi');


#3.查看函数
SHOW CREATE FUNCTION myf1;


#4.删除函数
DROP FUNCTION myf3;
```

### 流程控制结构
```sql
/*
顺序结构：程序从上往下依次执行
分支结构：程序从两条或多条路径中选择一条去执行
循环结构：程序在满足一定条件的基础上，重复执行一段代码
*/
#一、分支结构
#1.IF函数
/*
功能：实现简单的双分支
语法：
IF(表达式1,表达式2,表达式3)
执行顺序:
如果表达式1成立，则 IF函数返回表达式2的值，否则返回表达式3的值

应用：任何地方
*/

#2.case结构
/*
情况1:类似于java中的switch语句，一般用于实现等值判断
语法：
		CASE 变量|表达式|字段
		WHEN 要判断的值 THEN 返回的值1或[语句1;]
		WHEN 要判断的值 THEN 返回的值2或[语句2;]
		...
		ELSE 要返回的值n或[语句n;]
		END CASE;

情况2:类似于java的多重 IF语句，一般用于实现区间判断
		CASE
		WHEN 要判断的条件1 THEN 返回的值1或[语句1;]
		WHEN 要判断的条件2 THEN 返回的值2或[语句2;]
		...
		ELSE 要返回的值n或[语句;]
		END CASE;


特点：
i.
可以作为表达式，嵌套在其他语句中使用，可以放在任何地方
可以作为独立的语句使用，只能放在 BEGIN END
ii.
如果WHEN中的值满足或条件成立，则执行对应的THEN后面的语句，并且结束CASE
如果都不满足，则执行ELSE中的语句或值
iii.
ELSE可以省略，如果ELSE省略了，并且所有WHEN条件都不满足，则返回NULL

*/
#案例
#创建存储过程，根据传入的成绩，来显示等级，比如传入的成绩：90-100，显示A，
#80-90，显示B，60-80，显示C，否则，显示D
DROP PROCEDURE IF EXISTS myp1;
CREATE PROCEDURE myp1(IN score INT)
BEGIN
		SELECT CASE
		WHEN score BETWEEN 90 AND 100 THEN 'A'
		WHEN score BETWEEN 80 AND 90 THEN 'B'
		WHEN score BETWEEN 60 AND 80 THEN 'C'
		ELSE 'D'
		END;
END;
CALL myp1(98);

#IF结构
/*
功能：实现多重分支

语法：
IF 条件1 THEN 语句1;
ELSEIF 条件2 THEN 语句2;
...
ELSE 语句n;
END IF;

应用场合：应用在BEGIN END中
*/
#案例1:根据传入的成绩，来显示等级，比如传入的成绩：90-100，返回A，
#80-90，返回B，60-80，返回C，否则，返回D
DROP FUNCTION IF EXISTS myf1;
CREATE FUNCTION myf1(score INT) RETURNS CHAR
BEGIN
		DECLARE grade CHAR;
		IF 90<=score AND score<=100 THEN SET grade='A';
		ELSEIF 80<=score THEN SET grade='B';
		ELSEIF 70<=score THEN SET grade='C';
		ELSE SET grade='D';
		END IF;
		RETURN grade;
END;
SELECT myf1(89);



#二、循环结构
/*
分类：
WHILE、LOOP、REPEAT

循环控制:
ITERATE 类似于continue，继续，结束本次循环，继续下一次
LEAVE 类似于break,跳出，结束当前所在的循环

*/
#1.WHILE
/*
语法：
[标签:]WHILE 循环条件 DO
	循环体;
END WHILE [标签];
*/

#2.loop
/*
语法：
[标签:] LOOP
		循环体;
END LOOP [标签];

可以用来模拟简单的死循环

*/

#3.REPEAT
/*
[标签:]REPEAT
	循环体;
UNTIL 结束循环的条件 
END REPEAT [标签];

*/

#案例：批量插入，根据次数插入到admin表中多条记录
DROP PROCEDURE IF EXISTS insertRecord;
CREATE PROCEDURE insertRecord(IN insertCount INT)
BEGIN
		DECLARE i INT DEFAULT insertCount;
		WHILE i>0 DO
			INSERT INTO admin(username,`password`)
			VALUES ('Zed','1111');
			SET i = i-1;
		END WHILE;
END;
CALL insertRecord(5);

#2.添加 LEAVE语句
#案例：批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
TRUNCATE admin;
DROP PROCEDURE IF EXISTS insertRecord;
CREATE PROCEDURE insertRecord(IN insertCount INT)
BEGIN
		DECLARE i INT DEFAULT 1;
		label:WHILE i<insertCount DO
			INSERT INTO admin(username,`password`)
			VALUES (CONCAT('Zed',i),'1111');
			SET i = i+1;
			IF i>20 THEN LEAVE label;
			END IF;
		END WHILE label;
END;
CALL insertRecord(30);
SELECT * FROM admin;

#3.添加 ITERATE语句
#案例：批量插入，根据次数插入到admin表中多条记录，只插入偶数次
TRUNCATE admin;
DROP PROCEDURE IF EXISTS insertRecord;
CREATE PROCEDURE insertRecord(IN insertCount INT)
BEGIN
		DECLARE i INT DEFAULT 0;
		label:WHILE i<=insertCount DO
			SET i = i+1;
			IF i%2=1 THEN ITERATE label;
			END IF;
			INSERT INTO admin(username,`password`)
			VALUES (CONCAT('Yellow',i),'2222');
		END WHILE label;
END;
CALL insertRecord(50);
SELECT * FROM admin;
```