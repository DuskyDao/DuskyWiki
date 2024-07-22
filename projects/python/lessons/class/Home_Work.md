#python #homeworks
### **ДЗ-1: ФИО**
Создать класс **Name**, который принимает имя и фамилию в качестве аргументов при конструировании.\
Класс должен поддерживать атрибуты:
* `first_name`, возвращающий имя
* `last_name`, возвращающий фамилию
* `full_name`, возвращающий имя и фамилию
* `initials`, возвращающий инициалы
Класс должен приводить переданные имя и фамилию в форму при которой имя и фамилия начинаются с заглавной буквы, а все остальные буквы в нижнем регистре (поскольку вызывающий код может передавать такие строки как "JOHN", 'jOHN', 'sMiTh' и т.д.)

Примеры вызовов:
```python
a1 = Name('john', 'SMITH')
a1.first_name ➞ 'John'
a1.last_name ➞ 'Smith'
a1.full_name ➞ 'John Smith'
a1.initials ➞ 'J.S'
```
**Решение:**
```python
class Name:
    def __init__(self, first_name, last_name):
        self.first_name = first_name.capitalize()
        self.last_name = last_name.capitalize()
        self.full_name = self.first_name + " " + self.last_name
        self.initials = self.first_name[0] + "." + self.last_name[0]
```
### **ДЗ-2: Класс калькулятора**
Создайте класс **Calculator** который поддерживает:
* сложение двух чисел
* вычисление разницы между двумя числами
* умножение двух чисел
* деление одного числа на другое
  
Примеры вызовов и возвратов из функций:
```python
calculator = Calculator()  
calculator.add(10, 5) ➞ 15 
calculator.subtract(10, 5) ➞ 5 
calculator.multiply(10, 5) ➞ 50  
calculator.divide(10, 5) ➞ 2
```
**Решение:**
```python
class Calculator:
    def subtract(self, x, y):
        return x - y
    def add(self, x, y):
        return x + y
    def multiply(self, x, y):
        return x * y
    def divide(self, x, y):
        return x / y
```
### **ДЗ-3: Парсинг сотрудника**
Создать класс **Employee**, который принимает имя, фамилию и зарплату в качестве аргументов при конструировании.\
Класс должен поддерживать:
* атрибут `first_name`, возвращающий имя
* атрибут `last_name`, возвращающий фамилию
* атрибут `salary`, возвращающий зарплату
* функцию `from_string`, которая принимает имя, фамилию и зарплату в формате `'first_name-last_name-salary'`, парсит строку и возвращает экземпляр **Employee**

Примеры вызовов:
```python
emp1 = Employee('Mary', 'Sue', 60000)
emp2 = Employee.from_string('John-Smith-55000')
emp1.first_name ➞ 'Mary'
emp1.salary ➞ 60000
emp2.first_name ➞ 'John'
emp2.salary ➞ 55000
```
Решение задачи:
```python
class Employee:
    # Конструктор класса, инициализирует атрибуты объекта
    def __init__(self, first_name, last_name, salary):
        self.first_name = first_name
        self.last_name = last_name
        self.salary = salary

    # @classmethod - декоратор, указывающий на то, что следующий метод является методом класса
    @classmethod
    # Метод класса from_string, который создает экземпляр класса из строки
    def from_string(cls, stroka):
        # Разбиваем строку по символу '-' и получаем список данных
        data = stroka.split('-')
        # Создаем и возвращаем экземпляр класса с использованием данных из списка
        return cls(data[0], data[1], int(data[2]))
        #Можно ещё так решить
        # first_name, last_name, salary = stroka.split('-')
        # return cls(first_name, last_name, int(salary))
```
### **ДЗ-4 "Пицца"**
Создайте класс **Pizza**, который принимает список ингредиентов.\
Класс поддерживает:
* атрибут `order_number`, который возвращает текущий номер заказа (подсказка: используйте статический атрибут в качестве сквозного счётчика)
* атрибут `ingredients`, который возвращает список, принятый в конструкторе
* функции (`garden_feast`, `hawaiian`, `meat_festival`) создания видов пицц, ингредиенты которых заранее известны (см. таблицу)
  
