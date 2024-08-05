# Task 1
#### На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.
```
1. Master работает как основной и обрабатывает все записи и обновления данных. Один или несколько серверов Slaves копируют данные с Master и только читают их.
2. Несколько серверов Masters могут одновременно обрабатывать записи и обновления данных. Все Masters могут принимать и применять изменения, которые затем реплицируются между собой.
```

# Task 2
#### Выполните конфигурацию master-slave репликации, примером можно пользоваться из лекции.

1. Создам файл с поднятем 2 контейнеров и сетью

2. docker exec -it mysql-master mysql -u root -p # Создадим пользователям под репликаю и дадим права под это. Проверим что настроили.
```mysql
SELECT User, Host FROM mysql.user;
CREATE USER 'replica-user'@'%' IDENTIFIED BY 'replica';
GRANT REPLICATION SLAVE ON *.* TO 'replica-user'@'%';
FLUSH PRIVILEGES;
SHOW GRANTS FOR 'replica-user'@'%';
SHOW VARIABLES LIKE 'log_bin';
перезагрузить контейнер!
SHOW MASTER STATUS;
```
3. docker exec -it mysql-slave /bin/bash
4. изменим/добавим в файл /etc/my.cnf
```
log_bin = mysql-bin
server_id = 2
relay-log = /var/lib/mysql/mysql-relay-bin
relay-log-index = /var/lib/mysql/mysql-relay-bin.index
read_only = 1
```
5. Перезагрузить контейнер и зайти в бд. docker exec -it mysql-slave mysql -u root -p
```mysql
CHANGE MASTER TO
  MASTER_HOST='mysql-master',
  MASTER_USER='replica-user',
  MASTER_PASSWORD='replica',
  MASTER_LOG_FILE='mysql-bin.000004',
  MASTER_LOG_POS=157;

START SLAVE;
```
```mysql
SHOW SLAVE STATUS\G
```
6. Проверяю с помощью создания ДБ
```sql
CREATE database world;
USE world;
CREATE TABLE test_replication (
  id INT AUTO_INCREMENT PRIMARY KEY,
  data VARCHAR(255) NOT NULL
  );
INSERT INTO test_replication (data) VALUES ('Test data 1');
INSERT INTO test_replication (data) VALUES ('Test data 2');
```
7. Проверил на SLAVE что БД отоброжается, а значит репликация работает.


