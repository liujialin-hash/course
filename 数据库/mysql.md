## MySQL新建用户

在MySQL中，您可以使用`CREATE USER`语句来创建新用户。以下是一个创建新用户的示例代码：

```
CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
```

这将创建一个名为`newuser`的用户，其密码为`password`。用户将从`localhost`连接，这意味着用户将只能从本地计算机连接到MySQL服务器。如果您希望用户能够从任何主机连接，您可以将`localhost`替换为`%`。

```
CREATE USER 'newuser'@'%' IDENTIFIED BY 'password';
```

请确保在创建用户后运行`FLUSH PRIVILEGES;`命令，以使更改生效。

```
FLUSH PRIVILEGES;
```

您还可以为新用户分配特定的权限和角色。例如，为`newuser`授予在所有数据库上的所有权限：

```
GRANT ALL PRIVILEGES ON *.* TO 'newuser'@'localhost';FLUSH PRIVILEGES;
```

或者，只在特定数据库上授予特定权限：

```
GRANT SELECT, INSERT ON mydatabase.mytable TO 'newuser'@'localhost';FLUSH PRIVILEGES;
```

请记得替换`mydatabase`和`mytable`以指定正确的数据库和表名，并替换`newuser`和`password`以设置适合您环境的用户名和密码。

提示：AI自动生成，仅供参考

## 新建一个当前时间的字段的流程

```
ALTER TABLE `user` ADD COLUMN create_time DATE DEFAULT '1970-01-01';
UPDATE `user` SET create_time = CURRENT_DATE WHERE create_time = '1970-01-01';
ALTER TABLE `user` MODIFY COLUMN create_time DATE NOT NULL;
```

## 默认时间字段

```sql
ALTER TABLE news MODIFY date TIMESTAMP DEFAULT CURRENT_TIMESTAMP;
```

