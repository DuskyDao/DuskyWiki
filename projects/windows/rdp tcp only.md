## Як вимкнути використання протоколу UDP для RDP сесії

Для вирішення проблеми із зависанням RDP сесій під час використання VPN тунелів ви можете спробувати вимкнути використання протоколу UDP.

Можна вимкнути протокол UDP для RDP через групові політики.

- Відкрийте консоль редактора **локальної GPO (gpedit.msc)**;
- Перейдіть в розділ **Computer Configuration** -> **Administrative Templates** -> **Windows Components** -> **Remoter Desktop Services -> Remote Desktop Session Host -> Connections;**
- Увімкніть параметр політик **Select RDP transport protocols та встановіть Select Transport Type = Use on**ly TCP

![](https://tausoft.com.ua/wp-content/uploads/2024/04/rdp-server-ispolzovat-tolko-tcp-port.png)

- Перезавантажте сервер RDS/RDP, щоб застосувати налаштування;
- Підключіться до сервера RDP і натисніть на піктограму підключення. Тут має з’явитися напис:

![](https://tausoft.com.ua/wp-content/uploads/2024/04/dlya-rdp-podklyucheniya-ispolzuetsya-tolko-tcp.jpg)

Це означає, що для підключення RDP використовується тільки TCP.

Цей спосіб дозволяє вимкнути використання UDP на стороні сервера RDP/RDS. Якщо ви хочете заборонити використовувати UDP для RDP на стороні клієнта, потрібно включити параметр Turn off UDP on Client у розділі **Computer Configuration -> Administrative Templates -> Windows Components -> Remoter Desktop Services -> Remote Desktop Connection Client**.
На ру виндовсе
**Конфигурация комп'ютера => Административные шаблоны => Компоненты виндовс => Службы удаленных рабочих столов => Клиент подключения к удаленному рабочему столу => Отключение UDP на клиенте**

![](https://tausoft.com.ua/wp-content/uploads/2024/04/parametr-politik-otklyuchit-udp-na-kliente-turn-o.png)

Після внесення змін потрібно оновити локальні політики командою gpupdate /force і перезапустити клієнт mstsc.exe.