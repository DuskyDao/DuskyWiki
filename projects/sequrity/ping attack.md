Для проверки настроек файрвола на MikroTik с использованием примера ICMP Flood атаки, вы можете использовать утилиты, такие как `hping3` или `nping`, чтобы сгенерировать трафик, имитирующий DoS атаку. Эти инструменты можно запустить на другой системе, чтобы атаковать маршрутизатор и проверить, как он реагирует на такой трафик.

### Вариант 1: Использование `hping3`
#### Установка `hping3` на Linux
```bash
sudo apt-get update sudo
apt-get install hping3
```
#### Генерация ICMP Flood атаки с `hping3`
```bash
sudo hping3 -1 --flood <IP-адрес-вашего-MikroTik>
```
- `-1`: Указывает на использование ICMP (ping).
- `--flood`: Отправляет пакеты с максимальной скоростью.
- `<IP-адрес-вашего-MikroTik>`: IP-адрес вашего MikroTik маршрутизатора.

### Вариант 2: Использование `nping` (часть пакета `nmap`)
#### Установка `nmap` на Linux
```bash
sudo apt-get update
sudo apt-get install nmap
```
#### Генерация ICMP Flood атаки с `nping`
```
sudo nping --icmp --icmp-type echo-request --rate 1000 <IP-адрес-вашего-MikroTik>
```
- `--icmp`: Указывает на использование ICMP.
- `--icmp-type echo-request`: Указывает на использование ICMP Echo Request (ping).
- `--rate 1000`: Отправляет пакеты со скоростью 1000 пакетов в секунду.
- `<IP-адрес-вашего-MikroTik>`: IP-адрес вашего MikroTik маршрутизатора.