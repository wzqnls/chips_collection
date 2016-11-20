## 1.安装并启动MariaDB
```
    # 先更新一下
    yum upgrade
    # 安装
    yum -y install mariadb mariadb-server
    # 启动服务
    systemctl start mariadb
```

## 2.进行基本选项配置（也可以后再配置）
```
    mysql_secure_installation
```

## 3.编码配置（重点呀）
```
    # 编辑/etc/my.cnf
    vim /etc/my.cnf

    # 在[mysqld]标签下添加下面内容
    default-storage-engine = innodb
    innodb_file_per_table
    max_connections = 4096
    collation-server = utf8_general_ci
    character-set-server = utf8

    # 编辑/etc/my.cnf.d/client.cnf
    vim /etc/my.cnf.d/client.cnf

    # 在[client]标签下添加下面内容
    default-character-set=utf8

    # 编辑/etc/my.cnf.d/mysql-clients.cnf
    vim /etc/my.cnf.d/mysql-clients.cnf

    # 在[mysql]标签下添加下面内容
    default-character-set=utf8

```

## 4.重启服务
```
    systemctl restart mariadb

    # 设置开机自启动
    systemctl enable mariadb
```

## 5.查看字符集
```
    # 登录mysql之后，键入下面内容
    show variables like "%character%";show variables like "%collation%";
    # 简单的查看命令
    status
```

## 6.创建新数据库时进行编码设置
```
    # 有些小伙伴忘记了配置mysql的编码
    # 导致后期数据库数据乱码
    # 解决办法就是修改这个数据库编码格式
    # 但是很麻烦，不建议这样
    # 懒的配置的话，就在创建数据库时，直接进行数据库编码设置，按需配置
    create database `database_name` default character set utf8 collate utf8_general_ci;
```