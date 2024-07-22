#python #sqlite #sql 
#### Метод **execute**, **commit**, **close**
* служит для пересылки запроса в базу
```python
import sqlite3 as sq

with sq.connect("cars.db") as con:
	cur = con.cursor()

	cur.execute("""CREATE TABLE IF NOT EXISTS cars (
		car_id INTEGER PRIMARY KEY AUTOINCREMENT,
		model TEXT,
		price INTEGER
	)""")  # создаем таблицу если её нет

	# con.commit()  # физическое сохранение всех изменений
	# con.close()  # закрытие подключения к базе
```
> При такой форме создания методы `con.commit()` и `con.close()` выполняются автоматически и их не нужно вызывать в ручную
#### Метод **executemany**
* Служит для перебора колекций с данными
##### Пример 1:
Добавим в таблицу данные:
```python
import sqlite3 as sq

with sq.connect("cars.db") as con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS cars (
	    car_id INTEGER PRIMARY KEY AUTOINCREMENT,
	    model TEXT,
	    price INTEGER)""")

    cur.execute("INSERT INTO cars VALUES(NULL,'Audi',52642)")
    cur.execute("INSERT INTO cars VALUES(NULL,'Mersedes',57127)")
    cur.execute("INSERT INTO cars VALUES(NULL,'Skoda',9000)")
    cur.execute("INSERT INTO cars VALUES(NULL,'Volvo',29000)")
    cur.execute("INSERT INTO cars VALUES(NULL,'Bentley',350000)")
```
Это же самое с помощью списка кортежей и цикла перебирающего его
```python
import sqlite3 as sq

cars = [('Audi',52642), ('Mersedes',57127), ('Skoda',9000), ('Volvo',29000), ('Bentley',350000),]

with sq.connect("cars.db") as con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS cars (car_id INTEGER PRIMARY KEY AUTOINCREMENT,
	    model TEXT,
	    price INTEGER)""")
    for car in cars:
	    cur.execute("INSERT INTO cars VALUES(NULL, ?, ?)", car)
```
> вместо `?` подставляются значения с кортежей за каждую итерацию цикла
##### Пример 2, с использованием **executemany**
```python
import sqlite3 as sq

cars = [('Audi',52642), ('Mersedes',57127), ('Skoda',9000), ('Volvo',29000), ('Bentley',350000),]

with sq.connect("cars.db") as con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS cars (car_id INTEGER PRIMARY KEY AUTOINCREMENT,
        model TEXT,
        price INTEGER)""")
    cur.executemany("INSERT INTO cars VALUES(NULL, ?, ?)", cars)
```
> вместо `?` можно использовать именованные параметры (Обновим цены на Аудио = 0). В словаре `{}` указываем ключь/значения переменных
```python
cur.execute("UPDATE cars SET price = :Price WHERE model LIKE 'A%'", {'Price': 0})
```
#### Метод **executescript**
* Позволяет выполнить сразу несколько sql команд, разделяя их `;`
```python
import sqlite3 as sq

cars = [('Audi',52642), ('Mersedes',57127), ('Skoda',9000), ('Volvo',29000), ('Bentley',350000),]

with sq.connect("cars.db") as con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS cars (car_id INTEGER PRIMARY KEY AUTOINCREMENT,
	    model TEXT,
	    price INTEGER)""")
	cur.executescript("DELETE FROM cars WHERE model LIKE 'A%';
	UPDATE cars SET price = price + 1000")
```
Либо так, всё в одном запросе
```python
import sqlite3 as sq

cars = [('Audi',52642), ('Mersedes',57127), ('Skoda',9000), ('Volvo',29000), ('Bentley',350000),]

with sq.connect("cars.db") as con:
    cur = con.cursor()
    cur.executescript("""CREATE TABLE IF NOT EXISTS cars (car_id INTEGER PRIMARY KEY AUTOINCREMENT,
	    model TEXT,
	    price INTEGER);
        DELETE FROM cars WHERE model LIKE 'A%';
        UPDATE cars SET price = price + 1000""")
```
> нельзя использовать шаблоны запросов с данным методом
#### Используем методы вместе с проверкой на ошибки. Метод **rollback**
```python
import sqlite3 as sq

cars = [('Audi',52642), ('Mersedes',57127), ('Skoda',9000), ('Volvo',29000), ('Bentley',350000),]
con = None

try:
	con = sq.connect("cars.db")
	cur = con.cursor()
	
	cur.executescript("""CREATE TABLE IF NOT EXISTS cars (car_id INTEGER PRIMARY KEY AUTOINCREMENT,
		model TEXT,
		price INTEGER);
		BEGIN;
		DELETE FROM cars WHERE model LIKE 'A%';
        UPDATE cars SET price = price + 1000""")

	con.commit()  # если запрос прошел успешно - записываем данные
	
except sq.Error as e:
	if con: con.rollback()  # метод отката каких либо изменений в базе (до BEGIN;)
	print ("Ошибка выполнения запроса")
finally:
	if con: con.close()	 # закрываем соединение в любом случае			  
```
> откат, в случае ошибки, происходит до места в запросе с `BEGIN;`
#### Методы **fetchall()**, **fetchmany(size)**, **fetchone()**
* **fetchall()** - выводит результат последнего запроса
```python
import sqlite3 as sq

with sq.connect("cars.db") as con:
    cur = con.cursor()
    cur.execute("SELECT model, price FROM cars")
    sel = cur.fetchall()
    print (sel)
```
* **fetchmany(size)** - выводит указанное количество записей, например (5)
* **fetchone** - выводит первую запись
> в больших таблицах выборку лучше делать через цикл, для экономии памяти
#### Объекты **sqlite3.Row**
Во второй строке соединения с базой устанавливается `row_factory` для соединения `con` с базой данных SQLite. Конкретно, присваивание `sq.Row` свойству `row_factory` делает так, что все результаты запросов возвращаются в виде объектов `sqlite3.Row`, а не обычных кортежей.

