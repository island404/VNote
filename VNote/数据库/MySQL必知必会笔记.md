# MySQL必知必会笔记

基本语法
```sql
# 手写 SQL 语法
SELECT (DISTINCT)
    <select_list>
FROM
    <left_table> <join_type> JOIN <right_table> ON <join_condition>
WHERE
    <where_condition>
GROUP BY
    <group_by_list>
HAVING
    <having_condition>
ORDER BY
    <order_by_condition>
LIMIT
    <limit_number>


# 机读 SQL 语句
FROM <left_table>
ON <join_condition>
WHERE <where_condition>
GROUP BY <group_by_list>
HAVING <having_condition>
SELECT
DISTINCT <select_list>
ORDER BY <order_by_cndition>
LIMIT <limit_number>
```

**通过 ORDER BY 和 LIMIT 组合使用找最值**

**同时使用 ORDER BY 和 WHERE 子句时,应该让 ORDER BY 位于 WHERE 之后, 否则将会产生错误**

**IN 相对于 OR 的优势**
1. 在使用长的合法选项清单时,IN操作符的语法更清楚且更直观
2. 在使用IN时,计算的次序更容易管理(因为使用的操作符更少)
3. IN操作符一般比OR操作符清单执行更快。
4. IN的最大优点是可以包含其他SELECT语句,使得能够更动态地建立WHERE子句。

**为什么使用NOT**
对于简单的 WHERE子句,使用NOT确实没有什么优势。
但在更复杂的子句中, NOT是非常有用的。例如,在与 IN操作符联合使用时,NOT使找出与条件列表不匹配的行非常简单。
MySQL 支 持 使 用 NOT 对 IN 、 BETWEEN 和 EXISTS 子句取反,这与多数其他 DBMS 允许使用 NOT 对各种条件取反有很大的差别。

**使用通配符的技巧**
MySQL的通配符很有用。但这种功能是有代价的: 通配符搜索的处理一般要比前面讨论的其他搜索所花时间更长。这里给出一些使用通配符要记住的技巧。
* 不要过度使用通配符。如果其他操作符能达到相同的目的,应该使用其他操作符。
* 在确实需要使用通配符时, 除非绝对有必要,否则不要把它们用在搜索模式的开始处。把通配符置于搜索模式的开始处, 搜索起来是最慢的。
* 仔细注意通配符的位置。如果放错地方,可能不会返回想要的数据

**尾空格可能会干扰通配符匹配。**
例如,在保存词anvil 时 , 如 果 它 后 面 有 一 个 或 多 个 空 格 , 则 子 句 WHERE prod_name LIKE '%anvil'将不会匹配它们,因为在最后的 l  后有多余的字符。解决这个问题的一个简单的办法是在搜索模式最后附加一个% 。一个更好的办法是使用函数(第11章将会介绍)去掉首尾空格。

**注意NULL**
虽然似乎%通配符可以匹配任何东西,但有一个例外,即NULL。即使是WHERE prod_name LIKE '%'也不能匹配用值NULL 作为产品名的行。

**MySQL 语句拼接**
多数DBMS使用 + 或 || 来实现拼接,MySQL则使用 Concat() 函数来实现。

**删除空格: Trim函数**
LTrim() 删除左边的空格
RTrim() 删除右边的空格
Trim() 删除左右两边的空格

**SElECT 测试计算**
如:
SELECT NOW();# 获取当前时间

**表11-1 常用的文本处理函数**
Left() 返回串左边的字符
Length() 返回串的长度
Locate() 找出串的一个子串
Lower() 将串转换为小写
LTrim() 去掉串左边的空格
Right() 返回串右边的字符
RTrim() 去掉串右边的空格
Soundex() 返回串的SOUNDEX值
SubString() 返回子串的字符
Upper() 将串转换为大写

**表11-2 常用日期和时间处理函数**
AddDate() 增加一个日期（天、周等）
AddTime() 增加一个时间（时、分等）
CurDate() 返回当前日期
CurTime() 返回当前时间
Date() 返回日期时间的日期部分
DateDiff() 计算两个日期之差
Date_Add() 高度灵活的日期运算函数
Date_Format() 返回一个格式化的日期或时间串
Day() 返回一个日期的天数部分
DayOfWeek() 对于一个日期，返回对应的星期几
Hour() 返回一个时间的小时部分
Minute() 返回一个时间的分钟部分
Month() 返回一个日期的月份部分
Now() 返回当前日期和时间
Second() 返回一个时间的秒部分
Time() 返回一个日期时间的时间部分
Year() 返回一个日期的年份部分

**表11-3 常用数值处理函数**
Abs() 返回一个数的绝对值
Cos() 返回一个角度的余弦
Exp() 返回一个数的指数值
Mod() 返回除操作的余数
Pi() 返回圆周率
Rand() 返回一个随机数
Sin() 返回一个角度的正弦
Sqrt() 返回一个数的平方根
Tan() 返回一个角度的正切

