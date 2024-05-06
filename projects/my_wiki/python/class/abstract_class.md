+++
title = 'Abstract_class'
date = 2024-01-20T22:02:52+02:00
draft = false
+++
#python #class
В **Python** абстрактный класс - это класс, который не может быть создан напрямую, а его целью является предоставление интерфейса для других классов. Он может содержать абстрактные методы, которые не имеют реализации в самом классе, но должны быть реализованы в подклассах. Для создания абстрактных классов и методов в Python используется модуль [**abc** (Abstract Base Classes)](https://docs.python.org/uk/3/library/abc.html).\
Вот пример создания абстрактного класса:
```python
from abc import ABC, abstractmethod

class Shape(ABC):  # Наследуем от ABC (Abstract Base Class)
    def __init__(self, x, y):
        self.x = x
        self.y = y
    @abstractmethod
    def area(self):
        pass
    @abstractmethod
    def perimeter(self):
        pass
```
В этом примере **Shape** - абстрактный класс, который содержит абстрактные методы `area` и `perimeter`. Он также содержит обычный метод `__init__`. При попытке создать объект абстрактного класса будет вызвано исключение `TypeError`.

Подкласс, который наследуется от абстрактного класса, обязан предоставить реализации для всех абстрактных методов. Вот пример подкласса **Rectangle**, реализующего абстрактные методы:
```python
class Rectangle(Shape):
    def __init__(self, x, y, width, height):
        super().__init__(x, y)
        self.width = width
        self.height = height
    def area(self):
        return self.width * self.height
    def perimeter(self):
        return 2 * (self.width + self.height)
```
Теперь объекты класса **Rectangle** могут быть созданы, и они обязаны предоставить реализации для методов `area` и `perimeter`.
Использование:
```python
rectangle = Rectangle(0, 0, 10, 5)
print(rectangle.area())       # Вывод: 50
print(rectangle.perimeter())  # Вывод: 30
```
Абстрактные классы полезны для создания интерфейсов, определения общей структуры для классов и обеспечения единообразия в их использовании.