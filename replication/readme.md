# Task 1
#### На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.
```

```

# Task 2
#### Выполните конфигурацию master-slave репликации, примером можно пользоваться из лекции.

1. Создам файл с поднятем 2 контейнеров и сетью

2. docker exec -it mysql-master mysql -u root -p # Создадим пользователям под репликаю и дадим права под это. Проверим что настроили.
```
SELECT User, Host FROM mysql.user;
CREATE USER 'replica-user'@'%' IDENTIFIED BY 'replica';
GRANT REPLICATION SLAVE ON *.* TO 'replica-user'@'%';
FLUSH PRIVILEGES;
SHOW GRANTS FOR 'replica-user'@'%';
SHOW VARIABLES LIKE 'log_bin';

```
6. 