**MAX() MIN()**
对非数值数据使用MIN() MIN()函数与MAX()函数类似，
MySQL允许将它用来返回任意列中的最小值，包括返回文本列中的最小值。在用于文本数据时，如果数据按相应的列排序，则MIN()返回最前面的行;
MIN()函数忽略列值为NULL的行。

**GROUP BY子句一些重要的规定**

* GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制。
* 如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）。
* GROUP BY子句中列出的每个列都必须是检索列或有效的表达式（但不能是聚集函数）。如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式。不能使用别名。
* 除聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子句中给出。
* 如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列中有多行NULL值，它们将分为一组。
* GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前。
* 使用WITH ROLLUP关键字，可以得到每个分组以及每个分组汇总级别（针对每个分组）的值，示例：
```sql
SELECT `vend_id`, COUNT(*) AS `num_prods` FROM `products` GROUP BY `vend_id` WITH ROLLUP;
```

**WHERE 和 HAVING**
我们已经看到了WHERE子句的作用（第6章中引入）。但是，在这个例子中WHERE不能完成任务，因为WHERE过滤指定的是行而不是分组。事实上，WHERE没有分组的概念。那么，不使用WHERE使用什么呢？MySQL为此目的提供了另外的子句，那就是HAVING子句。HAVING非常类似于WHERE。事实上，目前为止所学过的所有类型的WHERE子句都可以用HAVING来替代。唯一的差别是WHERE过滤行，而HAVING过滤分组。

**HAVING支持所有WHERE操作符**
在第6章和第7章中，我们学习了WHERE子句的条件（包括通配符条件和带多个操作符的子句）。所学过的有关WHERE的所有这些技术和选项都适用于HAVING。它们的句法是相同的，只是关键字有差别。

**HAVING和WHERE的差别**
这里有另一种理解方法，WHERE在数据分组前进行过滤，HAVING在数据分组后进行过滤。这是一个重要的区别，WHERE排除的行不包括在分组中。这可能会改变计算值，从而影响HAVING子句中基于这些值过滤掉的分组。

**表13-1 ORDER BY与GROUP BY**
| ORDER BY     | GROUP BY     |
| --- | --- |
| 排序产生的输出     | 分组行。但输出可能不是分组的顺序     |
| 任意列都可以使用（甚至非选择的列也可以使用）     | 只可能使用选择列或表达式列，而且必须使用每个选择列表达式     |
| 不一定需要     | 如果与聚集函数一起使用列（或表达式），则必须使用     |

**不要忘记ORDER BY**
一般在使用GROUP BY子句时，应该也给出ORDER BY子句。这是保证数据正确排序的唯一方法。千万不要仅依赖GROUP BY排序数据

**表13-2 SELECT子句及其顺序**
| 子句    | 说明    | 是否必须使用    |
| --- | --- | --- |
| SELECT     | 要返回的列或表达式    | 是    |
| FROM    | 从中检索数据的表    | 仅在从表选择数据时使用    |
| WHERE    | 行级过滤    | 否    |
| GROUP BY    | 分组说明    | 仅在按组计算聚集时使用    |
| HAVING    | 组级过滤    | 否    |
| ORDER BY    | 输出排序顺序    | 否    |
| LIMIT    | 要检索的行数    | 否    |


**关系表设计初步探讨**
假如有一个包含产品目录的数据库表，其中每种类别的物品占一行。对于每种物品要存储的信息包括产品描述和价格，以及生产该产品的供应商信息。
现在，假如有由同一供应商生产的多种物品，那么在何处存储供应商信息（如，供应商名、地址、联系方法等）呢？将这些数据与产品信息分开存储的理由如下。

* 因为同一供应商生产的每个产品的供应商信息都是相同的，对每个产品重复此信息既浪费时间又浪费存储空间。
* 如果供应商信息改变（例如，供应商搬家或电话号码变动），只需改动一次即可。
* 如果有重复数据（即每种产品都存储供应商信息），很难保证每次输入该数据的方式都相同。不一致的数据在报表中很难利用。

关键是，**相同数据出现多次决不是一件好事，此因素是关系数据库设计的基础**。关系表的设计就是要保证把信息分解成多个表，一类数据一个表。各表通过某些常用的值（即关系设计中的关系（relational））互相关联。
在这个例子中，可建立两个表，一个存储供应商信息，另一个存储产品信息。vendors表包含所有供应商信息，每个供应商占一行，每个供应商具有唯一的标识。此标识称为主键（primary key）（在第1章中首次提到），可以是供应商ID或任何其他唯一值。products表只存储产品信息，它除了存储供应商ID（vendors表的主键）外不存储其他供应商信息。vendors表的主键又叫作products的外键，它将vendors表与products表关联，利用供应商ID能从vendors表中找出相应供应商的详细信息。

