---
layout: post
title:  "mysql 命令行"
categories:  mysql command
---

### 连接数据库
本地连接（且默认端口为3306）  
mysql -u root -p  
远端连接  
mysql -P 3306 -h 192.168.1.104 -u root -p

### 查看所有数据库
show databases;

### 创建数据库
create database db_name;

### 删除数据库
drop database db_name;

### 使用数据库
use db_name;

### 展示数据库中所有表
show tables;

### 查看表结构
show columns from table_name;

### 导入数据库文件
mysql -h localhost -u root -p db_yves < D:\db_yves.sql

### 导出数据库文件
- 导出数据库结构和数据  
    mysqldump -h localhost -u root -p db_name > D:\db_name.sql
- 导出数据库结构  
    mysqldump -h localhost -u root -p db_name -d > D:\db_name_stru.sql
- 导出数据库表的数据和结构  
    mysqldump -h localhost -u root -p db_name table_name > D:\table_name.sql
- 导出数据库表的结构  
    mysqldump -h localhost -u root -p db_name table_name -d > D:\table_name_stru.sql

### 其他命令
- 显示服务器状态  
    SHOW STATUS
- 显示创建数据库语句  
    SHOW CREATE DATABASE
- 显示创建表语句
    SHOW CREATE TABLE
- 显示授予用户的安全权限
    SHOW GRANTS
- 显示服务器错误信息
    SHOW ERRORS
- 显示服务器警告信息
    SHOW ERRORS








