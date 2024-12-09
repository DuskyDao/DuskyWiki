#linux #systemd #systemctl #mount
### Монтирование SMB/CIFS с помощью systemd
После установки NAS и настройки qBittorrent я искал эффективные способы монтирования SMB/CIFS шары в своей VM под управлением debian linux, лучшим способом было бы использование fstab, но этот вариант не подошел из-за особенностей загрузки VM и сервисов TrueNAS (коротко: сеть в VM появлялась раньше, чем была готова шара на самом NAS), нужно было решение которое бы монтировало каталог при первой возможности, и им стал systemd.

Для начала создадим директорию в которую будем монтировать шару:
```shell
sudo mkdir /mnt/share
```
Теперь запишем логин и пароль от шары в файл:
```shell
sudo nano /root/.smbcredentials
```

Синтаксис credentials файла:

```ini
username=MyUsername
password=MyPassword

# или (при доменной авторизации)
# username=MyUsername@MyDomain
# password=MyPassword

# или (для старых windows)
# username=MyDomain/MyUsername
# password=MyPassword
```

Далее нужно создать юнит отвечающий за mount point:

```shell
sudo nano /etc/systemd/system/mnt-share.mount
```

ВАЖНО!!!

Файл юнита должен называться так же как путь к директории в которую требуется смонтировать шару, где все слеши заменены на дефис и иметь расширение ".mount". В примере выше каталог для монтирования "/mnt/share"

Содержимое файла юнита:

```ini
[Unit]
Description=Share mount
Requires=network-online.target
After=network-online.service
Conflicts=umount.target
Before=umount.target

[Mount]
Type=cifs
What=//192.168.1.2/share
Where=/mnt/share
Options=uid=0,gid=0,file_mode=0777,dir_mode=0777,vers=3.0,credentials=/root/.smbcredentials
TimeoutSec=5

[Install]
WantedBy=multi-user.target
```

Теперь необходимо создать "watcher" unit, он будет автоматически пытаться присоединить директорию при первой возможности:

```shell
sudo nano /etc/systemd/system/mnt-share.automount
```

```ini
[Unit]
Description=Share automount
Requires=network-online.target

[Automount]
Where=/mnt/share
TimeoutIdleSec=30

[Install]
WantedBy=remote-fs.target
```

Все, можно пробовать перезапускать systemd запускать монтирование:

```shell
systemctl daemon-reload
systemctl start mnt-share.mount
```

Если все прошло успешно, ставим юниты в автозапуск:

```shell
systemctl enable mnt-share.mount
systemctl enable mnt-share.automount
```
[Источник](https://iteelion.ru/blog/admin/mount-smb-cifs-with-systemd-linux/)