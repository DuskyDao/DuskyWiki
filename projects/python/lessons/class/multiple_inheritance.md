#python #class
### **Краткое содержание:**
1. **[Наследования](#наследования)**
     - [Проблемы с наследованием](#проблемы-с-наследованием)
     - [Ещё пример:](#ещё-пример)
2. **[Миксины](#миксины)**
### Наследования
В питоне можно наследовать как классы так и их методы. В данном примере классы `Carnivour(Animal)` и `Mammal(Animal)` наследуют класс `Animal()`, а класс `Dog(Carnivour, Mammal)` наследует что  `Carnivour(Animal)`, что  `Mammal(Animal)`. Так же на все эти классы распостраняется общий метод `set_health(self, health)`.
```python
class Animal():
    def set_health(self, health):
        print ('set in animal')
class Carnivour(Animal):        
    def set_health(self, health):
        print ('set in carnivour')
class Mammal(Animal):
    def set_health(self, health):
        print ('set in mammal')
class Dog(Carnivour, Mammal):
    pass
```
При вызове класса `Dog()` c методом `set_health(self, health)` первым сработает внутри тот класс, которой стоит первым в аргументах - в данном случае класс `Carnivour`
```python
dog = Dog()
dog.set_health(33)
# set in carnivour
```
Мы так же в классе `Dog()` можем вызывать метод `set_health`, как и инициализировать вывод классов выше
```python
class Animal():
    def set_health(self, health):
        print ('set in animal')
class Carnivour(Animal):        
    def set_health(self, health):
        print ('set in carnivour')
class Mammal(Animal):
    def set_health(self, health):
        print ('set in mammal')
class Dog(Carnivour, Mammal):
    def set_health(self, health):
        Carnivour.set_health(self, health)
        Mammal.set_health(self, health)
        Animal.set_health(self, health)
        print ('set in dog')
dog = Dog()
dog.set_health(33)
# set in carnivour
# set in mammal
# set in animal
# set in dog
```
#### Проблемы с наследованием
В данном примере всё работает отлично, но проблемы начинают возникать, если подклассы `Carnivour(Animal)` и `Mammal(Animal)` так же инициализируют родительский класс `Animal()` у себя.
```python
class Animal():
    def set_health(self, health):
        print ('set in animal')
class Carnivour(Animal):        
    def set_health(self, health):
        Animal.set_health(self, health) # тут
        print ('set in carnivour')
class Mammal(Animal):
    def set_health(self, health):
        Animal.set_health(self, health) # тут
        print ('set in mammal')
class Dog(Carnivour, Mammal):
    def set_health(self, health):
        Carnivour.set_health(self, health)
        Mammal.set_health(self, health)
        print ('set in dog')
dog = Dog()
dog.set_health(33)
# set in animal
# set in carnivour
# set in animal
# set in mammal
# set in dog
```
> При вызове класса `Dog()` вызов самого глубокого родительского класса `Animal()` повторяется дважды, что в данном случае ни на что не влияет, но будь на его месте не просто строка текста, а какая-то функция - у нас возникли бы большие проблемы!

На помощь нам приходит встроенная функция `super().метод(аргументы)`
```python
class Animal():
    def set_health(self, health):
        print ('set in animal')
class Carnivour(Animal):        
    def set_health(self, health):
        super().set_health(health) # вызывает метод родительского класса Animal
        print ('set in carnivour')
class Mammal(Animal):
    def set_health(self, health):
        super().set_health(health) # вызывает метод родительского класса Animal
        print ('set in mammal')
class Dog(Carnivour, Mammal):
    def set_health(self, health):
        super().set_health(health)
        print ('set in dog')
dog = Dog()
dog.set_health(33)
# set in animal
# set in mammal
# set in carnivour
# set in dog
```
Функция `super()` отрабатывает всегда **снизу-вверх** и **слева-направо**. Сначала выводит самый глубокий родительский класс/метод (`Animal()`), потом предыдущий родительский (`Mammal()`) и (`Carnivour()`), в конце сам вызываемый класс/метод (`Dog()`). Если в классе `Dog(Mammal, Carnivour)` переставить родительские классы местами, то в данном случае вторим вызовется класс/метод (`Carnivour()`) а потом (`Mammal()`)
```python
# set in animal
# set in carnivour
# set in mammal
# set in dog
```
#### Ещё пример:
```python
class Animal():
    def __init__(self):
        self.health = 100
    def hit(self, damage):
        self.health -= damage
class Carnivour(Animal):
    def __init__(self):
        super().__init__()
        self.legs = 4
c = Carnivour()
c.hit(33)
print (c.health)
# 67
```
Задаем родительский класс `Animal()`, инициализируем в нем начальный атрибут `self.health = 100`. Создаем второй метод для класса `hit()` и задаем ему уравнение отнимания здоровья.
Создаем дочерний класс `Carnivour(Animal)` в котором инициализируем метод `__init__` из родительского класса, для вызова его начального атрибута `self.health = 100`, иначе вызов метода `hit(self, damage)` не найдет аргумент `health`. 
`self.legs = 4` хоть и заданы - не вызываются в данном примере
### Миксины
абыръ!!