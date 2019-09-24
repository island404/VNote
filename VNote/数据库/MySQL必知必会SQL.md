# MySQL必知必会SQL

```sql
CREATE DATABASE IF NOT EXISTS mysql_crash_course;

# 基本查询
USE mysql_crash_course;# 使用数据库:mysql_crash_course
SELECT * FROM products;
SELECT prod_id, prod_price FROM products;
SELECT prod_id, prod_price FROM products LIMIT 5;# 选择多列, 取前5行(未指定排序方式)
SELECT prod_id, prod_price FROM products LIMIT 0,5;# 选择多列, 从0行开始, 取前5行, 作用同上
SELECT prod_id, prod_price FROM products LIMIT 5,5;# 选择多列, 取后5行, 以此类推...
SELECT DISTINCT vend_id FROM products;# 去重
SELECT prod_id, prod_price FROM mysql_crash_course.products;# 使用全限定名: database_name.table_name

# 排序: ODER BY 子句
# 不指定排序方向默认按升序排列(数字按数值从小到大, 字母按a到z)
SELECT prod_id, prod_price FROM products ORDER BY prod_price ASC;# 检索多个列, 按 prod_price 升序排序, 默认升序所以ASC可不写
SELECT prod_id, prod_price FROM products ORDER BY prod_price DESC;# 检索多个列, 按 prod_price 降序排序
SELECT * FROM products ORDER BY prod_price DESC LIMIT 1;# 通过 ORDER BY 和 LIMIT 组合使用找最值

# 过滤: WHERE 子句
## 同时使用 ORDER BY 和 WHERE 子句时,应该让 ORDER BY 位于 WHERE 之后, 否则将会产生错误
# 条件操作符: =等于  <>不等于  !=不等于  <小于  <=小于等于  >大于  >=大于等于  BETWEEN在指定的两个值之间
SELECT prod_name, prod_price FROM products WHERE prod_price < 10;
SELECT prod_name, prod_price FROM products WHERE prod_name = 'carrots';# 字符需要用''或""(注意区分和''), 字符匹配时默认不区分大小写
SELECT prod_name, prod_price FROM products WHERE prod_price BETWEEN 10 AND 50;# BETWEEN 匹配范围中所有的值, 包括指定的开始值和结束值
SELECT * FROM customers WHERE cust_email IS NULL;# 空值查询
# 逻辑操作符: AND OR NOT
SELECT prod_name, prod_price FROM products WHERE  prod_name = 'bird seed' AND prod_price < 50;
SELECT prod_name, prod_price FROM products WHERE  prod_price <10 OR prod_price > 50;
## AND和OR操作符组合使用时, AND 优先级更高; 明确分组时使用圆括号划分
SELECT prod_name, prod_price, vend_id FROM products WHERE  (vend_id = 1002 OR vend_id = 1003) AND prod_price < 10;
## IN 操作符: 效果和 OR 相近
SELECT prod_name, prod_price, vend_id FROM products WHERE  vend_id IN(1002, 1003);
## IN 想对于 OR 的优势
### 1. 在使用长的合法选项清单时,IN操作符的语法更清楚且更直观
### 2. 在使用IN时,计算的次序更容易管理(因为使用的操作符更少)
### 3. IN操作符一般比OR操作符清单执行更快。
### 4. IN的最大优点是可以包含其他SELECT语句,使得能够更动态地建立WHERE子句。
# NOT: MySQL 支持使用 NOT 对 IN, BETWEEN 和 EXISTS 子句取反

# 使用通配符搜索
## % 通配符: 最常使用的通配符是百分号(%), 在搜索串中, % 表示任何字符出现任意次数(包括0个字符)
## _ 通配符: 下划线的用途与%一样,但下划线只匹配单个字符而不是多个字符。与 % 能匹配0个字符不一样, _ 总是匹配一个字符,不能多也不能少。
SELECT prod_name, prod_price FROM products WHERE prod_name LIKE '%lin%';
SELECT prod_name, prod_price FROM products WHERE prod_name LIKE 's%e';

# 使用正则表达式
## 略

# 计算字段
##　拼接查询结果：Concat()函数; 
SELECT CONCAT(vend_name, '-' ,vend_address) FROM vendors ORDER BY vend_name;# 查询结果集为: vend_name-vend_address 格式
SELECT vend_name AS NAME, vend_address AS addr FROM vendors ORDER BY NAME;# 起别名：AS 或者空格
## 查询结果去除空格: LTrim()删除左边的空格；RTrim() 删除右边的空格；Trim() 删除左右两边的空格
SELECT TRIM(vend_name), TRIM(vend_address) FROM vendors ORDER BY vend_name;
## 对查询结果进行二次计算:
SELECT prod_id, quantity, item_price, quantity*item_price AS expanded_price FROM orderitems;
SELECT NOW();

# 数据处理函数
## 文本处理函数(更多常用文本处理函数见表11-1)
SELECT UPPER(vend_name) FROM vendors;# Uper() 函数: 字母全部改成大写
## 日期函数(更多日期处理函数见表11-2)
SELECT cust_id, order_num, order_date FROM orders WHERE order_date = '2005-09-01';
### 如果只需要精确到日期部分, 建议使用 Data() 函数, 于是上面语句改为
SELECT cust_id, order_num, order_date FROM orders WHERE DATE(order_date) = '2005-09-01';
## 使用 BETWEEN... AND... 查询某月所有数据(年同理)
SELECT sust_id, order_num FROM orders WHERE (order_date) BETWEEN '2005-09-01' AND '2005-09-30';
## 使用 Year()函数和 Month() 函数, 可以避免记忆每月多少天和处理闰年2月; Year()可以从日期（或日期时间）中返回年份, Month()从日期中返回月份
SELECT sust_id, order_num FROM orders WHERE YEAR(order_data) = 2005 AND MONTH(order_date) = 9;
## 常用数值处理函数见表11-3
SELECT ABS(-10 MOD(3));# ABS() 函数返回绝对值; MOD() 函数返回余数

# 聚集函数
SELECT AVG(prod_price) AS avg_price FROM products WHERE vend_id = 1003;# AVG() 函数求平均值
SELECT COUNT(*) AS num_cust FROM customers;# 统计行数: COUNT(*) 包含null行; COUNT(column_name) 忽略null行
SELECT MAX(prod_price) AS max_price FROM products;# MAX() 返回列最大值; MIN() 返回最小值
SELECT SUM(item_price * quantity) AS items_ordered FROM orderitems WHERE order_num = 20005;# SUM() 返回列求和
SELECT AVG(DISTINCT prod_price) AS avg_price FROM products WHERE vend_id = 1003;# 使用 DISTINCT 去重

# 分组数据
## 创建分组
### GROUP BY子句指示MySQL按vend_id排序并分组数据, 然后对每个组而不是整个结果集进行聚集
### 使用 WITH ROLLUP 可以得到每个分组以及每个分组汇总级别（针对每个分组）的值
SELECT vend_id, COUNT(*) AS num_prods FROM products GROUP BY vend_id WITH ROLLUP;
## 过滤分组
SELECT cust_id, COUNT(*) AS orders FROM orders GROUP BY cust_id HAVING COUNT(*) >= 2;# 过滤分组汇总数量大于2的结果集
### WHERE 和 HAVING 联合使用
#### 列出具有2个（含）以上、价格为10（含）以上的产品的供应商：
SELECT vend_id, COUNT(*) AS num_prods FROM products WHERE prod_price >= 10 GROUP BY vend_id HAVING COUNT(*) >= 2;
## 分组和排序
### ORDER BY 和 GROUP BY 配合使用
#### 检索总计订单价格大于等于50的订单的订单号和总计订单价格，用ORDER BY子句按总计订单价格排序输出
SELECT order_num, SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price) >= 50 ORDER BY ordertotal;
## SELECT子句及其顺序
SELECT FROM WHERE GROUP BY HAVING ORDER BY LIMIT

# 子查询
## 使用子查询过滤(递进关系)；虽然子查询一般与IN操作符结合使用，但也可以用于测试等于（=）、不等于（<>）等
SELECT cust_id FROM orders WHERE order_num IN (SELECT order_num FROM orderitems WHERE prod_id = 'TNT2');
## 作为计算字段使用子查询(并列关系)；下面 sql 语句使用了全限定名
SELECT cust_name, cust_state, (SELECT COUNT(*) FROM orders WHERE orders.cust_id = customers.cust_id) AS orders FROM customers ORDER BY cust_name;
## 要注意子查询有性能问题，子查询嵌套层数最好不要太多

# 联结
## 理解关系表于联结
## 创建联结
SELECT vend_name, prod_name, prod_price FROM vendors, products WHERE vendors.vend_id = products.vend_id ORDER BY vend_name, prod_name;
### 内联结
#### 内部联结：上述使用联结称为等值联结(equijoin)，它基于两个表之间的相等测试。这种联结也称为内部联结。内联结可以使用另外的语法来明确指定联结的类型。
SELECT vend_name, prod_name, prod_price FROM vendors INNER JOIN products ON vendors.vend_id = products.vend_id;# INNER 可省略
### 联结多个表(由于性能问题，应慎重考虑多表关联查询)
SELECT prod_name, vend_name, prod_price, quantity FROM orderitems, products, vendors 
WHERE products.vend_id = vendors.vend_id AND orderitems.prod_id = products.prod_id AND order_num = 20005;
## 子查询和联结查询对比(15.3)

# 创建高级联结
## 表别名：使用表别名可以缩短sql语句
### 列别名与表别名的区别：表别名只在查询执行中使用。与列别名不一样，表别名不返回到客户机。
## 自联结与子查询：两者哪个更快应该进行测试，但有时候自联结一般比子查询快得多
SELECT prod_id, prod_name FROM products WHERE vend_id = (SELECT vend_id FROM products WHERE prod_id = 'DTNTR');# 自联结
SELECT p1.prod_id, p1.prod_name FROM products AS p1, products AS p2 WHERE p1.vend_id = p2.vend_id AND p2.prod_id = 'DTNTR';# 自联结，必须使用别名
## 自然联结：迄今为止我们建立的每个内部联结都是自然联结，很可能我们永远都不会用到不是自然联结的内部联结。
SELECT customers.`cust_id`, orders.`order_num` FROM customers INNER JOIN orders ON customers.`cust_id` = orders.`cust_id`;# 内部联结(不包含空值)；INNER 可省略
## 外部联结：联结包含了那些在相关表中没有关联行的行。这种类型的联结称为外部联结。外联结查询结果集包含空值
SELECT customers.`cust_id`, orders.`order_num` FROM customers LEFT OUTER JOIN orders ON customers.`cust_id` = orders.`cust_id`;# 外部联结(包含空值)；OUTER 可省略
### 在使用OUTER JOIN语法时，必须使用RIGHT或LEFT关键字指定包括其所有行的表（RIGHT指出的是OUTER JOIN右边的表，而LEFT指出的是OUTER JOIN左边的表）。
### 上面的例子使用LEFT OUTER JOIN从FROM子句的左边表（customers表）中选择所有行。为了从右边的表中选择所有行，应该使用RIGHT OUTER JOIN，
## 带聚合函数的联结

# 组合查询
## UNION
## 略

# 全文搜索
## 略

# 插入数据
## 插入全部字段：按表字段顺序插入，字段数据类型需要全部匹配，主键位设置自增插入null
INSERT INTO Customers VALUES(NULL, 'Pep E. LaPew', '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL, NULL);
## 插入所需字段：相比上面的sql语句更安全，但是更烦琐
### 指定列插入数据时可以省略列，如自增主键cust_id可以省略
 customers(cust_name, cust_address, cust_city, cust_state, cust_zip, cust_country, cust_contact, cust_email)
VALUES('Pep E. LaPew', '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL, NULL);
## 插入多行
### 1.编写多条独立sql插入语句，各句间用分号隔开
### 2.假如各条sql语句插入的字段都相同，则在 VALUES(...) 后加所需值的集合，各集合用逗号隔开：INSERT INTO ... VALUES(...), (...), (...)... ;
## 检索插入：INSERT SELECT 一个表中查找出的数据插入到另一个表

# 更新数据
## 更新列数据
UPDATE customers SET cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;# 更新一列
UPDATE customers SET cust_name = 'The Fudds', cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;# 更新多列
### 在update语句中使用子查询，可以先查找出来再更新
### 为即使是发生错误，也继续进行更新，可使用IGNORE关键字：UPDATE IGNORE customers…
## 删除列数据（置为空）
UPDATE customers SET cust_email = NULL WHERE cust_id = 10005;# 更新一列
## 删除行数据
DELETE FROM customers WHERE cust_id = 10006;# 忽略 WHERE 条件将删除所有行
## 删除所有表数据
DELETE FROM customers;# 删除表中所有数据
TRUNCATE customers；# 删除整个表，再重建一个（速度比上面的更快）
### MySQL没有撤销（undo）按钮，所以每次更新和删除数据都要谨慎，防止错误更新/删除

# 操纵表
## 创建表
CREATE TABLE IF NOT EXISTS customers(
  cust_id      int       NOT NULL AUTO_INCREMENT,
  cust_name    char(50)  NOT NULL ,
  cust_address char(50)  NULL ,
  cust_city    char(50)  NULL ,
  cust_state   char(5)   NULL ,
  cust_zip     char(10)  NULL ,
  cust_country char(50)  NULL ,
  cust_contact char(50)  NULL ,
  cust_email   char(255) NULL ,
  PRIMARY KEY (cust_id)
) ENGINE=InnoDB; # 忽略 ENGINE=... 将使用默认引擎;
### 每个表只允许一个AUTO_INCREMENT列，而且它必须被索引（如，通过使它成为主键）
### 可以通过last_insert_id()函数获取最新插入的自增主键值：SELECT last_insert_id() ...
## 修改表
ALTER TABLE vendors ADD vend_phone CHAR(20);# 增加列
ALTER TABLE Vendors DROP COLUMN vend_phone;# 删除列
ALTER TABLE orderitems ADD CONSTRAINT fk_orderitems_orders FOREIGN KEY (order_num) REFERENCES orders (order_num);# 定义外键
## 删除表(删除整个表，而不是其内容)
drop table customers2;
## 重命名表
rename table customers2 to customers3, orders2 to orders3;# 多个表以此类推

# 视图(MySQL5)
## 视图是虚拟的表。与包含数据的表不一样，视图只包含使用时动态检索数据的查询。
## 视图不包含数据，每次使用视图时，都必须处理查询执行时所需的任一个检索，所以多联结或复杂视图查询会有性能问题
```