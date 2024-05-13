+++
title = 'Lambda'
date = 2024-03-10T13:44:49+02:00
draft = false
+++
**Lambda** - функции в Python - это анонимные (безымянные) функции, которые могут содержать только одно выражение. Они обычно используются в местах, где требуется функция, но её определение не требуется быть долгим или сложным. Lambda-функции создаются с помощью ключевого слова `lambda`.  

Синтаксис **lambda**-функции:
```python
lambda arguments: expression
```
Где:
* **arguments** - это аргументы функции (могут быть любым количеством, но lambda-функции обычно используются для простых случаев с одним или двумя аргументами).
* **expression** - это выражение, которое выполняется и возвращается в lambda-функции.
Вот несколько примеров использования lambda-функций:
```python
# Пример 1: lambda-функция с одним аргументом
square = lambda x: x**2
print(square(5))  # Вывод: 25

# Пример 2: lambda-функция с двумя аргументами
add = lambda x, y: x + y
print(add(3, 4))  # Вывод: 7

# Пример 3: lambda-функция внутри функции
def my_function(n):
    return lambda x: x * n

double = my_function(2)
triple = my_function(3)

print(double(10))  # Вывод: 20
print(triple(10))  # Вывод: 30
```
**Lambda**-функции часто используются вместе с функциями высшего порядка, такими как **[map()]()**, **[filter()](projects/dusky-wiki/python/function/default_function.md)**, **[reduce()]()**, а также в тех случаях, когда нужно передать функцию в качестве аргумента.  

Например, с использованием **map()**:
```python
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, numbers))
print(squared)  # Вывод: [1, 4, 9, 16, 25]
```
Или с использованием **filter()**:
```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(even_numbers)  # Вывод: [2, 4, 6, 8, 10]
```
Или с использованием **reduce()** (для Python 3.x требуется импортировать из модуля `functools`):
```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]
product = reduce(lambda x, y: x * y, numbers)
print(product)  # Вывод: 120
```
Таким образом, **lambda**-функции представляют собой удобный способ определения небольших функций на лету, когда их использование не требует создания полноценного определения функции с помощью `def`.