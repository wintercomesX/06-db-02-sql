# Домашнее задание к занятию "`SQL`" - `Кандала Кирилл`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

1. `Решение задач приведено в поле для вставки кода`

```
Поле для вставки кода...

#manifest

version: '3'
services:
  postgres:
    image: postgres:12
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    volumes:
      - pgdata:/var/lib/postgresql/data
      - pgbackups:/backups

volumes:
  pgdata:
  pgbackups:
```

---

### Задание 2

1. `Итоговый список баз данных после выполнения пунктов выше будет содержать одну базу данных test_db.`
2. `Решение следующих задач приведено в поле для вставки кода`

```
Поле для вставки кода...

#DESCRIBE orders result

           Table "public.orders"
 Column |  Type   | Collation | Nullable | Default 
--------+---------+-----------+----------+---------
 id     | integer |           | not null | 
 name   | text    |           |          | 
 price  | integer |           |          | 

#DESCRIBE clients result

           Table "public.clients"
 Column |  Type   | Collation | Nullable | Default 
--------+---------+-----------+----------+---------
 id     | integer |           | not null | nextval('clients_id_seq'::regclass)
 name   | text    |           | not null | 
 country| text    |           | not null | 
 order  | integer |           |          | 
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
Foreign-key constraints:
    "fk_clients_orders" FOREIGN KEY (order) REFERENCES orders(id)

#SQL query to return a list of users with rights over the test_db tables

SELECT usename AS "User Name", usesysid AS "User ID", usecreatedb AS "Create DB", usesuper AS "Super User", passwd AS "Password", valuntil AS "Password Expires", useconfig AS "Config File" FROM pg_shadow WHERE usename NOT LIKE 'pg_%' AND usename NOT LIKE 'regress_%';

#List of users with rights over the test_db tables (result of the previous query)

| User Name | User ID | Create DB | Super User | Password | Password Expires | Config File |
|-----------|---------|-----------|------------|----------|------------------|-------------|
| test-admin-user | 16384 | f | t | ******** |                |             |
| test-simple-user | 16385 | f | f | ******** |                |             |
```

---

### Задание 3

1. `Решение задач приведено в поле для вставки кода`

```
Поле для вставки кода...

#Filling tables with test data

INSERT INTO orders (name, price) VALUES ('Шоколад', 10), ('Принтер', 3000), ('Книга', 500), ('Монитор', 7000), ('Гитара', 4000);
INSERT INTO clients (name, country) VALUES ('Иванов Иван Иванович', 'USA'), ('Петров Петр Петрович', 'Canada'), ('Иоганн Себастьян Бах', 'Japan'), ('Ронни Джеймс Дио', 'Russia'), ('Ritchie Blackmore', 'Russia');

#Number of records in tables

SELECT count(*) FROM orders;
SELECT count(*) FROM clients;

#Query results

count(*)
---------
5

count(*)
---------
5

```

### Задание 4

1. `Решение задач приведено в поле для вставки кода`

```
Поле для вставки кода...

#Linking records from tables

UPDATE clients SET order_id = (SELECT id FROM orders WHERE name = 'Книга') WHERE name = 'Иванов Иван Иванович';
UPDATE clients SET order_id = (SELECT id FROM orders WHERE name = 'Монитор') WHERE name = 'Петров Петр Петрович';
UPDATE clients SET order_id = (SELECT id FROM orders WHERE name = 'Гитара') WHERE name = 'Иоганн Себастьян Бах';

#Query for output of all users who made an order

SELECT name, order_id FROM clients WHERE order_id IS NOT NULL;

#Result

 name  | order_id
-------+----------
 Иванов |        1
 Петров |        2
 Бах    |        3

```

### Задание 5

1. `Решение задач приведено в поле для вставки кода`
2. `Значения, полученные в результате выполнения запроса, показывают план выполнения запроса, стоимость запроса, количество строк, ширину строки, время выполнения и другие параметры.`

```
Поле для вставки кода...

#Complete information on completing a request to issue all users

EXPLAIN ANALYZE SELECT name, order_id FROM clients WHERE order_id IS NOT NULL;

#Result

                                                 QUERY PLAN                                                  
---------------------------------------------------------------------------------------------------------------
 Seq Scan on clients  (cost=0.00..1.01 rows=1 width=16) (actual time=0.019..0.019 rows=1 loops=1)
   Filter: (order_id IS NOT NULL)
   Rows Removed by Filter: 4
 Planning Time: 0.083 ms
 Execution Time: 0.031 ms
(5 rows)

```


### Задание 6

1. `Решение задач приведено в поле для вставки кода`
```
Поле для вставки кода...

#Creating a database backup

pg_dump -Fc -U postgres -f /backups/test_db.dump test_db

#Stopping a PostgreSQL container

docker stop postgres

#Raising a new empty container with PostgreSQL

docker run --name new-postgres -d -v pgdata:/var/lib/postgresql/data -v pgbackups:/backups postgres:12

#Restoring a database to a new container

psql -U postgres -d postgres -f /backups/test_db.dump

```
