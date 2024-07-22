#python #libraries #logging
Для записи логов вывода скрипта на **Python** в файл можно использовать модуль **logging**. Этот модуль обеспечивает гибкие средства для ведения журнала с различными уровнями важности сообщений.  
Вот пример использования модуля logging для записи вывода скрипта в файл:
```python
import logging

# Конфигурация логгера

logging.basicConfig(filename='app.log', level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Примеры вывода логов

logging.debug('Это сообщение уровня DEBUG')
logging.info('Это сообщение уровня INFO')
logging.warning('Это сообщение уровня WARNING')
logging.error('Это сообщение уровня ERROR')
logging.critical('Это сообщение уровня CRITICAL')
```
Этот код создаст файл **app.log** и запишет в него сообщения разных уровней важности. Уровни важности включают в себя `DEBUG`, `INFO`, `WARNING`, `ERROR` и `CRITICAL`. Вы можете настроить уровень важности для вывода в файл, указав его в функции `basicConfig()`. В приведенном выше примере используется уровень `INFO`, который включает в себя все уровни важности, начиная с `INFO` и выше.  
В результате выполнения этого кода в файле **app.log** будет содержаться вывод скрипта, а также метки времени и уровень важности для каждого сообщения.
#### Давайте создадим простой пример скрипта, который будет генерировать случайные числа и выводить их в лог-файл.
```python
import logging
import random
import time

# Конфигурация логгера
logging.basicConfig(filename='random_numbers.log', level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Функция для генерации случайных чисел и записи их в лог
def generate_random_numbers():
    while True:
        random_number = random.randint(1, 100)
        logging.info(f'Случайное число: {random_number}')
        time.sleep(1)

if __name__ == "__main__":
    generate_random_numbers()

```
В этом примере мы создаем логгер с именем **random_numbers**, который записывает сообщения в файл **random_numbers.log**. Уровень важности установлен на `INFO`, что означает, что будут записаны все сообщения уровня `INFO` и выше (такие как `INFO, WARNING, ERROR и CRITICAL`). Формат сообщений указан как `%(asctime)s - %(levelname)s - %(message)s`, что добавляет метку времени и уровень важности к каждому сообщению.

Функция `generate_random_numbers()` генерирует случайные числа в бесконечном цикле и записывает их в лог с помощью метода `logging.info()`.