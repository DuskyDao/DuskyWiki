+++
title = 'Decorators'
date = 2024-03-10T21:02:52+02:00
draft = false
+++
**Декораторы (decorators)** в Python - это мощный инструмент, который позволяет изменять поведение функций или методов без изменения их самих. Они представляют собой функции, которые принимают другую функцию в качестве аргумента и возвращают новую функцию. Декораторы могут использоваться для добавления дополнительной функциональности к существующим функциям, такой как логирование, аутентификация, кэширование, проверка аргументов и многое другое.  
##### Синтаксис декораторов в Python выглядит следующим образом:
```python
@decorator
def function():
    pass
```
Где `decorator` - это функция-декоратор, которая принимает другую функцию в качестве аргумента.  

Давайте рассмотрим примеры декораторов с более подробным объяснением:
##### Пример 1: **Декоратор для логирования**
```python
def log_decorator(func):
    def wrapper(*args, **kwargs):
        print(f"Вызвана функция {func.__name__}() с аргументами {args}, {kwargs}")
        result = func(*args, **kwargs)
        print(f"Функция {func.__name__}() вернула {result}")
        return result
    return wrapper

@log_decorator
def add(x, y):
    return x + y

result = add(3, 5)
# Вывод:
# Вызвана функция add() с аргументами (3, 5), {}
# Функция add() вернула 8
```
##### Пример 2: **Декоратор для проверки аргументов**
```python
def validate_arguments(func):
    def wrapper(*args, **kwargs):
        if any(not isinstance(arg, int) for arg in args):
            raise TypeError("Аргументы должны быть целыми числами")
        return func(*args, **kwargs)
    return wrapper

@validate_arguments
def multiply(x, y):
    return x * y

result = multiply(3, 5)
print(result)  # Вывод: 15

result = multiply("hello", 5)  # Вызовет TypeError
```
##### Пример 3: **Декоратор с аргументами**
```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(times=3)
def greet(name):
    print(f"Привет, {name}!")

greet("Миша")
# Вывод:
# Привет, Миша!
# Привет, Миша!
# Привет, Миша!
```
В этих примерах `log_decorator`, `validate_arguments` и `repeat` - это декораторы, которые изменяют поведение функций `add`, `multiply` и `greet`, соответственно. Декораторы позволяют создавать компактный и чистый код, улучшая его читаемость и поддерживаемость.  

Это лишь несколько примеров использования декораторов в Python. Они могут быть использованы для широкого спектра задач и добавлять любую необходимую функциональность к функциям или методам.