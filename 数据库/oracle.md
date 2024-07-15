1.登录sys用户

```sh
su root 
密码

su - oracle

sqlplus /nolog

conn sys/sys as sysdba

```

2.创建用户-赋予权限

```

-- 创建新用户
CREATE USER new_user IDENTIFIED BY password;
 
-- 授予SELECT ANY DICTIONARY权限
GRANT SELECT ANY DICTIONARY TO new_user;

--授予create session权限
grean create session to new_user;
```

3.会话数以及线程数

```sql
--查询线程数
SELECT value FROM v$parameter WHERE name = 'processes';
--修改连接数
ALTER SYSTEM SET processes = 500 SCOPE = SPFILE;

```

