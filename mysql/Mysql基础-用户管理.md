# Mysql基础-用户管理

## 创建用户
```sql
create user 'user1'@'%' identified by 'password';
```

## 授权用户访问数据库
```sql
grant all privileges on mydb.* to 'user1'@'%';
```

## 撤销权限
```sql
revoke all privileges on mydb.* from 'user1'@'%';
```

## 删除用户
```sql
drop user 'user1'@'%';
```

