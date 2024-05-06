# logical_level
Выполнение задания 
```
postgres=# create database testdb;
CREATE DATABASE

postgres=# \d testdb
Отношение "testdb" не найдено.

postgres=# \c testdb;
Вы подключены к базе данных "testdb" как пользователь "postgres".

testdb=# create schema testnm;
CREATE SCHEMA

testdb=# create table t1 (c1 int);
CREATE TABLE

testdb=# insert into t1 values(1);
INSERT 0 1

testdb=# create role readonly;
CREATE ROLE

testdb=# grant connect on database testdb to readonly;
GRANT

testdb=# grant usage on schema testnm to readonly;
GRANT

testdb=# grant select on all table in schema testnm to readonly;
ОШИБКА:  ошибка синтаксиса (примерное положение: "table")
СТРОКА 1: grant select on all table in schema testnm to readonly;
                              ^
testdb=# grant select on all tables in schema testnm to readonly;
GRANT

testdb=# create user testread with password 'test123';
CREATE ROLE

testdb=# grant readonly to testread;
GRANT ROLE

testdb=# \c testdb testread
Вы подключены к базе данных "testdb" как пользователь "testread".

testdb=> select*from t1
testdb-> ;
ОШИБКА:  нет доступа к таблице t1
testdb=>

#Нет доступа к таблице в связи с тем что таблица создана в схеме public, а на схему public у пользователя testread нет прав
testdb=> \c testdb postgres
Вы подключены к базе данных "testdb" как пользователь "postgres".

testdb=# drop table t1
testdb-# ;
DROP TABLE

testdb=# create table testnm.t1 (c1);

testdb=# create table testnm.t1 (c1 int);
CREATE TABLE
testdb=# insert into testnm.t1(c1) values(1);
INSERT 0 1

testdb=# \c testdb testread;
Вы подключены к базе данных "testdb" как пользователь "testread".

testdb=> select*from testnm.t1;
ОШИБКА:  нет доступа к таблице t1

testdb=> \c testdb postgres
Вы подключены к базе данных "testdb" как пользователь "postgres".

testdb=# alter default privileges in schema testnm grant select on tables to readonly;
ALTER DEFAULT PRIVILEGES

testdb=# \c testdb testread;
Вы подключены к базе данных "testdb" как пользователь "testread".

testdb=> select *from testnm.t1
testdb-> ;
ОШИБКА:  нет доступа к таблице t1

testdb=> \c testdb postgres

#Пересоздаю таблицу в схеме testnm ,чтобы был доступ на select c новыми правами
Вы подключены к базе данных "testdb" как пользователь "postgres".
testdb=# drop table testnm.t1;
DROP TABLE

testdb=# create table testnm.t1 (c1 int);
CREATE TABLE

testdb=# insert into testnm.t1(c1) values(1);
INSERT 0 1

testdb=# \c testdb testread;
Вы подключены к базе данных "testdb" как пользователь "testread".

testdb=> select*from testnm.t1
testdb-> ;
 c1
----
  1
(1 строка)

#Создать таблицы не получилось, не в схеме testnm, не в схеме Public  
#у меня 15 версия на домашнем ПК установлена, все верно прав у user`a testread на создание #таблиц нет, кажется решение только давай grant на создание таблиц в схеме testnm для #пользователя

testdb=> create table t2(c1 int);
ОШИБКА:  нет доступа к схеме public
СТРОКА 1: create table t2(c1 int);
                       ^
testdb=> create table testnm.t2(c1 int);
ОШИБКА:  нет доступа к схеме testnm
СТРОКА 1: create table testnm.t2(c1 int);
                       ^
testdb=>


