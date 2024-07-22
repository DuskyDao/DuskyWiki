#python #class
### Наследование классов. 
Создадим class `OldMen()` c простым выводом текста при вызове как класса так и его методов:
```python
class OldMen():
    def __init__(self):
        print ('Начало функции')
    def first(self):
        print ('Первый метод')
    def end(self):
        print ('Второй метод')
```
И class `Men()` который будет унаследовать `OldMen()`:
```python
class Men(OldMen):
    def __init__(self, weight, height):
        OldMen.__init__(self)
        self.weight = weight
        self.height = height
        print ('Конец функции')
```
если вызвать класс `Меn()` с аргументами `(22,33)` получим его вывод:
```python        
human = Men(22,33)
#Конец функции
```
Но мы также можем вызывать все методы и аргументы с родительского класса, более того - обращаться к методам и аргументам родительского класса прямо в унаследованном:
```python
class Men(OldMen):
    def __init__(self, weight, height):
        OldMen.__init__(self) # Начало функции
        self.weight = weight
        self.height = height
        print ('Конец функции') # Конец функции
        OldMen.end(self) # Второй метод
        print (OldMen.first(self)) # None
human = Men(22,33)
human.first() # Певвый метод
# Начало функции
# Конец функции
# Второй метод
# Певвый метод
# None
# Певвый метод
```
### Переопредиление методов
Мы так же можем переопределять у унаследовательного класса методы и аргументы родительского
```python
class OldMen():
    def __init__(self):
        print ('Начало функции')
    def first(self):
        print ('Певвый метод')
    def end(self):
        print ('Второй метод')
class Men(OldMen):
    def __init__(self, weight, height):
        self.weight = weight
        self.height = height
    def first(self):
        return self.height + 1
    def end(self):
        return self.weight * self.height
human = Men(22,33)
print (human.first()) # 34
print (human.end()) # 726
```
### Полиморфизм
Если методы повторяются в разных классах их можно вызывать одновременно, обращаясь к ним обоим сразу. Для примера создадим ещё 1н класс который унаследуем методы с `OldMen`
```python
class Women(OldMen):
    def __init__(self, long, age):
        self.long = long
        self.age = age
    def first(self):
        return self.long ** 2
    def end(self):
        return 'Old Women'
```
Мы видим что методы `first(self):` и `end(self):` дублируются что в классе `class Men(OldMen)`, что в `class Women(OldMen)`. Можем к ним обратиться таким образом
```python        
for shape in [Men(22,33), Women(140, 43)]:
    shape.first()
# 25600
# Old Women
```