| Name | Ingredients|
| --- | --- |
|hawaiian | ham, pineapple |
|meat_festival | beef, meatball, bacon |
|garden_feast | spinach, olives, mushroom |

Примеры вызовов:
```python
p1 = Pizza(['bacon', 'parmesan', 'ham'])    # order 1
p2 = Pizza.garden_feast()                  # order 2
p1.ingredients ➞ ['bacon', 'parmesan', 'ham']
p2.ingredients ➞ ['spinach', 'olives', 'mushroom']
p1.order_number ➞ 1
p2.order_number ➞ 2
```
Выполнение задачи:
```python
class Pizza:
    n = 0 #создаем атрибут для счетчика
    def __init__(self, ingredients):
        self.ingredients = ingredients
        Pizza.n += 1 #при инициализации класса прибавляем +1
        self.order_number = Pizza.n
        
    @classmethod
    def hawaiian(cls):
        return cls(['ham', 'pineapple']) #возращаем список ингредиентов
    
    @classmethod
    def garden_feast(cls):
        return cls(['spinach', 'olives', 'mushroom'])
    
    @classmethod
    def meat_festival(cls):
        return cls(['beef', 'meatball', 'bacon'])
```
### **ДЗ-5: Окружность** 
Создайте класс **Circle** который конструируется с передачей радиуса в качестве аргумента.\
Класс Circle должен предоставлять две функции:
* `get_area`, которая возвращает площадь круга
* `get_perimeter`, которая возвращает длину окружности

**Примеры вызовов**
```python
circle = Circle(10)
area = circle.get_area() # возвращает 314.1592653589793
perimeter = circle.get_perimeter() # возвращает 62.83184
```
**Решение задачи**
```python
import math
class Circle:
    def __init__(self, radius):
        self.radius = radius
    def get_area(self):
        return math.pi * pow(self.radius, 2) # S = π × r2
    def get_perimeter(self):
        return math.pi * self.radius * 2 # l=πd д - диаметр
```
### **ДЗ-6: Смузи**
Создать класс **Beverage**, который при конструировании принимает список ингредиентов
* поддерживает атрибут `ingredients`, возвращающий список ингредиентов, переданных при конструировании инстанции класса
* поддерживает функцию `get_cost`, вычисляющую итоговую стоимость всех ингредиентов напитка (получается себестоимость напитка)
* поддерживает функцию `get_price`, вычисляющую цену напитка посредством умножения себестоимости на 2.5
* поддерживает функцию `get_name`, которая возвращает строку, перечисляющую ингредиенты, сортируя их в алфавитном порядке. Если ингредиентов больше одного, то в конце добавляет слово `'Fusion'`, иначе добавляет слово `'Smoothie'`. Эта функция также должна заменять `'berries'` на `'berry'`.

|Ингредиенты|их себестоимость||
|---|---|---|
|Strawberries|$1.50||
|Banana |$0.50||
|Mango|$2.50||
|Blueberries|$1.00||
|Raspberries|$1.00||
|Apple|$1.75||
|Pineapple|$3.50||

