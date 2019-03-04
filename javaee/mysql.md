
#1.建表：
创建数据库：
CREATE DATABASE IF NOT EXISTS lewis DEFAULT CHARACTER SET UTF8 COLLATE UTF8_GENERAL_CI;
删除数据库：drop database 库名

1. mysql> CREATE TABLE IF NOT EXISTS knife (id SMALLINT UNSIGNED AUTO_INCREMENT PRI
MARY KEY,name VARCHAR(32) NOT NULL,atk SMALLINT UNSIGNED NOT NULL,hit SMALLINT U
NSIGNED NOT NULL DEFAULT 20,crit SMALLINT UNSIGNED NOT NULL DEFAULT 10) DEFAULT
CHARACTER SET UTF8 COLLATE UTF8_GENERAL_CI;

2. 显示建表的信息
Desc 表名

3.为已建表添加属性
ALTER TABLE knife ADD attr VARCHAR(4) NOT NULL;

4.修改数据：(为属性添加值)
UPDATE knife SET attr='木',type='神界' WHERE name='黑风';

5.通过CREATE...SELECT来创建数据表并且同时写入记录
CREATE TABLE sword_attr (attr_id SMALLINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,attr VARCHAR(4) NOT NULL SELECT attr FROM knife GROUP BY attr;




3.插入数据（对应的字段如果使用DEFAULT 则会使用定义时的默认值）
方式一：INSERT sword VALUES(NULL,'黑风',10000,400,400);
方式二：INSERT knife SET name='洛神',atk='20000',hit=1;

4.显示表内容
SELECT*FROM sword;

5.修改字段值
ALTER TABLE knife MODIFY crit SMALLINT UNSIGNED DEFAULT 10;

6.一次插入多行
mysql> INSERT knife VALUES(
DEFAULT,'痕兮',7000,800,999),
(DEFAULT,'逐暮',100,1000,10000),(DEFAULT,'风跃',9000,10,DEFAULT);

7.MD5加密项
INSERT knife VALUES(DEFAULT,MD5('隐锋'),8000,2000,10);

8.向一张表插入来自另一张表的数据
 INSERT knife_insert(name) SELECT name FROM sword WHERE id>5;

9.**更新数据**

1. 1.更新某项值：将命中值(hit)全加1
UPDATE knife SET hit=hit+1;
1. 2.更新多项值
UPDATE knife SET atk=atk+2*crit,hit=hit-1;
1. 3.更新限定项的值
UPDATE knife SET atk=atk-20000 WHERE name='逐暮';

10.**删除DELETE**
DELETE FROM knife WHERE id=7;
（注意，删掉id为7的，不会影响其他数据的id，再插入id为9）

11.**查询**
（查询任何查询都不会影响表中的数据）
11.1.
方式一：SELECT id,name FROM knife;
方式二：SELECT knife.id,knife.name FROM knife;

11.2.查询的字段取别名
SELECT crit AS '暴击',name AS'名称' FROM knife;

11.3.分组（# **去重** #）
	1. SELECT crit FROM knife GROUP BY crit;
	(这里查询到的数据数目和排序不定，是根据什么规则?)
	2. SELECT crit FROM knife GROUP BY crit HAVING crit<500;
11.4. 降序排列
	1. SELECT * FROM knife ORDER BY id DESC;
	2. 多条记录排序(当第一个字段相同，相同的数据按第二个字段再排)
	SELECT * FROM knife ORDER BY crit,id DESC;
11.5.查询限制
	1.SELECT * FROM knife LIMIT 4;(前4条)
	2.SELECT * FROM knife LIMIT 1,4;(查询前2~4条数据,前面数字代表起始的id,不包括该条，即ID从2开始，后面的数字表示数据条数，即4条)

11.6.子查询
11.6.1.1特点：
	1、出现在其他SQL语句内的SELECT语句
	2、子查询必须在()内
	3、增删改查都可以进行子查询
	4、返回：标量，行，列或子查询
11.6.2.计算平均攻击力(四舍五入2位)
	SELECT ROUND(AVG(atk),2) AS '平均攻击力' FROM sword;
11.6.3 普通查询
 	SELECT * FROM knife WHERE atk > 5636.36 ORDER BY atk DESC;
11.6.4.使用子查询(也就是将上面两步简化为一步)
SELECT * FROM knife WHERE atk > (SELECT ROUND(AVG(atk),2) AS '平均攻击力' FROM knife) ORDER BY atk DESC;
    
	ANY     满足一条即可查询到任一项
	SOME    同ANY
	ALL     满足所有可查询到的所有项
	IN      等价于  = ANY 或 = SOME   等于查询到的子项的任意一项
11.7.上述查询限定关键字的使用
//指定的字段查询
SELECT atk FROM knife WHERE type = '人界';
//子查询同时使用关键字（any）
//查询满足子查询中列表项数据的任一项的列表
SELECT id,name,atk FROM knife WHERE atk > ANY(SELECT atk FROM knife WHERE type = '人界') ORDER BY atk DESC;

11.8.将查询的结果写入另一个数据表
INSERT sword_type (type_name) SELECT type FROM knife GROUP BY type;
11.9.使用type_id更新knife中的数据
 UPDATE knife INNER JOIN sword_type ON type = type_name SET type = type_id ;

#如果两表字段相同，这样会报错，需具体指定字段属于哪张表
UPDATE knife AS s INNER JOIN sword_attr AS a ON s.attr = a.attr SET s.attr = a.attr_id;

#修改字段属性
ALTER TABLE knife CHANGE type type_id SMALLINT UNSIGNED NOT NULL,
CHANGE attr attr_id SMALLINT UNSIGNED NOT NULL;

##内联INNER JOIN
（通过INNER JOIN内联其他表，通过目标表与关联表之间字段的关联关系及判断条件，找出所需要的值，比如这里的通过ID去查找对应的名称）
	SELECT id,name,atk,hit,type_name,attr FROM sword AS s INNER JOIN sword_type AS t ON s.type_id = t.type_id INNER JOIN sword_attr AS a ON s.attr_id = a.attr_id;

##LEFT JOIN
查询所有剑的详细信息(左外连接：左表（***这里指目
标表knife，即等号左侧***）全部，右表（**等号右侧**）无匹配则置空)
SELECT id,name,atk,hit,type_name,attr FROM knife AS s LEFT JOIN sword_type AS t ON s.type_id = t.type_id LEFT JOIN sword_attr AS a ON s.attr_id = a.attr_id;

##RIGHT JOIN
查询所有剑的详细信息(左外连接：右表全部，左表无匹配则置空)
（右表所有字段都要参与查询，已匹配的（匹配次数不限）除外，匹配不到的项至少参与查询一次并输出，对应字段查询不到值则赋值为null）
SELECT id,name,atk,hit,type_name,attr FROM knife AS s RIGHT JOIN sword_type AS t ON s.type_id = t.type_id RIGHT JOIN sword_attr AS a ON s.attr_id = a.attr_id;


**13.**常规操作
	-->插入数据：
	insert into android(type,name,localPath,jianshuUrl,juejinUrl,imgUrl,createTime)
	values (XX,XX,XX,XX,XX,XX,XX)
	
	-->更新数据：
	UPDATE android
	SET type=XX,name=XX,localPath=XX,jianshuUrl=XX,juejinUrl=XX,imgUrl=XX,createTime=XX
	WHERE id=XX
	
	-->查询所有+INNER JOIN
	<select id="findALL" resultType="toly1994.com.android_project.bean.Note">
	   SELECT a.id,name,t.type,localPath,jianshuUrl,juejinUrl,imgUrl,createTime FROM android AS a
	   INNER JOIN type AS t ON a.type = t.type_idx;
	</select>
	
	-->根据id查询
	SELECT a.id,name,t.type,localPath,jianshuUrl,juejinUrl,imgUrl,createTime FROM android AS a
	INNER JOIN type AS t ON a.type = t.type_idx
	WHERE a.id=XX
	
	-->根据type查询
	SELECT a.id,name,t.type,localPath,jianshuUrl,juejinUrl,imgUrl,createTime FROM android AS a
	INNER JOIN type AS t ON a.type = t.type_idx
	 WHERE a.type=XX;
	
	
	-->根据name部分字符查询
	SELECT a.id,name,t.type,localPath,jianshuUrl,juejinUrl,imgUrl,createTime FROM android AS a
	INNER JOIN type AS t ON a.type = t.type_idx
	WHERE name like '%XX%';
	
	-->根据id删除数据
	DELETE FROM android
	WHERE id=#{id}


CREATE TABLE if not exists user(userId INT NOT NULL PRIMARY KEY AUTO_INCREMENT,userName VARCHAR(255) NOT NULL,password VARCHAR(255) NOT NULL,phone VARCHAR(255) NOT NULL)ENGINE=INNODB DEFAULT CHARSET=utf8;