#python #class
### **Статические атрибуты**
Можно выводить как через класс напрямую, так и через вызов класса
```python
class StaticTest:
    x = 'SuperStatic'
```
можно вывести аргумент класса 2мя способами
```python
xx = StaticTest()
print (f'Можно вывести так {xx.x}') # SuperStatic
print (f'или через класс {StaticTest.x}') # SuperStatic
```
если задать аргументу новое значение (аргумент) на уровне инстанции мы получим изменения только при выводе аргумента через сам аргумент, где вывод через класс останется неизменным
```python
xx.x = 10
print (f'Можно вывести так {xx.x}') # 10 создает атрибут на уровне инстанции
print (f'или через класс {StaticTest.x}') # SuperStatic
```
если изменить аргумент через вызов класса, то аргумент на уровне инстанции не изменится, а вот на уровне класса - да!
```python
StaticTest.x = 20
print (f'Можно вывести так {xx.x}') # 10 
print (f'или через класс {StaticTest.x}') # 20 теперь перезаписывает на уровне класса
```