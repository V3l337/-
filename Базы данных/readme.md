# Задание 1 
https://github.com/netology-code/sdb-homeworks/raw/main/resources/hw-12-1.xlsx

```SQL
CREATE TABLE Сотрудники (
    идентификатор SERIAL PRIMARY KEY,
    фио VARCHAR(100),
    оклад DECIMAL,
    должность_id INTEGER REFERENCES Должности(идентификатор),
    подразделение_id INTEGER REFERENCES Подразделения(идентификатор),
    дата_найма DATE,
    адрес_филиала_id INTEGER REFERENCES Адреса_филиалов(идентификатор),
    проект_id INTEGER REFERENCES Проекты(идентификатор)
);
```
```sql
CREATE TABLE Должности (
    идентификатор SERIAL PRIMARY KEY,
    наименование VARCHAR(50)
);
```
```sql
CREATE TABLE Подразделения (
    идентификатор SERIAL PRIMARY KEY,
    наименование VARCHAR(100),
    тип_id INTEGER REFERENCES Типы_подразделений(идентификатор)
);
```
```sql
CREATE TABLE Типы_подразделений (
    идентификатор SERIAL PRIMARY KEY,
    наименование VARCHAR(50)
);
```
```sql
CREATE TABLE Проекты (
    идентификатор SERIAL PRIMARY KEY,
    наименование VARCHAR(100)
);
```
```sql
CREATE TABLE Адреса_филиалов (
    идентификатор SERIAL PRIMARY KEY,
    адрес VARCHAR(255)
);
```
```sql
CREATE TABLE Назначения_на_проекты (
    сотрудник_id INTEGER REFERENCES Сотрудники(идентификатор),
    проект_id INTEGER REFERENCES Проекты(идентификатор),
    PRIMARY KEY (сотрудник_id, проект_id)
);
```
