# 1.MySql基础

```js
// 生成唯一 ID 的库 nanoid
const nanoid = require('nanoid');
const id = nanoid();
```



## 1.1 常用命令

### 创建数据库

```mysql
CREATE DATABASE 数据库名;
```

### 删除数据库

```mysql
drop database 数据库名;
```

### 创建数据表

```mysql
CREATE TABLE 表名 (表字段 字段类型);

CREATE TABLE IF NOT EXISTS `sxt_tbl`(
   `sxt_id` INT UNSIGNED AUTO_INCREMENT,
   `sxt_title` VARCHAR(100) NOT NULL,
   `sxt_author` VARCHAR(40) NOT NULL,
   `submission_date` DATE,
   PRIMARY KEY ( `sxt_id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 如果你不想字段为NULL可以设置字段的属性为 NOT NULL，在操作数据库时如果输入该字段的数据为NULL，就会报错。
-- AUTO_INCREMENT定义列为自增的属性，一般用于主键，数值会自动加1。
-- PRIMARY KEY关键字用于定义列为主键。 您可以使用多列来定义主键，列间以逗号分隔。
-- ENGINE 设置存储引擎，CHARSET 设置编码。
```

### 删除数据表

```mysql
DROP TABLE 表名;
```



## 1.2 常用数据类型

| 类型     | 范围                                                    | 作用            |
| -------- | ------------------------------------------------------- | --------------- |
| INT      | (-2 147 483 648，2 147 483 647)                         | 大整数值        |
| BIGINT   | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807) | 极大整数值      |
| FLOAT    | float精度7位，一般用float，精确度要求更高用double       | 单精度 浮点数值 |
| DOUBLE   | 有效数字16位，                                          | 双精度 浮点数值 |
| TEXT     |                                                         | 长文本数据      |
| LONGTEXT |                                                         | 极大文本数据    |



### 插入数据

```mysql
INSERT INTO table_name ( field1, field2,...fieldN ) VALUES ( value1, value2,...valueN );
```

### 更新数据

```mysql
UPDATE table_name SET field1=new-value1, field2=new-value2 [WHERE Clause];
```

### 删除数据

```mysql
DELETE FROM table_name [WHERE Clause];
```



## 1.3 条件

### 比较运算符

-   等于=
-   大于>
-   大于等于>=
-   小于<
-   小于等于<=
-   不等于!=或<>

```mysql
-- 查询编号大于3的学生
select * from students where id>3;
-- 查询编号不大于4的科目
select * from subjects where id<=4;
-- 查询姓名不是“黄蓉”的学生
select * from students where sname!='黄蓉';
-- 查询没被删除的学生
select * from students where isdelete=0;
```

### 逻辑运算符

-   and
-   or
-   not

```mysql
-- 查询编号大于3的女同学
select * from students where id>3 and gender=0;
-- 查询编号小于4或没被删除的学生
select * from students where id<4 or isdelete=0;
```

### 模糊查询

-   like
-   %表示任意多个任意字符
-   _表示一个任意字符

```mysql
-- 查询姓黄的学生
select * from students where sname like '黄%';
-- 查询姓黄并且名字是一个字的学生
select * from students where sname like '黄_';
-- 查询姓黄或叫靖的学生
select * from students where sname like '黄%' or sname like '%靖%';
```

### 范围查询

-   in表示在一个非连续的范围内
-   between ... and ...表示在一个连续的范围内

```mysql
-- 查询编号是1或3或8的学生
select * from students where id in(1,3,8);
-- 查询学生是3至8的学生
select * from students where id between 3 and 8;
-- 查询学生是3至8的男生
select * from students where id between 3 and 8 and gender=1;
```

### 空判断

-   注意：null与空字符串是不同的
-   判空is null

```mysql
-- 查询没有填写地址的学生
select * from students where hometown is null;
-- 查询填写了地址的学生
select * from students where hometown is not null;
-- 查询填写了地址的女生
select * from students where hometown is not null and gender=0;
```

### 优先级

-   小括号，not，比较运算符，逻辑运算符
-   and比or先运算，如果同时出现并希望先算or，需要结合()使用



# 2.Node.js 连接 MySQL

介绍如何使用 Node.js 来连接 MySQL，并对数据库进行操作。

### 安装

```shell
npm install mysql
```

### 连接数据库

在以下实例中根据你的实际配置修改数据库用户名、及密码及数据库名：

## db.js 文件代码：

```js
const mysql = require('mysql');
// 数据库连接配置对象
const option = {
    // 主机
    host: '127.0.0.1',
    // 用户名
    user: 'root',
    // 密码
    password: '123456',
    // 数据库名称
    database: 'express_server'
};

const connection = mysql.createConnection(option);

connection.connect(err=>{
    if(!err){
        console.log('数据库连接成功！')
    }else {
        console.log('数据库连接失败!')
        console.log(err)
    }
})

module.exports = connection;
```



## 数据库操作( CURD )

### 查询数据

```js
let connection = require('../../db/index');

router.get('/', (req, res)=>{
    let sql = 'SELECT * FROM user';
    // 查询userid = 3的数据
    // let sql = 'SELECT * FROM user WHERE userid=3'; 
    connection.query(sql, (err, data)=>{
        if(!err){
            console.log(data)
            res.send({
                code: 200,
                data
            })
        }
    })
})

```



### 插入数据

```js
let connection = require('../../db/index');

router.get('/register', (req, res)=>{
    const {username, password} = req.query;
    let addSql = 'INSERT INTO user (username, password) VALUES (?, ?)';
    let addParams = [username, password];
    connection.query(addSql, addParams, (err, data)=>{
        if(!err){
            res.send('注册成功!')
        }else {
            console.log(err)
        }
    })
})
```



### 更新数据

```js
let connection = require('../../db/index');

router.get('/update', (req, res) => {
    const {username, password} = req.query;
    let updateSql = 'UPDATE user set username=?, password=? WHERE userid=?'
    let updateParams = [username, password, 3];
    connection.query(updateSql, updateParams, (err, data)=>{
        if(!err){
            res.send('用户账号更新成功!')
        }else {
            console.log(err);
        }
    })
})
```



### 删除数据

```js
let connection = require('../../db/index');

router.get('/deleteUser', (req, res) => {
    const {userid} = req.query;
    let sql = 'DELETE FROM user WHERE userid=?';
    let sqlParams = [userid];
    connection.query(sql, sqlParams, (err, data)=>{
        if(err){
            console.log(err);
            return
        }
        res.send('数据删除成功!');
    })
})
```