Это позволяет обращаться к столбцам результата запроса по именам, а не по индексам. Например, вместо того чтобы писать `row[0]`, можно написать `row["column_name"]`. Это делает код более читабельным и удобным для работы с результатами запросов.

Пример использования:
```python
import sqlite3 as sq

with sq.connect("cars.db") as con:
    con.row_factory = sq.Row
    cur = con.cursor()
    cur.execute("SELECT * FROM cars")
    
    rows = cur.fetchall()
    for row in rows:
        print(f"ID: {row['id']}, Name: {row['name']}, Price: {row['price']}")

```
В этом примере, благодаря установке `row_factory`, можно обращаться к столбцам по их именам, что улучшает читаемость кода.
#### Хранение изображений в базе данных, в бинарном виде.
Создадим новую таблицу **users** в базе **cars** и добивим в нее данные с картинкой в бинарном виде
```python
import sqlite3 as sq

with sq.connect("cars.db") as con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS users (
        name TEXT,
        ava BLOB,
        score INTEGER)""")

# функция для проверки наличия картинки и открытия её
def readAva(n):
    try:
        with open(f"avas/{n}.jpg", "rb") as f:  # "rb" данные в бинарном режиме
            return f.read()
    except IOError as e:
        print (e)
        return False

# Добавим запись с картинкой в базу
img = readAva(1)
if img:
    binary = sq.Binary(img)  # переводим картинку в бинарный вид
    try:
		#cur.execute("INSERT INTO users (name, ava, score) VALUES (?, ?, ?)", ('Nikolay', binary, 1000))  # можно так
        cur.execute("INSERT INTO users VALUES ('Nikolay', ?, 1000)", (binary,))
        con.commit()
    except sq.Error as e:
        print (f"Error add user avatar\n{e}")
    finally:
        cur.close
```
Теперь вытянем картинку из базы
```python
import sqlite3 as sq

def writeAva(name, data):
    try:
        with open(name, "wb") as f:
            f.write(data)
    except IOError as e:
        print (e)
        return False
    return True

with sq.connect("cars.db") as con:
    con.row_factory = sq.Row
    cur = con.cursor()

    cur.execute("SELECT ava FROM users LIMIT 1")
    img = cur.fetchone()['ava']  # благодаря добавления свойства можем обращаться к словарю
    writeAva("out.png", img)
```
#### Метод **iterdump()**
* Дает возможность сделать дамп базы, выводя все данные и команды
```python
import sqlite3 as sq

with sq.connect("cars.db") as con:
    cur = con.cursor()
    
    for sql in con.iterdump():
        print (sql)
```
* Запишем дамп в файл
```python
import sqlite3 as sq

with sq.connect("cars.db") as con:
    cur = con.cursor()
    with open('dumpbase.sql', 'w') as f:
        for sql in con.iterdump():
            f.write(sql)
```
Восстановим базу с дампа
```python
import sqlite3 as sq

with sq.connect("cars.db") as con:
    cur = con.cursor()
    with open('dumpbase.sql', 'r') as f:
        sql = f.read()
        cur.executescript(sql)
```
#### Создание базы в памяти
```python
import sqlite3 as sq

data = [("car", "машина"), ("house", "дом"), ("tree", "дерево"), ("color", "цвет")]

con = sq.connect(':memory:')
with con:
    cur = con.cursor()
    cur.execute("""CREATE TABLE IF NOT EXISTS dict(eng TEXT, rus TEXT)""")
    cur.executemany("INSERT INTO dict VALUES(?, ?)", data)
    cur.execute("SELECT rus FROM dict WHERE eng LIKE 'c%'")
    print (cur.fetchall())
```
Вывод
```python
[('машина',), ('цвет',)]
```
