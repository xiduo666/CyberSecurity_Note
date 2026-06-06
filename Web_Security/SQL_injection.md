# MySQL--SQL注入理论基础

>**一切用户可控的输入，皆是攻击面**

## 1.注入分类
### 1.1 普通注入
* 特点：有明显特定的数据回显，以DVWA的SQL注入为例，当输入已存在的用户ID时，会回显指定用户的first_name surname。
* 常见技术：UNION注入，报错注入...

### 1.2 盲注
* 特点：没有特定数据的回显，并且其也应该依据什么样的回显选择什么样的盲注。
比如，当回显能够在正确时和错误时有不相同的回显，就先去判断**布尔盲注**；当无论正确与否，回显都相同时，尝试**时间盲注**`sleep(N)`...

## 2.简单DVWA靶场通关
### 2.1 普通注入的坑
* UNION注入时,`0' union select 1,group_concat(table_name) from information_schema.tables where table_schema='dvwa'#`这个SQL注入语句会报错，说是UNION两边类型不同，具体可以去问AI，解决方法我认为直接转换成十六进制就没问题了 `hex(group_concat(table_name)) from...`
  
### 2.2 盲注
我感觉盲注就是一个个去猜，先猜数据库个数，然后一个个猜数据库名字（一个一个字符猜），然后拿到可疑数据库，再猜里面的表数，然后一个个猜表名（一个一个字符猜），看到可疑表，再一个个猜列数和列名，发现可疑列，然后去拿数据就好了。
`0' union select user,password from dvwa.users#`

举个例子：猜dvwa数据库里面的表数
`1' and (select count(table_name) from information_schema.tables where schema_table='dvwa')=8#`


时间盲注形式上有一点不同，但原理还是猜猜猜：依旧猜dvwa数据库里面的表数
`1' and if((select count(table_name) from information_schema.tables where table_schema='dvwa')=8,sleep(5),1)#`

## 3.补充一些常用函数
* `length()`猜名字长度时使用
* `group_concat()`
* `concat_ws()`
* `count()`
* `database()`
* `version()`
  
  这里只是补充，后续还有再进行补充，具体使用问AI，即学即用