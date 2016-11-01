---
layout: post
title:  "MySQL数据库sql语法不定时更新"
date:   2015-8-20 8:34:03 +0800
categories: jekyll update
---

//普通查询语句格式

>select *from 主表 left join 次 on 主表.字段I=次表.字段D where 主表.字段x=变量1 and 次表.字段x2 like '%变量2%';

//二表临时左连接查找提取模板样式

>select *from product left join product_type on product.pro_id=product_type.protype_id;  
select *from 表名 where 段名 between $x and $x1;  
select *from 表名 order by 段名 desc或asc;

//and语法

>select *from 表名 where 段名1=info and 字段 like '%$info2%'      

//group by语法

>Select Dept,Count(User_name) From Ec_uses Where Score>89 Group By Dept  

//having语法

>SELECT dept,COUNT(user_name) count_tmp FROM ec_uses GROUP BY dept Having score>1      

//模糊查询

>select *from 表名 where 段名 like '%x%' / 'x%' / '_x%'
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;//_单 %多          

//limit语法

>select *from 表名 limit 3
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;//前三条  
select *from 表名 limit 1,3
&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;//从第二条到第四条       

//order by语法

>select *from 表名 order by 段名 desc limit 2,5;			               

//表起别名

>select *from 表名 别名						                                 

//字段起别名

>select 段名 as 段别名, 段名 as 段别名, 段名 as 段别名 from 表名;              

//特例

>1、 "select y.*,b.bumen_name from yuangong as y inner join bumen as b on y.bumen_id=b.id  where b.bumen_name='{$bumen_name}'"  
>2、 "select password from user where username='{$username}'"            

//insert语法

>insert into 表名(段名1，段名2、、、、) value('变量','字符串',null,1,0,、、、、);  
格式一  
$sql="insert into product values(null,'$a','$b','$c','15/6/7')";  
格式二  
$sql="insert into product(pro_id,pro_name,protype_id,price,pinpai,chandi)";  
	$sql.="values('$b','$c','$d','$e','$f','$g')";  
	$result=mysql_query($sql);  
格式三  
	"insert into user values(null,'{$username}','{$password}','{$email}','{$hobby}','{$phone}','{$add_time}' )"                

//update语法

>表名  set 结果变换 where 位置[定取行];  
//变种一  
	$sql="update product set nam='$a',num='$b',cho='$c' where ID='$xiug'";
	$result=mysql_query($sql);

//delete语法

>delete from 表名 where 段名 in(id1,id2,id3);  
delete from 表名 where 位置[定取行];

//摒弃自定义数据库

>drop database 库名;             

//摒弃自定义页表

>drop table 叶表名
?>

//创建数据库用户

>mysql> insert into mysql.user(Host,User,Password) values(“localhost”,”用户名”,password(“密码”));  
//刷新系统权限表  
mysql>flush privileges;

//授权xxxx用户拥有数据库的所有权限。

>mysql>grant all privileges on 数据库名.* to xxxx@localhost identified by ‘xxxx密码';  
//刷新系统权限表  
mysql>flush privileges;          

//对数据库用户进行部分授权

>mysql>grant select,update on cplusplusDB.* to cplusplus@localhost identified by ‘cplusplus.me';
//刷新系统权限表。  
mysql>flush privileges;           

//删除数据库用户

>mysql>DELETE FROM user WHERE User=”cplusplus” and Host=”localhost”;  
//刷新系统权限表。  
mysql>flush privileges;          

//修改数据库用户的密码

>mysql>update mysql.user set password=password(‘新密码’) where User=”cplusplus” and Host=”localhost”;  
//刷新系统权限表。  
mysql>flush privileges;