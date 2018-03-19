title: 十个实用MySQL命令及函数
author: jackcooper
date: 2018-03-19 17:12:30
tags:
---
#### 前言

今天介绍一些MySQL常用的实用命令，都是一些比较简单的命令。已经知道的朋友，就当是巩固吧，不知道的童鞋，可以好好在自己的机器上，练习下。

**0\. 显示数据库**
**命令**：`show databases`。
**作用**：列出当前数据库服务器中所有的数据库。
**例子**：

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| andyqian           |
| mydata             |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
6 rows in set (0.00 sec)
```

**1\. 切换数据库**
**命令**: `use database_name`。
**作用**: 切换数据库。
例子:

```
mysql> use andyqian;
Database changed
```

**2\. 显示数据库中的所有表**
**命令**: `show tables`。
**作用**: 显示数据库中的所有表。
**例子**:

```
mysql> show tables;
+--------------------+
| Tables_in_andyqian |
+--------------------+
| hit_counter        |
| t_base_01          |
| t_base_data        |
| t_base_user        |
+--------------------+
4 rows in set (0.00 sec)
```

**3\. 显示表中的所有列**
**命令:**`show full columns tables_name;`
**作用: **显示表中所有列信息。
例子:

```
mysql> show full columns from t_base_data\G;
*************************** 1\. row ***************************
     Field: id
      Type: bigint(20)
 Collation: NULL
      Null: NO
       Key: PRI
   Default: NULL
     Extra: auto_increment
Privileges: select,insert,update,references
   Comment: 
*************************** 2\. row ***************************
     Field: content
      Type: json
 Collation: NULL
      Null: YES
       Key: 
   Default: NULL
     Extra: 
Privileges: select,insert,update,references
   Comment: 
2 rows in set (0.01 sec)
```

**注意**：我这里使用终端直接连接数据库，`\G`为按列格式化显示。如果使用Navicat工具，则为下面这样。

![image](http://upload-images.jianshu.io/upload_images/3710706-b774b67066aed593?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**4\. 查看MySQL版本**
**命令**：`select version();` 。
**作用**：查看服务器版本。
**例子**：

```
mysql> select version();
+-----------+
| version() |
+-----------+
| 5.7.20    |
+-----------+
1 row in set (0.00 sec)
```

**注意**：查看MySQL版本是一件非常重要的事情，比如：MySQL5.6之前就不支持Online DDL，MySQL 5.7.8 之前就不支持JSON数据类型。

**5\. 查看当前用户**
**命令**：`select current_user();`
**作用**：显示当前登录用户
**例子**：

```
mysql> select current_user();
+----------------+
| current_user() |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)
```

**注意**：上面是仅仅是为了演示，在生产环境中，不建议直接使用root用户，建议的是：建一个新用户连接数据库，以及做好权限分配，至于为什么？这里就不一一展开了，下次单独拧出来写一篇文章。

**6\. 显示单表信息**
**命令**：`show table status like table_name`。
**作用**：显示表详细信息。
**例子**：

```
mysql> show table status like "t_base_data"\G;
*************************** 1\. row ***************************
           Name: t_base_data
         Engine: InnoDB
        Version: 10
     Row_format: Dynamic
           Rows: 2
 Avg_row_length: 8192
    Data_length: 16384
Max_data_length: 0
   Index_length: 0
      Data_free: 0
 Auto_increment: 3
    Create_time: 2017-12-08 00:30:03
    Update_time: NULL
     Check_time: NULL
      Collation: utf8_unicode_ci
       Checksum: NULL
 Create_options: 
        Comment: 
1 row in set (0.00 sec)
```

**注意**：该命令详细的展示了表的信息，其中包括表名，表的存储引擎，版本，数据长度，索引长度，创建时间，修改时间等等。

**7\. 显示进程数**
**命令**： `show processlist`。
**作用**：显示正在操作数据库的进程数。
**例子**：

```
mysql> show processlist\G;
*************************** 1\. row ***************************
     Id: 6
   User: root
   Host: localhost
     db: andyqian
Command: Query
   Time: 0
  State: starting
   Info: show processlist
*************************** 2\. row ***************************
     Id: 7
   User: root
   Host: localhost:46612
     db: NULL
Command: Sleep
   Time: 1439
  State: 
   Info: NULL
