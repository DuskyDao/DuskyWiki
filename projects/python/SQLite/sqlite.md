#python #sqlite #sql
Принятые окончания базы **.db .db3 .sqlite .sqlite3**
Пример создания и подключения к базе, с пустым запросом
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
##### Cоздать таблицу в базе
```python
import sqlite3 as sq

with sq.connect("firstbase.db") as con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS users (name TEXT, sex INTEGER, old INTEGER, score INTEGER)""")
```
##### Сделать выборку всех данных с таблицы
```python

import sqlite3 as sq

with sq.connect("firstbase.db") as con:
    cur = con.cursor()
    cur.execute("""SELECT * FROM users""")
    #cur.execute("""SELECT rowid, * FROM users""")  # с уникальной скрытой ячейкой
```
##### Удалить таблицу
```python
import sqlite3 as sq

with sq.connect("firstbase.db") as con:
    cur = con.cursor()
    cur.execute("""DROP TABLE users""")
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
