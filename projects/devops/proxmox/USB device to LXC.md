### Прокидываем USB устройство в LXC
#### Установим недостающие пакеты в LXC контейнере
Пo умолчанию в LXC контейне не доступны команды `lspci` и `lsusb`
```bash
apt install usbutils pciutils
```
#### В Proxmox смотрим подключенные USB устройства
```bash
root@proxmox:~# lsusb
Bus 002 Device 003: ID 1b3f:2008 Generalplus Technology Inc. USB Audio Device
Bus 002 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 004: ID 0557:2221 ATEN International Co., Ltd Winbond Hermon
Bus 001 Device 003: ID 0ea0:2222 Ours Technology, Inc. 
Bus 001 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

```
Нас интересует USB звуковая карта (смотрим номера шин устройства)
```bash
Bus 002 Device 003: ID 1b3f:2008 Generalplus Technology Inc. USB Audio Device
```
Запомним номера 189 и 130
```bash
ls -l /dev/bus/usb/002/003
crw-rw-r-- 1 root root 189, 130 Nov 19 09:40 /dev/bus/usb/002/003
```
#### Монтируем устройство в LXC контейнер
```bash
nano /etc/pve/nodes/proxmox/lxc/105.conf
```
Где 105 номер нашего контейнера. Добавляем строки взяв информацию выше
```bash
lxc.cgroup.devices.allow: c 189:* rwm   
lxc.mount.entry: /dev/bus/usb/002/003 dev/bus/usb/002/003 none bind,optional,create=file
```
Первый предназначен для предоставления привилегии контейнера доступа к устройству, указанному его старшим и младшим номером.

Примечание. **189:*** означает, что нам важен только старший номер, применяются все младшие. **rwm** означает чтение-запись-монтирование.

Последний предназначен для монтирования представления файла устройства в пространство контейнера. В этом случае я монтирую конкретное устройство, однако было бы неплохо сопоставить весь каталог (со всеми его братьями и сестрами) с контейнером, поскольку более чем вероятно, что имя файла изменится.

Перезапускаем контейнер и смотрим что у нас появилось аудио устройство
```bash
lsusb
Bus 002 Device 003: ID 1b3f:2008 Generalplus Technology Inc. USB Audio Device
Bus 002 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 004: ID 0557:2221 ATEN International Co., Ltd Winbond Hermon
Bus 001 Device 003: ID 0ea0:2222 Ours Technology, Inc. 
Bus 001 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```