**外键（foreign key）**
外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。这样做的好处如下：

* 供应商信息不重复，从而不浪费时间和空间；
* 如果供应商信息变动，可以只更新vendors表中的单个记录，相关表中的数据不用改动；
* 由于数据无重复，显然数据是一致的，这使得处理数据更简单。

总之，关系数据可以有效地存储和方便地处理。因此，关系数据库的可伸缩性远比非关系数据库要好。

**可伸缩性（scale）**
能够适应不断增加的工作量而不失败。设计良好的数据库或应用程序称之为可伸缩性好（scale well）。

**理解连接**
如果数据存储在多个表中，怎样用单条SELECT语句检索出数据？答案是使用联结。简单地说，联结是一种机制，用来在一条SELECT语句中关联表，因此称之为联结。
重要的是，要理解联结不是物理实体，它在实际的数据库表中不存在。联结由MySQL根据需要建立，它存在于查询的执行当中。

**维护引用完整性：**
为避免在外键中插入非法数据（一般就是指不存在的数据或者空值），可指示MySQL只允许在products表的供应商ID列中出现合法值（即出现在vendors表中的供应商）。这就是维护引用完整性，它是通过在表的定义中指定主键和外键来实现的。（这将在第21章介绍。）

**联结与笛卡儿积**

**用自联结而不用子查询**
自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。虽然最终的结果是相同的，但有时候处理联结远比处理子查询快得多。应该试一下两种方法，以确定哪一种的性能更好。

**联结及其使用的某些要点**
* 注意所使用的联结类型。一般我们使用内部联结，但使用外部联结也是有效的。
* 保证使用正确的联结条件，否则将返回不正确的数据。
* 应该总是提供联结条件，否则会得出笛卡儿积。
* 在一个联结中可以包含多个表，甚至对于每个联结可以采用不同的联结类型。虽然这样做是合法的，一般也很有用，但应该在一起测试它们前，分别测试每个联结。这将使故障排除更为简单。

**UNION 使用规则**
* UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔；
* UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过各个列不需要以相同的次序列出）；
* 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）；

**插入数据**
插入可以用几种方式使用：
* 插入完整的行；
* 插入行的一部分；
* 插入多行；
* 插入某些查询的结果。

**总是使用列的列表**
一般不要使用没有明确给出列的列表的INSERT语句。使用列的列表能使SQL代码继续发挥作用，即使表结构发生了变化。

**仔细地给出值**
不管使用哪种INSERT语法，都必须给出VALUES的正确数目。如果不提供列名，则必须给每个表列提供一个值。如果提供列名，则必须对每个列出的列给出一个值。如果不这样，将产生一条错误消息，相应的行插入不成功。

**省略列**
如果表的定义允许，则可以在INSERT操作中省略某些列。省略的列必须满足以下某个条件。

* 该列定义为允许NULL值（无值或空值）。
* 在表定义中给出默认值。这表示如果不给出值，将使用默认值。

如果对表中不允许NULL值且没有默认值的列不给出值，则MySQL将产生一条错误消息，并且相应的行插入不成功。

**提高整体性能**
数据库经常被多个客户访问，对处理什么请求以及用什么次序处理进行管理是MySQL的任务。INSERT操作可能很耗时（特别是有很多索引需要更新时），而且它可能降低等待处理的SELECT语句的性能。如果数据检索是最重要的（通常是这样），则你可以通过在INSERT和INTO之间添加关键字LOW_PRIORITY，指示MySQL降低INSERT语句的优先级，如下所示：
```sql
INSERT LOW_PRIORITY INTO
```
顺便说一下，这也适用于下一章介绍的UPDATE和DELETE语句。

**IGNORE关键字**
如果用UPDATE语句更新多行，并且在更新这些行中的一行或多行时出一个现错误，则整个UPDATE操作被取消（错误发生前更新的所有行被恢复到它们原来的值）。为即使是发
生错误，也继续进行更新，可使用IGNORE关键字，如下所示：
```sql
UPDATE IGNORE customers…
```

**理解NULL**
不要把NULL值与空串相混淆。NULL值是没有值，它不是空串。如果指定''（两个单引号，其间没有字符），这在NOT NULL列中是允许的。空串是一个有效的值，它不是无值。NULL值用关键字NULL而不是空串指定。

**存储引擎**
* InnoDB是一个可靠的事务处理引擎（参见第26章），它不支持全文本搜索；
* MEMORY在功能等同于MyISAM，但由于数据存储在内存（不是磁盘）中，速度很快（特别适合于临时表）；
* MyISAM是一个性能极高的引擎，它支持全文本搜索（参见第18章），但不支持事务处理。


