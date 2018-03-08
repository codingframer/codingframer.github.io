---
layout:     post
title:      Mysql实现序列（Sequence）
subtitle:   
date:       2018-03-08
author:     codingframer
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - Mysql
    - sequence
---

```sql
/*建表*/
DROP TABLE IF EXISTS sequence;     
CREATE TABLE sequence (         
seq_name        VARCHAR(50) NOT NULL COMMENT '序列名称',
current_val     INT         NOT NULL COMMENT '当前值',  
increment_val   INT         NOT NULL DEFAULT 1 COMMENT '步长(跨度)',
PRIMARY KEY (seq_name)   
);
```

```sql
/*插入数据*/
INSERT INTO sequence VALUES ('global', 0, 1);
```

```sql
/*当前值*/
DELIMITER $$
CREATE FUNCTION currval(v_seq_name VARCHAR(50))     
RETURNS INTEGER    
BEGIN        
    DECLARE value_ INTEGER;         
    SET value_ = 0;         
    SELECT current_val INTO value_  FROM sequence WHERE seq_name = v_seq_name;   
    RETURN value_;
END $$  
DELIMITER;
```

```sql
/*设置值*/
DELIMITER $$
CREATE FUNCTION setval (seq_name_ VARCHAR(50),value_ INTEGER) 
RETURNS INTEGER
BEGIN
 UPDATE sequence
 SET current_val = value_
 WHERE seq_name = seq_name_;
 RETURN currval(seq_name_);
END $$  
DELIMITER;
```

```sql
/*下个值*/
DELIMITER $$
CREATE FUNCTION nextval (v_seq_name VARCHAR(50))  
    RETURNS INTEGER  
BEGIN  
    UPDATE sequence SET current_val = current_val + increment_val  WHERE seq_name = v_seq_name;  
    RETURN currval(v_seq_name);  
END $$  
DELIMITER;
```

```sql
/*测试*/
SELECT nextval('global')
/*table id auto_increment*/
```

```sql
/*触发器*/
DELIMITER $$
CREATE TRIGGER `tri_e_substation_id` BEFORE INSERT ON `e_substation` FOR EACH ROW BEGIN  
SET NEW.id = nextval('global');
END$$
DELIMITER ;  
```