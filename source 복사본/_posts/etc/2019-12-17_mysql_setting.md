---
title: MySQL 설치 및 설정
date: 2019-12-17 09:18:50
categories: etc
---

## Homebrew를 이용하여 MySQL 설치

~~~bash
# 최신버전으로 업데이트
brew update
~~~

~~~bash
brew search mysql
~~~

~~~shell
sudo apt-get update -y
sudo apt-get upgrade -y
~~~

~~~bash
sudo apt-get install mysql-server
~~~

~~~bash
sudo mysql_secure_installation

Would you like to setup VALIDATE PASSWORD plugin? N
New password:
Re-enter new password: 
Remove annoymous users? Y
Disallow root login remotely? N
Remove test database and access to it? Y
Reload privilege tables now? Y
~~~

## MYSQL 설정
~~~bash
# MYSQL 패스워드 설정
sudo mysql

mysql> SELECT user,authentication_string,plugin, host FROM mysql.user;
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '지정할 패스워드'
mysql> FLUSH PRIVILEGES;
mysql> SELECT user,authentication_string,plugin,host FROM mysql.user;
mysql> exit
~~~

~~~bash
# 설정한 패스워드를 입력하여 접속
mysql -u root -p

Enter password: 
~~~

~~~bash
# 외부접속 설정
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf

# bind-address를 127.0.0.1을 0.0.0.0으로 변경
bind-address = 0.0.0.0
~~~

~~~bash
# 외부접속 패스워드 설정
mysql> gran all privileges on *.* to root@'%' idenfified by '지정한 패스워드'
~~~

## 서버 시작 종료 상태 확인

~~~bash
sudo systemctl start mysql.service
sudo systemctl stop mysql.service
sudo systemctl restart mysql.service
sudo systemctl status mysql.service
~~~

## 설정 후 서버 재시작으로 설정 내용 적용

~~~bash
sudo systemctl restart mysql.service
~~~

## 패스워드 변경

~~~bash
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '변경할 패스워드'
~~~



