#python #sqlite #sql
Принятые окончания базы **.db .db3 .sqlite .sqlite3**
#### Пример создания и подключения к базе, с пустым запросом
```python
import sqlite3 as sq  #Подключаем модуль и даем для удобства псевдоним

con = sq.connect("firstbase.db")  # осткрываем бузу или создаем её, если её нет
cur = con.cursor()  #для взаимодействия с базой - возвращает экземпляр класса #Сursor
cur.execute("""
""")  # пустой запрос для теста
con.close()  # закрываем базу
```
Более правильный вариант
```python
import sqlite3 as sq  #Подключаем модуль и даем для удобства псевдоним

with sq.connect("firstbase.db") as con:  # осткрываем бузу или создаем её, если её нет
    cur = con.cursor()  #для взаимодействия с базой - возвращает экземпляр класса #Сursor
    cur.execute("""
    """)  # пустой запрос для теста
```
##### Типы данных в таблице
* **NULL** - значение NULL
* **INTEGER** - целочисленный тип (занимает от 1 до 8 байт) 
* **REAL** - вещественный тип (8 байт в формате IEEE)
* **TEXT** - строковый тип (в кодировке данных, обычно UTF-8)
* **BLOB** - двоичные данные, храняться "как есть", например, для небольших изображений 

Для примера создадим таблицу с:
* name - строка с именем игрока
* sex - число, пол игрока (1 - муж., 2 - женс.)
* old - число, возраст игрока
* score - суммарное число набранных очков за все игры
#### Cоздать таблицу в базе
```python
import sqlite3 as sq

with sq.connect("firstbase.db") as con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS users (name TEXT, sex INTEGER, old INTEGER, score INTEGER)""")
```
##### Создать таблицу с дополнительными параметрами ячеек и уникальной ячейкой *user_id*
* **PRIMARY KEY** - уникальные данные (числа)
* **AUTOINCREMENT** - каждая новая строка увеличивается на 1
```python
import sqlite3 as sq

with sq.connect("firstbase.db") as con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS users (
	    user_id INTEGER PRIMARY KEY AUTOINCREMENT,
	    name TEXT NOT NULL,
	    sex INTEGER NOT NULL DEFAULT 1,
	    old INTEGER,
	    score INTEGER
	    )""")
```
#### Сделать выборку всех данных с таблицы
**NOT=>AND=>OR приоритет операций**
```python
import sqlite3 as sq

with sq.connect("firstbase.db") as con:
    cur = con.cursor()
    cur.execute("""SELECT * FROM users""")
    #cur.execute("""SELECT rowid, * FROM users""")  # с уникальной скрытой ячейкой
```
* выбрать 3 ячейки где score в районе от 50 до 200
```sql
SELECT sex, old, score FROM users 
WHERE score BETWEEN 50 AND 200
```
*  где score больше 200
```sql
SELECT sex, old, score FROM users WHERE score > 200
```
* где old равно (24, 44, 32, 31) и score != 0
```sql
SELECT name, sex, old, score FROM users 
WHERE old IN (24, 44, 32, 31) AND score != 0
```
* где old равно (24, 44, 32, 31) или score = 32
```sql
SELECT name, sex, old, score FROM users 
WHERE old IN (24, 44, 32, 31) OR score = 32
```
* где score > 200 и old != 19 и отсортировав выход по ячейке name за алфавитом

```sql
SELECT name, sex, old, score FROM users 
WHERE score > 200 AND old != 19 
ORDER BY name ASC 
LIMIT 10
```
> *ORDER BY name DESC* по убыванию 
> *LIMIT 10* - последние 10строк
> *LIMIT 2, 10* - пропустить первые 2 и вывести последине 10.
#### Сделать выборку в файле python
* через метод `fetchall()` - выводит упорядоченный список кортежей (подходит для маленьких таблиц, так как выводит все данные сразу)
```python
import sqlite3 as sq

with sq.connect("firstbase.db") as con:
    cur = con.cursor()
    cur.execute("""SELECT * FROM users""")
    result = cur.fetchall()  
    print (result)
```
* методом перебора - для больших баз, последовательно формируя кортежи, не нагружая моментно память
```python
import sqlite3 as sq

with sq.connect("firstbase.db") as con:
    cur = con.cursor()
    cur.execute("""SELECT * FROM users""")
    for result in cur:
	    print (result)
```
Так же есть ещё 2 метода:
* fetchmany(syze) - возвращает число записей не более size;
* fetchone() - возвращает первую запись;
#### Удалить таблицу или данные в ней
```sql
DROP TABLE users
```
* с проверкой на существование таблици
```sql
DROP TABLE IF NOT EXISTS users
```
* удалить строки с rowid 2 и 5
```sql
DELETE FROM users WHERE rowid IN(2, 5)
```
#### Внести данные в таблицу
без указания имен ячеек (данные вносятся по порядку)
```sql
INSERT INTO users VALUES('Misha',1, 19, 1111)
```
с указанием имен конкретных ячеек
```sql
INSERT INTO users (name, old, score) VALUES('Kolya', 24, 3232)
```
#### Обновить данные в таблице
**UPDATE имя_таблици SET имя_столбца = новое_значение WHERE условие**
* обнулим все значения score в таблице users
```sql
UPDATE users SET score = 0
```
* обновить 1ю запись ячейки score на 1000
```sql
UPDATE users SET score = 1000 WHERE rowid = 1
```
* обновить все ячейки score, добавив им +1000, строк в которых sex = 2
```sql
UPDATE users SET score = score + 1000 WHERE sex = 2
```
* обновить все ячейки score, добавив им +200, строк в которых name является Miha
```sql
UPDATE users SET score = score + 200 WHERE name LIKE 'Miha'
```
> *WHERE name LIKE 'M%'* - все совпадения на М и дальше любые символы
> *WHERE name LIKE 'Mih_'* - все совпадения на Мih и дальше 1н любой символ
* обновим score = 100 и old = 45 где old = 44
```sql
UPDATE users SET score = 100, old = 45 WHERE old = 44
```