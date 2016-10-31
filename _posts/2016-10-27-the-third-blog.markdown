---
layout: post
title:  "php中mysqli和mysql区别"
date:   2016-10-27 8:34:03 +0800
categories: jekyll update
---

一、mysql与mysqli的概念相关：

>1、php-mysql是 PHP 操作 MySQL 数据库最原始的 Extension
&#160;&#160;&#160;&#160;&#160;php-mysqli 的 i 代表 Improvement ，相当于php-mysql的进阶，就 Extension 而言，本身也增加了安全性。
2、在php5版本之前，一般是用mysql函数去驱动MySQL数据库的，比如mysql_query()的函数，属于面向过程。
3、在php5版本以后，增加了mysqli的函数功能，某种意义上讲，它是mysql系统函数的增强版，更稳定更高效更安全，与mysql_query()对应的是mysqli_query()，属于面向对象，用对象的方式来操作和驱动MySQL数据库
4、其结果可以看作mysql与mysqli都是php方面的函数集。而就本质上与MySQL数据库关联不大。

二、mysql与mysqli的区别：

>1、mysql是非持继连接函数，mysql每次链接都会打开一个连接的进程。
2、mysqli是永远连接函数，mysqli多次运行mysqli将使用同一连接进程,从而减少了服务器的开销。mysqli封装了诸如事务等一些高级操作，同时封装了DB操作过程中的很多可用的方法，而mysql则没有这些，都需要通过myql_query()来执行。
3、mysql_fetch_row【、assoc、array】() 及产生的结果是索引数组、关联数组、混合数组；不过如果括号内的资源值为空，该函数产生的值为 bool(false)。
&#160;&#160;&#160;&#160;&#160;mysqli_fetch_row【、assoc、array】() 及产生的结果是索引数组、关联数组、混合数组；不过如果括号内的对象数组值为空，该函数产生的值为 NULL。
4、mysqli 每次执行 mysqli_query 都需要[连接标识]；
5、select 语句：
&#160;&#160;&#160;&#160;&#160;&#160;mysql  中select 语句，获取的结果是资源；
&#160;&#160;&#160;&#160;&#160;&#160;mysqli 中select 语句，会产生对象数组，即使搜索不到，也会产生对象数组。

三、mysql与mysqli的用法区别：

１:mysql（过程方式）：

>//建立连接
 	$link=mysql_connect("数据库地址","管理员名称","管理员密码")or die("数据库连接失败:".header("refresh:3;xxxx.php").mysql_error());
mysql_select_db("数据库名",$link);
mysql_query("set names utf8");//设定字符集
//选择数据库　　
$result=mysql_query('select * from data_base');//第二个可选参数，指定打开的连接　　
$row=mysql_fetch_row( $result ) ) //只取一行数据　　
echo $row[0]; //输出第一个字段的值　　

2、mysqli(对象方式)：　　

>$mysqli_obj=mysqli_connect("数据库地址","管理员名称","管理员密码","数据库名")or die("数据库连接失败:".header("refresh:3;xxxx.php").mysqli_error());//要使用new操作符，最后一个参数是直接指定数据库
//假如构造时候不指定，那下一句需要 $mysqli_obj->select_db('data_base')实现
mysqli_query($link,"set names utf8");
$result=$mysqli_obj -> query( 'select * from data_base' );　　
$row=$result -> fetch_row(); //取一行数据　　
echo row[0]; //输出第一个字段的值

####使用new mysqli('localhost', usenamer', 'password', 'databasename');会报错，提示如下：
####Fatal error: Class 'mysqli' not found in ...

####PS：<font color=#DC143C>mysqli支持以过程式的方式操作，不过有些函数必须指定资源，</font>比如mysqli_query(资源标识,SQL语句),并且资源标识的参数是放在前面的，而mysql_query(SQL语句,'资源标识')的资源标识是可选的，默认值是上一个打开的连接或资源。


一般是mysqli是没有开启的，因为mysqli类不是默认开启的，win下要改php.ini,去掉php_mysqli.dll前的,linux下要把mysqli编译进去。

四、mysql与mysqli在事务上的区别

1、mysql事务示例如下：

>$conn = mysql_connect('localhost','root','');
mysql_select_db('DB_Lib2Test');
mysql_query('start transaction');
//mysql_query('SET autocommit=0');
mysql_query($sql1);
mysql_query($sql2);
if (mysql_errno()) {
 mysql_query('rollback');
 echo 'err';
} else {
 mysql_query('commit');
 echo 'ok';
}

2、mysqli事务实例如下：

>$mysqli = new mysqli('localhost','root','','DB_Lib2Test');
$mysqli->autocommit(false);//开始事物
$mysqli->query($sql1);
$mysqli->query($sql2);
if(!$mysqli->errno){
  $mysqli->commit();
  echo 'ok';
}else{
 echo 'err';
  $mysqli->rollback();
}

由上可以看出，php-mysql并没有封装MySQL事务方法，php-mysqli则对事务进行了封装。这其中更多的原因在于MySQL数据库软件的迭代，随着MySQL不断的完善，php-mysql正在被逐步淘汰。