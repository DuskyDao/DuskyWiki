+++
title = 'Files'
date = 2024-02-20T15:47:31+02:00
draft = false
+++
#python #file #os #path #pathlib
### Содержание:
1. **[[#Модуль os.path]]**
2. **[[#Модуль Pathlib]]**
3. **[[#Работа с файлами]]**

#### Модуль **os.path**
Модуль **os.path** в языке программирования **Python** предоставляет удобные функции для работы с путями файловой системы. Он позволяет создавать, модифицировать и анализировать пути к файлам и директориям на всех поддерживаемых операционных системах. Давайте рассмотрим некоторые из основных функций модуля `os.path` с примерами.

**os.path.join()**
Эта функция используется для объединения компонентов пути в один.
```python
import os
path = os.path.join("folder", "subfolder", "file.txt")
print(path)
# Вывод: folder\subfolder\file.txt`
```
**os.path.basename()**
Возвращает последний компонент указанного пути.
```python
import os
path = "/path/to/file.txt"
basename = os.path.basename(path)
print(basename)  # Вывод: file.txt`
```
**os.path.dirname()**
Возвращает директорию указанного пути.
```python
import os
path = "/path/to/file.txt"
dirname = os.path.dirname(path)
print(dirname)  # Вывод: /path/to`
```
**os.path.exists()**
Проверяет существование указанного пути.
```python
import os
path = "/path/to/file.txt"

if os.path.exists(path):
	print("Путь существует.")
else:
	print("Путь не существует.")`
```
**os.path.isfile()** и **os.path.isdir()**
Проверяют, является ли указанный путь файлом или директорией.
```python
import os
path_file = "/path/to/file.txt"
path_dir = "/path/to/directory"

if os.path.isfile(path_file):
	print("Это файл.")
else:
	print("Это не файл.")
	
if os.path.isdir(path_dir):
	print("Это директория.")
else:
	print("Это не директория.")`
```
**os.path.splitext()**
Разделяет путь на имя файла и расширение.
```python
import os
filename = "file.txt"
name, extension = os.path.splitext(filename)
print("Имя файла:", name)  # Вывод: file 
print("Расширение файла:", extension)  # Вывод: .txt`
```
Это лишь некоторые из функций, предоставляемых модулем `os.path`. Они делают работу с путями файлов и директорий более удобной и переносимой между различными операционными системами.
#### Модуль **Pathlib**
Модуль `pathlib` в Python предоставляет удобный способ для работы с путями к файлам и папкам в файловой системе. Он представляет объектно-ориентированный интерфейс для работы с путями, что делает код более читаемым и понятным. Давайте рассмотрим основные возможности модуля `pathlib` с примерами.
* **Создание пути**
Сначала нужно импортировать модуль `pathlib`:
```python
from pathlib import Path
```
* **Создание объекта пути:**
```python
# Абсолютный путь
path = Path("/path/to/file.txt")

# Путь относительно текущей директории
path = Path("file.txt")`
```

##### Основные операции с путями
* **Получение информации о пути:**
```python
# Получение абсолютного пути
print(path.resolve())

# Получение имени файла или папки
print(path.name)

# Получение родительской директории
print(path.parent)

# Проверка существования пути
print(path.exists())

# Проверка, является ли путь файлом или папкой
print(path.is_file())
print(path.is_dir())
```
* **Объединение путей:**
```python
# Объединение путей
new_path = path / "folder" / "file.txt"
```
* **Итерация по содержимому папки:**
```python
# Получение списка файлов и папок в директории
for item in Path("folder").iterdir():
print(item)
```
##### Операции с файлами и папками
* **Создание и удаление файлов и папок:**
```python
# Создание папки
Path("new_folder").mkdir()

# Удаление папки
Path("new_folder").rmdir()

# Создание файла и запись в него
with open(Path("new_file.txt"), "w") as file:
	file.write("Hello, world!")

# Удаление файла
Path("new_file.txt").unlink()
```
* **Копирование и перемещение файлов и папок:**
```python
# Копирование файла
Path("source_file.txt").replace("target_file.txt")

# Копирование папки и ее содержимого
import shutil
shutil.copytree("source_folder", "target_folder")

# Перемещение файла или папки
Path("source").rename("target")
```
##### Пример: Использование **pathlib** для работы с файлами и папками
```python
from pathlib import Path

# Создание папки и файла
path = Path("example_folder")
path.mkdir()
with open(path / "example_file.txt", "w") as file:
	file.write("Hello, world!")
	
# Чтение из файла
with open(path / "example_file.txt", "r") as file:
	content = file.read()
	print(content)  # Вывод: Hello, world!
	
# Удаление папки и ее содержимого
shutil.rmtree(path)
```
Это лишь небольшой обзор возможностей модуля `pathlib`. Он предоставляет удобный и более понятный способ для работы с путями к файлам и папкам, и рекомендуется к использованию в современном Python коде.
#### Работа с файлами
```python
open ('test.txt', 'w') # 'w' оператор, может принимать двойные значения (wt)
open (r'E:\git\first_rep_test\python_lessons\part1\test.txt', 'w') # с указанием полного пути и папки где нужно создать/открыть файл
open ('E:\\git\\first_rep_test\\python_lessons\\part1\\test1.txt', 'w') # с двойными слешами
open ('E:/git/first_rep_test/python_lessons/part1/test2.txt', 'w') # можно и так (для линукс машин в основном, но работает и на винде)
# 'r'	открытие на чтение (является значением по умолчанию).
# 'w'	открытие на запись, содержимое файла удаляется, если файла не существует, создается новый.
# 'x'	открытие на запись, если файла не существует, иначе исключение.
# 'a'	открытие на дозапись, информация добавляется в конец файла.
# 'b'	открытие в двоичном режиме.
# 't'	открытие в текстовом режиме (является значением по умолчанию).
# 'r+'	открытие на чтение и запись (файл должен уже существовать)
# 'w+'  открытие на чтение и запись (перезаписывает файл или создает его))

# Пример:
l = [str(i)+str(i-1) for i in range(10)]
#задаем список ['0-1', '10', '21', '32', '43', '54', '65', '76', '87', '98']
f = open("test.txt", "w") # открываем/создаем файл test.txt с правами записи
for index in l: # проходим циклом по нашему списку
    f.write(index + '\n')  # записываем данные с цикла в файл
f.close() # закрываем файл, ОБЯЗАТЕЛЬНО!!

# Пример 2:
f = open ('test.txt', 'r') # открываем файл test.txt с правами просмотра
l = [line.strip() for line in f] # создаем список с данных файла с удалением пробелов 
f.close
print (l)

# Функция read() вывода содержимого файла
f = open ('test.txt', 'r')
print (f.read()) # выводит только один раз данные, так как функция их выводит посимвольно перемещаясь от начала в конец файла,
# после чего "курсор" остается в конце.
f.seek(0) # перемещает курсов в начало файла (0) и дает возможность выводить данные еще раз

# Функция передачи содержимого в список
f = open ('test.txt', 'r')
lines = f.readlines()
print (type(lines)) # [[dusky-wiki/python/variable_types/lists/list|list]] список
print (lines)
#['0-1\n', '10\n', '21\n', '32\n', '43\n', '54\n', '65\n', '76\n', '87\n', '98\n']
f.close

# Читаем содержимое файла с автоматическим закрытием
with open('test.txt') as test_file:
    test_data = test_file.read()
    print (f" содержимое файла {test_data}") 

# Создаем файл и записываем в него, в конец, 'Bobs:buba' и выводим
text = 'E:/git/first_rep_test/python_lessons/part1/test5.txt'
with open(text, 'w+') as open_text:
    open_text.seek(0, 2) # переносим курсор в конец файла
    open_text.write('n\Bobs:buba') # записываем
    open_text.seek(0)  # переносим курсор в начало файла
    print (open_text.read())
```