**Примеры вызовов**
```python
s1 = Beverage(["Banana"])
s1.ingredients ➞ ["Banana"]
s1.get_cost() ➞ "$0.50"
s1.get_price() ➞ "$1.25"
s1.get_name() ➞ "Banana Smoothie"
s2 = Beverage(["Raspberries", "Strawberries", "Blueberries"])
s2.ingredients ➞ ["Raspberries", "Strawberries", "Blueberries"]
s2.get_cost() ➞ "$3.50"
s2.get_price() ➞ "$8.75"
s2.get_name() ➞ "Blueberry Raspberry Strawberry Fusion"
```
**Решение задания (мое):**
```python
prices = {"Strawberries" : 1.5, "Banana" : 0.5, "Mango" : 2.5,
		"Blueberries" : 1, "Raspberries" : 1, "Apple" : 1.75,
		"Pineapple" : 3.5}
class Beverage:
    def __init__(self, ingredients):
        self.ingredients = ingredients
        self.prices = prices
        self.data = {}
        for k, v in self.prices.items():
            for y in self.ingredients:
                if k == y:
                    self.data[k] = v
    def get_cost(self):
        self.cost = 0
        for x in self.data.values():
            self.cost += x
        return f"${self.cost:.2f}"
    def get_price(self):
        price = self.cost * 2.5
        return f"${price:.2f}"
    def get_name(self):
        name = ""
        key = 0
        for x in sorted(self.data.keys()): #сортируем по алфавиту ключи словаря и перебираем
            key += 1 #ключь для понимания - у нас 1ингредиент или много
            name += f"{x.replace('berries', 'berry')} "
        if key == 1:
            return f"{name}Smoothie"
        else:
            return f"{name}Fusion"
        
cc = Beverage(["Raspberries", "Blueberries", 'Banana', 'Apple', "Strawberries"])
print (cc.data) # {'Strawberries': 1.5, 'Banana': 0.5, 'Blueberries': 1, 'Raspberries': 1, 'Apple': 1.75}
print (cc.get_cost()) # $5.75
print (cc.get_price()) # $14.38
print (cc.get_name()) # Apple Banana Blueberry Raspberry Strawberry Fusion
```
**Более красивое решение:**
### **ДЗ-7: Игра в крестики-нолики**
Написать игру в крестики-нолики используя функции. Для упрощения кода - крестик ходит первый. Выводить игровую сетку можно с помощью вертикальных слешей
> Подсказка - продолжать выводить данные без переноса на новую строку можно с помощью конструкции `print (данные, end=" ")`.

**Решение задачи:**
```python
map = ['-', '-', '-', '-', '-', '-', '-', '-', '-']
win = [(0,1,2), (3,4,5), (6,7,8), (0,3,6), (1,4,7), (2,5,8), (0,4,8), (2,4,6)]
print (f'Добро пожаловать в игру крестики-нолики. Ставте номер ячейки для своего хода:\n| 0 | 1 | 2 |\n| 3 | 4 | 5 |\n| 6 | 7 | 8 |\nКрестик ходит первым!')

def write(mod_map, user = 'X'):
    turn = int(input(f'Время ставить {user}: ')) # ввод значения
    if not mod_map[turn] == '-': # проверяем не введено ли уже в ячейке игрового поля что-то
        print (f'Эта ячейка уже занята, введите {user} в другом месте!')
        write(mod_map, user) # вызываем ввод переменной опять
    mod_map[turn] = user # вносим переменную в список игровой сетки
    paint(mod_map)
    win_comb(user, mod_map)

def paint(mod_map): # функция вывода игровой сетки
    for k, v in enumerate(mod_map):
        if (k+1) % 3 == 0:
            print (f'| {v} |')
        else:
            print (f'| {v}', end=" ")

def win_comb(user, mod_map):
    for combo in win: # распаковка выиграшного списка на триплеты кортежей
        if all((mod_map[i] == user for i in combo)): # проверка выиграшной комбинации, если все значения с кортежа "win" присутствуют в "mod_map" 
            print (f'победил {user}')
            return
        elif mod_map.count('-') == 0: # проверка остались ли '-' 
            print (f'Ничья')
            return
    user_modifi(mod_map, user)

def user_modifi(mod_map, user):
    user = 'O' if user == "X" else "X" # смена очереди
    write(mod_map, user)

write(map)
```
**Выглядит вывод так**
```python
Добро пожаловать в игру крестики-нолики. Ставте номер ячейки для своего хода:
| 0 | 1 | 2 |
| 3 | 4 | 5 |
| 6 | 7 | 8 |
Крестик ходит первым!
Время ставить X: 4
| - | - | - |
| - | X | - |
| - | - | - |
Время ставить O: 8
| - | - | - |
| - | X | - |
| - | - | O |
Время ставить X:
```