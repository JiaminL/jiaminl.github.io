>  利用 python3 中的 pymysql，或 python2 的 MySQLdb，向服务器发送 SQL 命令，从而实现数据库操作。

## Python 中操作 MySQL 

### 引入模块

```python
from pymysql import *
```

### 链接服务器

需要注意，MySQL 默认使用了 UNIX auth_socket plugin插件，所以要通过下种方式连接有两种方法

1. 可以设置你的root用户使用 mysql_native_password 插件
2. 可以创建一个新的数据库用户，以使用 mysql_native_password 插件

```python
conn = connect(host='localhost', port=3306, user='root', password='mysql', database='jing_dong', charset='utf8')
```

### 获得 Cursor 对象

```python
cs1 = conn.cursor()
```

### 执行 SQL 语句

调用游标对象 csl 的方法 execute 来执行 SQL 语句，execute 返回值为 SQL 语句返回的行数，执行完以后，可以通过游标对象的 `fetchall()`、`fetchmany(n)`、`fetchone()` 方法来获得  SQL 输出，

```python
count = cs1.execute("select id, name from goods where id >= 40")
for i in range(count):
    print(cs1.fetchone())
```

### 提交修改及取消修改

```python
# 提交修改
conn.commit()
# 如果返回可以取消修改到上次提交
conn.rollback()
```

### 关闭 cursor 对象与链接

```sql
cs1.close()
conn.close()
```

## SQL 注入

也称 **SQL 注码**，是发生于应用程序与数据库层的安全漏洞。简而言之，是在输入的字符串之中注入 SQL 指令，在设计不良的程序当中忽略了字符检查，那么这些注入进去的恶意指令就会被数据库服务器误认为是正常的SQL指令而运行，因此遭到破坏或是入侵。

非安全模式

```python
# 查询代码
find_name = input("请输入要查询的商品名字：")
sql = 'select * from goods where name = "%s"' % find_name
count = csl.execute(sql)
# 如果用户输入 " or 1=1 or " （包括双引号） 给 find_name
# 将会执行如下SQL语句
# select * from goods where name = "" or 1=1 or "";
# 从而将所有数据全部输出
```

安全的模式

```python
# 构造参数列表
params = [find_name]
# 让 execute 来拼装字符串
count = csl.execute('select from goods where name = %s', params)
```

注意：不要轻易尝试，否则可能会触犯法律。