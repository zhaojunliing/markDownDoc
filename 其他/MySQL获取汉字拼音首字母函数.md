# MySQL获取汉字拼音首字母函数
id: mysql-pinyin
title: MySQL获取汉字拼音首字母函数
date: 2014-01-25 21:49:05
tags: [MySQL,DB]
---
功能：支持多个汉字
用法：SELECT PINYIN('单身不是狗');
+----------------------+
| PINYIN('单身不是狗') |
+----------------------+
| DSBSG                |
+----------------------+
1 row in set

注意：数据库的字符编码集得是UTF8或GBK才行。
<!-- more -->
## 第一步、创建表
``` sql
DROP TABLE IF EXISTS `pinyin`;
CREATE TABLE `pinyin` (
  `letter` char(1) NOT NULL,
  `chinese` char(1) NOT NULL,
  PRIMARY KEY  (`letter`)
) ENGINE=MyISAM DEFAULT CHARSET=gbk;
```

## 第二步、插入拼音数据
``` sql
INSERT INTO `pinyin` VALUES
('A','驁'),
('B','簿'),
('C','錯'),
('D','鵽'),
('E','樲'),
('F','鰒'),
('G','腂'),
('H','夻'),
('J','攈'),
('K','穒'),
('L','鱳'),
('M','旀'),
('N','桛'),
('O','漚'),
('P','曝'),
('Q','囕'),
('R','鶸'),
('S','蜶'),
('T','籜'),
('W','鶩'),
('X','鑂'),
('Y','韻'),
('Z','咗');
```

## 第三步、创建函数
``` sql
DELIMITER $$
 
DROP FUNCTION IF EXISTS `PINYIN`$$
 
CREATE FUNCTION `PINYIN`(str CHAR(255)) RETURNS char(255)
BEGIN
DECLARE hexCode char(4);
DECLARE pinyin varchar(255);
DECLARE firstChar char(1);
DECLARE aChar char(1);
DECLARE pos int;
DECLARE strLength int;
 
SET pinyin    = '';
SET strLength = CHAR_LENGTH(LTRIM(RTRIM(str)));
SET pos       = 1;
SET @str      = (CONVERT(str USING gbk));
WHILE pos <= strLength DO
	SET @aChar = SUBSTRING(@str,pos,1);
	SET hexCode = HEX(@aChar); 
 
	IF hexCode >= "8140" AND hexCode <= "FEA0" THEN
		SELECT letter into firstChar
		FROM   pinyin
		WHERE  chinese >= @aChar
		LIMIT  1;
	ELSE 
	  SET firstChar = @aChar;
	END IF;
 
	SET pinyin = CONCAT(pinyin,firstChar);
	SET pos = pos + 1;
END WHILE;  
 
RETURN UPPER(pinyin);
END$$
 
DELIMITER ;
```

大功告成！