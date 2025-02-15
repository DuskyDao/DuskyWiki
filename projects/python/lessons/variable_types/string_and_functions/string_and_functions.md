+++
title = 'String_and_functions'
date = 2024-02-20T15:27:48+02:00
draft = false
+++
Строки **(strings)** в Python представляют собой последовательности символов Unicode и используются для хранения текстовой информации. Вот основные характеристики и методы строк в Python:
* **Неизменяемость:** Строки в Python являются неизменяемыми, что означает, что их нельзя изменить после создания. Однако вы можете создать новую строку на основе существующей, применив операции над строками.
* **Индексация:** Строки поддерживают индексацию, то есть доступ к отдельным символам по их позиции в строке. Индексация начинается с нуля.
* **Срезы (slicing):** С помощью срезов вы можете получать подстроки из строки, указывая начальный и конечный индексы. Срез включает начальный индекс, но исключает конечный индекс.
* **Конкатенация:** Строки могут быть объединены с помощью оператора +.
* **Длина строки:** Функция `len()` возвращает количество символов в строке.
* **Методы:** Python предоставляет множество методов для работы со строками, таких как `upper()`, `lower()`, `capitalize()`, `strip()`, `split()`, `join()`, `replace()` и многие другие.
* **Форматирование строк:** Python поддерживает различные способы форматирования строк, включая f-строки, метод format() и модуль string.Template.  
### Примеры использования:
```python
st = ("Hello world, Bitch!!!")

# Подсчет символов len(x) 
print (len(st)) # 21

# Подсчет указанных символов в строке x.count("y")
print (st.count("o")) # 2

# Ставит заглавной первый символ, остальные делает прописными
print (st.capitalize()) # Hello world, bitch!!!

# Переводит все символы строки в заглавные
caps = st.upper()
print (caps) # HELLO WORLD, BITCH!!!
# Проверяет все символы в строке на регистр - БОЛЬШИЕ
print (caps.isupper()) # True

# Переводит все символы строки в прописные
low_st = st.lower()
print (low_st) # hello world, bitch!!!
# Проверяет все символы в строке на регистр - МАЛЕНЬКИЕ
print (low_st.islower()) # True

# Поиск символа в строке - выводит номер от нуля до n
print (st.find("l")) # 2
print (st.find("l", 4)) # 9 начинает искать с 4го символа
print (st.find("0", 4, 10)) # -1 ищет с 4 по 10 символ и ничего не находит

# Проверяет строку на наличие ТОЛЬКО букв и цифр
print ('12346fd!@'.isalnum()) #False
print ('1234634'.isalnum()) #True
# Проверяет строку на наличие ТОЛЬКО букв
print ('1234634'.isalpha()) # False

# Проверяет строку на наличие пробелов (хотя бы одного). В втором случает строка является пустой
print ('    '.isspace()) # True
print (''.isspace()) # False

# Проверяет строку на пустоту
stroka = " "
print (stroka == "") # False (Если  stroka равна "" пустой сткоке то выводит True, но она содержит пробел, потому False)
# Функция strip убирает из строки из начала и конца пробелы
print (stroka.strip(" ") == "") # (True Убирает пробел и ровняет к пустой строке)
# Если передать методу в качестве аргумента какие либо символы, то метод будет убирать их из начала и конца строки:
stroka = ",,,::rrrr:test;dd;,,"
print (f"убираем символы: ,:;.dr c переменной {stroka.strip('.,:;dr')}") # test (убираем символы)
# Так же пожно проверить пустоту строки через if
if not stroka:
    print ("пустая")
else:
    print ("не путсая") # В данном случае не пустая, так как содержит ПРОБЕЛ

# Проверяет строку на наличие УКАЗАННЫХ символов в начале и конце
print (st.startswith("He")) # True
print (st.endswith("tch")) # False  так как в конце есть "!!!"

# str.split(sep=None, maxsplit=-1)
# sep - необязательный параметр, позволяющий задать разделитель вручную. По умолчанию любой пробел является разделителем.
# maxsplit - необязательный параметр, указывающий максимальное количество разбиений, которое требуется выполнить. Если параметр указан,то выполняется не более maxsplit
# разбиений, то есть итоговый список будет содержать не более maxsplit+1 элементов. Если maxsplit не указан или равен -1, то ограничения на количество сплитов нет.
a = 'Пример работы метода split'
print(a.split()) # => ['Пример', 'работы', 'метода', 'split']
num = '1, 2, 3, 4, 5' #Все параметры по умолчанию.
print(num.split()) # => ['1,', '2,', '3,', '4,', '5']
# Указываем разделитель - ', '
print(num.split(', ')) # => ['1', '2', '3', '4', '5']
# Указываем разделитель и ограничиваем количество сплитов.
print(num.split(', ', 2)) # => ['1', '2', '3, 4, 5'] 

# Метод partition возвращает кортеж из строки, разделенной по ПЕРВОМУ совпадению подстроки и указанного в параметре разделителя. 
cat = "speed cat = 60km, cat very slow"
cat_tuple = cat.partition("cat")
print (cat_tuple) # ('speed ', 'cat', ' = 60km, cat very slow') кортеж
```