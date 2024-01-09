# MySQL 递归查询

## 例


![微信图片_20181212141515.jpg | center | 100x128.9840637450199](https://cdn.nlark.com/yuque/0/2018/jpeg/159888/1544595393207-9106cd5b-e642-4e2c-ba01-8313536747db.jpeg "")


```sql
DROP TABLE IF EXISTS `treenodes`;
CREATE TABLE `treenodes` (
  `id` int(11) NOT NULL,
  `nodename` varchar(20) DEFAULT NULL,
  `pid` int(11) DEFAULT NULL
--  ,PRIMARY KEY (`id`) -- 支持没有主键
) ENGINE=InnoDB ;


INSERT INTO `treenodes` VALUES ('1', 'A', '0');
INSERT INTO `treenodes` VALUES ('2', 'B', '1');
INSERT INTO `treenodes` VALUES ('3', 'C', '1');
INSERT INTO `treenodes` VALUES ('4', 'D', '2');
INSERT INTO `treenodes` VALUES ('5', 'E', '2');
INSERT INTO `treenodes` VALUES ('6', 'F', '3');
INSERT INTO `treenodes` VALUES ('7', 'G', '6');
INSERT INTO `treenodes` VALUES ('8', 'H', '0');
INSERT INTO `treenodes` VALUES ('9', 'I', '8');
INSERT INTO `treenodes` VALUES ('10', 'J', '8');
INSERT INTO `treenodes` VALUES ('11', 'K', '8');
INSERT INTO `treenodes` VALUES ('12', 'L', '9');
INSERT INTO `treenodes` VALUES ('13', 'M', '9');
INSERT INTO `treenodes` VALUES ('14', 'N', '12');
INSERT INTO `treenodes` VALUES ('15', 'O', '12');
INSERT INTO `treenodes` VALUES ('16', 'P', '15');
INSERT INTO `treenodes` VALUES ('17', 'Q', '15');
-- 测试环形
-- INSERT INTO `treenodes` VALUES ('15', 'O', '13');
-- INSERT INTO `treenodes` VALUES ('17', 'Q', '16');

delimiter //
CREATE FUNCTION `getParentList`(rootId INT)
RETURNS varchar(1000)

BEGIN
  DECLARE sTemp VARCHAR(1000);
  DECLARE sTempPar VARCHAR(1000);
  SET sTemp = '';
  SET sTempPar = rootId;

  #循环递归
  WHILE sTempPar is not null DO
  #判断是否是第一个, 不加的话第一个会为空
  IF sTemp != '' THEN
    SET sTemp = concat(sTemp, ',', sTempPar);
  ELSE
    SET sTemp = sTempPar;
  END IF;

  SET sTemp = concat(sTemp, ',', sTempPar);
  SELECT group_concat(pid) INTO sTempPar FROM treenodes where pid <> id and FIND_IN_SET(id, sTempPar) > 0;
  END WHILE;

  RETURN sTemp;
END
//

select *
from treenodes
where FIND_IN_SET(id, getParentList(17));
# ----------------------------------------------

delimiter //
CREATE FUNCTION `getChildList`(rootId INT)
RETURNS varchar(1000)

BEGIN
  DECLARE sTemp VARCHAR(1000);
  DECLARE sTempChd VARCHAR(1000);

  SET sTemp = '$';
  SET sTempChd = cast(rootId as CHAR);

  WHILE sTempChd is not null DO
  SET sTemp = concat(sTemp, ',', sTempChd);
  SELECT group_concat(id) INTO sTempChd FROM treeNodes where FIND_IN_SET(pid, sTempChd) > 0;
  END WHILE;
  RETURN sTemp;
END
//

select *
from treenodes
where FIND_IN_SET(id, getChildList(0));

```

## 使用

```sql
CREATE TABLE `t_system_table_relation` (
  `tab_from` varchar(128) NOT NULL COMMENT '数据来源表',
  `tab_to` varchar(128) NOT NULL COMMENT '数据目标表',
  `depth` int(3) NOT NULL DEFAULT '-1' COMMENT '来源表与目标表的长度',
  `sql_id` varchar(255) NOT NULL DEFAULT '' COMMENT '表关系所属脚本,当前关系可能存在于多个脚本中',
  `descr` varchar(255) DEFAULT '' COMMENT '描述',
  UNIQUE KEY `idx_tab_from_to` (`tab_from`,`tab_to`,`sql_id`) USING BTREE COMMENT '两个表间关系只有一个'
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```


```sql

```



[https://blog.csdn.net/xiaodingdou/article/details/53286503](https://blog.csdn.net/xiaodingdou/article/details/53286503)