```

**注意**：该命令显示了所有连接数据库的进程数，其中就包括，用户,主机，连接的数据库等信息。

**8\. 显示索引**
**命令**：`show index from table_name`;
**作用**：显示表中的所有索引。
**例子**：

```
mysql> show index from t_base_data\G;
*************************** 1\. row ***************************
        Table: t_base_data
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: id
    Collation: A
  Cardinality: 2
     Sub_part: NULL
       Packed: NULL
         Null: 
   Index_type: BTREE
      Comment: 
Index_comment: 
*************************** 2\. row ***************************
        Table: t_base_data
   Non_unique: 1
     Key_name: idx_created_at
 Seq_in_index: 1
  Column_name: created_at
    Collation: A
  Cardinality: 1
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: BTREE
      Comment: 
Index_comment: 
2 rows in set (0.00 sec)
```

**注意**：这里显示了表中的索引信息，从这里就能看出建了哪些索引，联合索引的顺序。对索引不了解的童鞋，可以看看这篇文章《[写会MySQL索引](http://mp.weixin.qq.com/s?__biz=MzI3NDA4OTk1OQ==&mid=2649901492&idx=1&sn=92acb39a56deb1ac002ad44594352099&chksm=f31fbb3cc468322a4d9c74750b06a26d9744431f7120c0c0741857d349a61d7abc970e0ff74b&scene=21#wechat_redirect)》。

**9\. 查看执行计划**
**命令**：`explain 查询语句`。
**作用**：查看查询语句的执行情况，常用于SQL优化。
**例子**：

```
mysql> explain select * from t_base_user where created_at>"2017-12-09"\G;
*************************** 1\. row ***************************
           id: 1
  select_type: SIMPLE
        table: t_base_user
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 1
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)
```

**注意**: 查看SQL的执行计划是一个好习惯，无论是新手，还是老司机，都应该养成这个习惯。上面这个执行计划还读的懂吗？如果不懂，可以回顾这篇文章《[读懂MySQL执行计划](http://mp.weixin.qq.com/s?__biz=MzI2NDU3OTg5Nw==&mid=2247483733&idx=1&sn=ac33a264094a1fb0bc7d658c6eaa6297&chksm=eaab3f03dddcb615cde0ef68b23b9f648ab4d26af34e22dcf82c6686bfe8f496ad71c6f6cade&scene=21#wechat_redirect)》

#### 最后

上面列出了一些SQL命令，在实际工作中，非常实用。其实工作中用到的SQL命令远远不止这些，下次我们再写。



---
---

#### 函数  

**0\. 显示当前时间**
**命令**：`select now()`。
**作用**: 显示当前时间。
**应用场景**: 创建时间，修改时间等默认值。
**例子**：

```
mysql> select now();
+---------------------+
| now()               |
+---------------------+
| 2017-12-27 20:14:56 |
+---------------------+
1 row in set (0.00 sec)
```

**1\. 字符长度**
**命令**：`select char_length('andyqan')`。
**作用**: 显示指定字符长度。
**应用场景**: 查看字符长度时。
**例子**：

```
mysql> select char_length('andyqian');
+-------------------------+
| char_length('andyqian') |
+-------------------------+
|                       8 |
+-------------------------+
1 row in set (0.00 sec)
```

**2\. 日期格式化**
**命令**：`select date_format(now(),'%y-%m-%d)`。
**作用**：格式化日期。
**应用场景**：格式化日期时。
**例子**：

```
mysql> select date_format(now(),'%y-%m-%d');
+-------------------------------+
| date_format(now(),'%y-%m-%d') |
+-------------------------------+
| 17-12-28                      |
+-------------------------------+
1 row in set (0.00 sec)
```

这里支持的格式有:
%y：表示年(两位数)，例如: 17 年。
%Y：表示4位数中的年，例如: 2017年
%m：表示月(1-12)
%d: 表示月中的天
%H: 小时(0-23)
%i: 分钟 (0-59)
%s: 秒 (0-59)

年月日时分秒: %y-%m-%d %H:%i:%s，
如下所示：

```
mysql> select DATE_FORMAT(now(),'%y-%m-%d %H:%i:%s');
+----------------------------------------+
| DATE_FORMAT(now(),'%y-%m-%d %H:%i:%s') |
+----------------------------------------+
| 17-12-27 20:28:54                      |
+----------------------------------------+
1 row in set (0.00 sec)
```

**3\. 添加/减少日期时间**
**命令**：
`DATE_ADD(date,interval expr unit)`
`DATE_SUB(date,interval expr unit)`
**作用**: 增加/减少日期时间
**应用场景**：当前时间的前一天，前几分钟。 常用于数据统计。
**例子**：

```
mysql> select date_add(now(),interval 1 day);
+--------------------------------+
| date_add(now(),interval 1 day) |
+--------------------------------+
| 2017-12-28 20:10:17            |
+--------------------------------+
1 row in set (0.00 sec)
```

其中Date表示日期格式，其中就包括: 如
2017-12-27，now() 等格式。
expr：表示数量。
unit：表示单位，支持毫秒(microsecond)，秒(second)，小时(hour)，天(day)，周(week)，年(year)等。

**4\. 类型转换**
**命令**： `CAST(expr AS type)`
**作用**: 主要用于显示类型转换
**应用场景**：显示类型转换
**例子**：

```
mysql> select cast(18700000000 as char);
+---------------------------+
| cast(18700000000 as char) |
+---------------------------+
| 18700000000               |
+---------------------------+
1 row in set (0.00 sec)
```

需要注意的是，其中type支持的不是全部基本数据类型，支持的详细类型，请参考之前的文章《[谈谈MySQL显示类型转换](http://mp.weixin.qq.com/s?__biz=MzI2NDU3OTg5Nw==&mid=2247483874&idx=1&sn=65a1bf8c3ec2b2cdcfba6b77a86e3930&chksm=eaab3fb4dddcb6a20f97e303a4d7a51fef6bc3ff6b4bb6eb345d6ce3df20d90cd042f392a11c&scene=21#wechat_redirect)》。

**5\. 加密函数**
**命令**： `md5(data)`
**作用**: 用于加密数据
**应用场景**：加密，一些隐私数据，例如银行卡号，身份证等需要存储密文，(当然，不建议使用数据库层加密，应该在应用层加密)
**例子**：

```
mysql> select md5("andyqian");
+----------------------------------+
| md5("andyqian")                  |
+----------------------------------+
| 8a6f60827608e7f1ae29d1abcecffc3a |
+----------------------------------+
1 row in set (0.00 sec)
```

注意事项: 如果目前你们数据库中的数据还是明文，此时可以使用数据库加密算法进行加密。

例如: (仅做演示):

```
update t_base_user set name=md5(name),updated_time=now() where id=1;
```

支持的加密函数有:

1.  md5()

2.  des_encrypt(加密) / des_decrypt(解密);

3.  sha1()

4.  password() 等等

这里不再一一介绍，有兴趣的同学，可以移步至官网进行详细了解。

**6\. 字符串连接**
**命令**： `concat(str,str2,str3)`
**作用**：拼接字符串
**应用场景**：拼接字符串，例如在某些字段上同一加上指定字符串。
**例子**：

```
mysql> select concat("andy","qian");
+-----------------------+
| concat("andy","qian") |
+-----------------------+
| andyqian              |
+-----------------------+
1 row in set (0.00 sec)
```

该函数平时也还是用的比较多的，基本上场景是，在某些数据上，加上特定的字符串。方法如下:

![image](http://upload-images.jianshu.io/upload_images/3710706-6779a591453af123?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**7\. JSON函数(5.7版本才支持)**

**命令**：`json_object(函数)`
**作用**：转换json字符串
**应用场景**：指定数据转换json字符串
**例子**：

```
mysql> select json_object("name","andyqian","database","MySQL");
+---------------------------------------------------+
| json_object("name","andyqian","database","MySQL") |
+---------------------------------------------------+
| {"name": "andyqian", "database": "MySQL"}         |
+---------------------------------------------------+
1 row in set (0.00 sec)
```

其中包括json_array:

```
mysql> select json_array("name","andyqian","database","MySQL");
+--------------------------------------------------+
| json_array("name","andyqian","database","MySQL") |
+--------------------------------------------------+
| ["name", "andyqian", "database", "MySQL"]        |
+--------------------------------------------------+
1 row in set (0.00 sec)
```

判断是否为json字符串的json_valid():

```
select json_valid('{"name": "andyqian", "database": "MySQL"}');
```

其中为有效json字符串时为1。
无效json字符串时为0.
还有很多方法，不再一一演示。

**8\. 聚合函数**
**命令**： `sum()，count()，avg()，max()，min()`
**作用**：统计，求平均值，最大，最小值
**应用场景**：这类函数非常常见，主要用于数据统计，在SQL优化方面，也适用。
**例子**：

```
mysql> select max(id) from t_base_user;
+---------+
| max(id) |
+---------+
|       2 |
+---------+
1 row in set (0.00 sec)
```

这里有个小技巧，如果主键是有序递增的，当需要有多少用户时，可以使用max(id)来代替count(*)函数。

**9\. distinct()**
**命令**： `distinct`
**作用**：去重
**应用场景**：需要统计类型，状态，计算区分度时。
**例子**：

```
mysql> select count(distinct(name))/count(*) from t_base_user;
+--------------------------------+
| count(distinct(name))/count(*) |
+--------------------------------+
|                         0.6667 |
+--------------------------------+
1 row in set (0.00 sec)
```

上述为计算区分度的例子，如有不明白的，可以参考之前的文章《[写会MySQL索引](http://mp.weixin.qq.com/s?__biz=MzI3NDA4OTk1OQ==&mid=2649901492&idx=1&sn=92acb39a56deb1ac002ad44594352099&chksm=f31fbb3cc468322a4d9c74750b06a26d9744431f7120c0c0741857d349a61d7abc970e0ff74b&scene=21#wechat_redirect)》

---

#### 附录（mysql 函数大全）：
**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">一、数学函数</font>** ABS(x)   返回x的绝对值
BIN(x)   返回x的二进制（OCT返回八进制，HEX返回十六进制）
CEILING(x)   返回大于x的最小整数值
EXP(x)   返回值e（自然对数的底）的x次方
FLOOR(x)   返回小于x的最大整数值
GREATEST(x1,x2,...,xn)返回集合中最大的值
LEAST(x1,x2,...,xn)      返回集合中最小的值
LN(x)                    返回x的自然对数
LOG(x,y)返回x的以y为底的对数
MOD(x,y)                 返回x/y的模（余数）
PI()返回pi的值（圆周率）
RAND()返回０到１内的随机值,可以通过提供一个参数(种子)使RAND()随机数生成器生成一个指定的值。
ROUND(x,y)返回参数x的四舍五入的有y位小数的值
SIGN(x) 返回代表数字x的符号的值
SQRT(x) 返回一个数的平方根
TRUNCATE(x,y)            返回数字x截短为y位小数的结果

**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">二、聚合函数(常用于GROUP BY从句的SELECT查询中)</font>** AVG(col)返回指定列的平均值
COUNT(col)返回指定列中非NULL值的个数
MIN(col)返回指定列的最小值
MAX(col)返回指定列的最大值
SUM(col)返回指定列的所有值之和
GROUP_CONCAT(col) 返回由属于一组的列值连接组合而成的结果

**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">三、字符串函数</font>**
ASCII(char)返回字符的ASCII码值
BIT_LENGTH(str)返回字符串的比特长度
CONCAT(s1,s2...,sn)将s1,s2...,sn连接成字符串
CONCAT_WS(sep,s1,s2...,sn)将s1,s2...,sn连接成字符串，并用sep字符间隔
INSERT(str,x,y,instr) 将字符串str从第x位置开始，y个字符长的子串替换为字符串instr，返回结果
FIND_IN_SET(str,list)分析逗号分隔的list列表，如果发现str，返回str在list中的位置
LCASE(str)或LOWER(str) 返回将字符串str中所有字符改变为小写后的结果
LEFT(str,x)返回字符串str中最左边的x个字符
LENGTH(s)返回字符串str中的字符数
LTRIM(str) 从字符串str中切掉开头的空格
POSITION(substr,str) 返回子串substr在字符串str中第一次出现的位置
QUOTE(str) 用反斜杠转义str中的单引号
REPEAT(str,srchstr,rplcstr)返回字符串str重复x次的结果
REVERSE(str) 返回颠倒字符串str的结果
RIGHT(str,x) 返回字符串str中最右边的x个字符
RTRIM(str) 返回字符串str尾部的空格
STRCMP(s1,s2)比较字符串s1和s2
TRIM(str)去除字符串首部和尾部的所有空格
UCASE(str)或UPPER(str) 返回将字符串str中所有字符转变为大写后的结果

**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">四、日期和时间函数</font>**
CURDATE()或CURRENT_DATE() 返回当前的日期
CURTIME()或CURRENT_TIME() 返回当前的时间
DATE_ADD(date,INTERVAL int keyword)返回日期date加上间隔时间int的结果(int必须按照关键字进行格式化),如：SELECTDATE_ADD(CURRENT_DATE,INTERVAL 6 MONTH);
DATE_FORMAT(date,fmt)  依照指定的fmt格式格式化日期date值
DATE_SUB(date,INTERVAL int keyword)返回日期date加上间隔时间int的结果(int必须按照关键字进行格式化),如：SELECTDATE_SUB(CURRENT_DATE,INTERVAL 6 MONTH);
DAYOFWEEK(date)   返回date所代表的一星期中的第几天(1~7)
DAYOFMONTH(date)  返回date是一个月的第几天(1~31)
DAYOFYEAR(date)   返回date是一年的第几天(1~366)
DAYNAME(date)   返回date的星期名，如：SELECT DAYNAME(CURRENT_DATE);
FROM_UNIXTIME(ts,fmt)  根据指定的fmt格式，格式化UNIX时间戳ts
HOUR(time)   返回time的小时值(0~23)
MINUTE(time)   返回time的分钟值(0~59)
MONTH(date)   返回date的月份值(1~12)
MONTHNAME(date)   返回date的月份名，如：SELECT MONTHNAME(CURRENT_DATE);
NOW()    返回当前的日期和时间
QUARTER(date)   返回date在一年中的季度(1~4)，如SELECT QUARTER(CURRENT_DATE);
WEEK(date)   返回日期date为一年中第几周(0~53)
YEAR(date)   返回日期date的年份(1000~9999)
一些示例：

<a data="60664" class="copybut" id="copybut60664" onclick="doCopy('code60664')" style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; color: rgb(51, 51, 51); text-decoration: none; cursor: pointer;"><u style="font-family: &quot;Microsoft Yahei&quot;, sans-serif;">复制代码</u></a>

代码如下:

获取当前系统时间：SELECT FROM_UNIXTIME(UNIX_TIMESTAMP());
SELECT EXTRACT(YEAR_MONTH FROM CURRENT_DATE);
SELECT EXTRACT(DAY_SECOND FROM CURRENT_DATE);
SELECT EXTRACT(HOUR_MINUTE FROM CURRENT_DATE);

返回两个日期值之间的差值(月数)：SELECT PERIOD_DIFF(200302,199802);

**在Mysql中计算年龄：**
SELECT DATE_FORMAT(FROM_DAYS(TO_DAYS(NOW())-TO_DAYS(birthday)),'%Y')+0 AS age FROM employee;
这样，如果Brithday是未来的年月日的话，计算结果为0。
下面的SQL语句计算员工的绝对年龄，即当Birthday是未来的日期时，将得到负值。
SELECT DATE_FORMAT(NOW(), '%Y') - DATE_FORMAT(birthday, '%Y') -(DATE_FORMAT(NOW(), '00-%m-%d') <DATE_FORMAT(birthday, '00-%m-%d')) AS age from employee

**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">五、加密函数</font>**
AES_ENCRYPT(str,key)  返回用密钥key对字符串str利用高级加密标准算法加密后的结果，调用AES_ENCRYPT的结果是一个二进制字符串，以BLOB类型存储
AES_DECRYPT(str,key)  返回用密钥key对字符串str利用高级加密标准算法解密后的结果
DECODE(str,key)   使用key作为密钥解密加密字符串str
ENCRYPT(str,salt)   使用UNIXcrypt()函数，用关键词salt(一个可以惟一确定口令的字符串，就像钥匙一样)加密字符串str
ENCODE(str,key)   使用key作为密钥加密字符串str，调用ENCODE()的结果是一个二进制字符串，它以BLOB类型存储
MD5()    计算字符串str的MD5校验和
PASSWORD(str)   返回字符串str的加密版本，这个加密过程是不可逆转的，和UNIX密码加密过程使用不同的算法。
SHA()    计算字符串str的安全散列算法(SHA)校验和
**示例：** 

<a data="52378" class="copybut" id="copybut52378" onclick="doCopy('code52378')" style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; color: rgb(51, 51, 51); text-decoration: none; cursor: pointer;"><u style="font-family: &quot;Microsoft Yahei&quot;, sans-serif;">复制代码</u></a>

代码如下:

SELECT ENCRYPT('root','salt');
SELECT ENCODE('xufeng','key');
SELECT DECODE(ENCODE('xufeng','key'),'key');#加解密放在一起
SELECT AES_ENCRYPT('root','key');
SELECT AES_DECRYPT(AES_ENCRYPT('root','key'),'key');
SELECT MD5('123456');
SELECT SHA('123456');

**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">六、控制流函数</font>**
MySQL有4个函数是用来进行条件操作的，这些函数可以实现SQL的条件逻辑，允许开发者将一些应用程序业务逻辑转换到数据库后台。

**MySQL控制流函数：** CASE WHEN[test1] THEN [result1]...ELSE [default] END如果testN是真，则返回resultN，否则返回default
CASE [test] WHEN[val1] THEN [result]...ELSE [default]END  如果test和valN相等，则返回resultN，否则返回default
IF(test,t,f)   如果test是真，返回t；否则返回f
IFNULL(arg1,arg2) 如果arg1不是空，返回arg1，否则返回arg2
NULLIF(arg1,arg2) 如果arg1=arg2返回NULL；否则返回arg1
这些函数的第一个是IFNULL()，它有两个参数，并且对第一个参数进行判断。如果第一个参数不是NULL，函数就会向调用者返回第一个参数；如果是NULL,将返回第二个参数。

如：SELECT IFNULL(1,2), IFNULL(NULL,10),IFNULL(4*NULL,'false');
NULLIF()函数将会检验提供的两个参数是否相等，如果相等，则返回NULL，如果不相等，就返回第一个参数。

如：SELECT NULLIF(1,1),NULLIF('A','B'),NULLIF(2+3,4+1);
和许多脚本语言提供的IF()函数一样，MySQL的IF()函数也可以建立一个简单的条件测试，这个函数有三个参数，第一个是要被判断的表达式，如果表达式为真，IF()将会返回第二个参数，如果为假，IF()将会返回第三个参数。

如：SELECTIF(1<10,2,3),IF(56>100,'true','false');
IF()函数在只有两种可能结果时才适合使用。然而，在现实世界中，我们可能发现在条件测试中会需要多个分支。在这种情况下，MySQL提供了CASE函数，它和PHP及Perl语言的switch-case条件例程一样。
CASE函数的格式有些复杂，通常如下所示：

<a data="30351" class="copybut" id="copybut30351" onclick="doCopy('code30351')" style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; color: rgb(51, 51, 51); text-decoration: none; cursor: pointer;"><u style="font-family: &quot;Microsoft Yahei&quot;, sans-serif;">复制代码</u></a>

代码如下:

CASE [expression to be evaluated]
WHEN [val 1] THEN [result 1]
WHEN [val 2] THEN [result 2]
WHEN [val 3] THEN [result 3]
......
WHEN [val n] THEN [result n]
ELSE [default result]
END

这里，第一个参数是要被判断的值或表达式，接下来的是一系列的WHEN-THEN块，每一块的第一个参数指定要比较的值，如果为真，就返回结果。所有的WHEN-THEN块将以ELSE块结束，当END结束了所有外部的CASE块时，如果前面的每一个块都不匹配就会返回ELSE块指定的默认结果。如果没有指定ELSE块，而且所有的WHEN-THEN比较都不是真，MySQL将会返回NULL。
CASE函数还有另外一种句法，有时使用起来非常方便，如下：
**CASE**

<a data="69872" class="copybut" id="copybut69872" onclick="doCopy('code69872')" style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; color: rgb(51, 51, 51); text-decoration: none; cursor: pointer;"><u style="font-family: &quot;Microsoft Yahei&quot;, sans-serif;">复制代码</u></a>

代码如下:

WHEN [conditional test 1] THEN [result 1]
WHEN [conditional test 2] THEN [result 2]
ELSE [default result]
END

这种条件下，返回的结果取决于相应的条件测试是否为真。
**示例：** 

<a data="51309" class="copybut" id="copybut51309" onclick="doCopy('code51309')" style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; color: rgb(51, 51, 51); text-decoration: none; cursor: pointer;"><u style="font-family: &quot;Microsoft Yahei&quot;, sans-serif;">复制代码</u></a>

代码如下:

mysql>SELECT CASE 'green'
     WHEN 'red' THEN 'stop'
     WHEN 'green' THEN 'go' END;
SELECT CASE 9 WHEN 1 THEN 'a' WHEN 2 THEN 'b' ELSE 'N/A' END;
SELECT CASE WHEN (2+2)=4 THEN 'OK' WHEN(2+2)<>4 THEN 'not OK' END ASSTATUS;
SELECT Name,IF((IsActive = 1),'已激活','未激活') AS RESULT FROMUserLoginInfo;
SELECT fname,lname,(math+sci+lit) AS total,
CASE WHEN (math+sci+lit) < 50 THEN 'D'
WHEN (math+sci+lit) BETWEEN 50 AND 150 THEN 'C'
WHEN (math+sci+lit) BETWEEN 151 AND 250 THEN 'B'
ELSE 'A' END
AS grade FROM marks;
SELECT IF(ENCRYPT('sue','ts')=upass,'allow','deny') AS LoginResultFROM users WHERE uname = 'sue';#一个登陆验证

**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">七、格式化函数</font>**
DATE_FORMAT(date,fmt)  依照字符串fmt格式化日期date值
FORMAT(x,y)   把x格式化为以逗号隔开的数字序列，y是结果的小数位数
INET_ATON(ip)   返回IP地址的数字表示
INET_NTOA(num)   返回数字所代表的IP地址
TIME_FORMAT(time,fmt)  依照字符串fmt格式化时间time值
其中最简单的是FORMAT()函数，它可以把大的数值格式化为以逗号间隔的易读的序列。
**示例：** 

<a data="33556" class="copybut" id="copybut33556" onclick="doCopy('code33556')" style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; color: rgb(51, 51, 51); text-decoration: none; cursor: pointer;"><u style="font-family: &quot;Microsoft Yahei&quot;, sans-serif;">复制代码</u></a>

代码如下:

SELECT FORMAT(34234.34323432,3);
SELECT DATE_FORMAT(NOW(),'%W,%D %M %Y %r');
SELECT DATE_FORMAT(NOW(),'%Y-%m-%d');
SELECT DATE_FORMAT(19990330,'%Y-%m-%d');
SELECT DATE_FORMAT(NOW(),'%h:%i %p');
SELECT INET_ATON('10.122.89.47');
SELECT INET_NTOA(175790383);

**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">八、类型转化函数</font>**
为了进行数据类型转化，MySQL提供了CAST()函数，它可以把一个值转化为指定的数据类型。类型有：BINARY,CHAR,DATE,TIME,DATETIME,SIGNED,UNSIGNED
**示例：**

<a data="60259" class="copybut" id="copybut60259" onclick="doCopy('code60259')" style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; color: rgb(51, 51, 51); text-decoration: none; cursor: pointer;"><u style="font-family: &quot;Microsoft Yahei&quot;, sans-serif;">复制代码</u></a>

代码如下:

SELECT CAST(NOW() AS SIGNED INTEGER),CURDATE()+0;
SELECT 'f'=BINARY 'F','f'=CAST('F' AS BINARY);

**<font style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; line-height: 25.2px; font-size: 14px; color: rgb(255, 0, 0);">九、系统信息函数</font>**
DATABASE()   返回当前数据库名
BENCHMARK(count,expr)  将表达式expr重复运行count次
CONNECTION_ID()   返回当前客户的连接ID
FOUND_ROWS()   返回最后一个SELECT查询进行检索的总行数
USER()或SYSTEM_USER()  返回当前登陆用户名
VERSION()   返回MySQL服务器的版本
**示例：**

<a data="80149" class="copybut" id="copybut80149" onclick="doCopy('code80149')" style="font-family: &quot;Microsoft Yahei&quot;, sans-serif; color: rgb(51, 51, 51); text-decoration: none; cursor: pointer;"><u style="font-family: &quot;Microsoft Yahei&quot;, sans-serif;">复制代码</u></a>

代码如下:

SELECT DATABASE(),VERSION(),USER();
SELECTBENCHMARK(9999999,LOG(RAND()*PI()));#该例中,MySQL计算LOG(RAND()*PI())表达式9999999次。
