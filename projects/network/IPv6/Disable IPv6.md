### Как навсегда отключить IPv6 в Ubuntu
По умолчанию IPv6 включен и используется параллельно с IPv4.
Чтобы отключить IPv6 в вашей системе Ubuntu, [откройте терминал](https://www.makeuseof.com/tag/5-easier-command-line-linux/) , нажав **Ctrl + Alt + T** , и [с помощью редактора nano](https://www.makeuseof.com/install-and-use-gnu-nano-in-linux/) откройте файл конфигурации sysctl:
```
sudo nano /etc/sysctl.conf
```
Теперь добавьте следующий код в файл конфигурации для отключения служб IPv6:
```
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```
В приведенном выше коде:
- Первая строка отключает IPv6 для всех сетевых интерфейсов системы.
- Вторая строка специально нацелена на интерфейс по умолчанию и отключает его.
- Третья строка отключает IPv6 на интерфейсе обратной связи, что означает, что машина не будет использовать IPV6 для внутренней связи.
> для lo шнтерфейса лучше оставить включенным протокол, так как некоторые програмы требуют его работы

![](files/adding-the-specified-lines-to-disable-the-ipv6-services-on-ubuntu.avif)
После добавления данного кода нажмите **Ctrl + O,** чтобы сохранить изменения и выйти из редактора. Теперь примените изменения в системе с помощью:
```
sudo sysctl -p
```
![](files/applying-the-changes-to-the-ubuntu-system.avif)
Перезапустите сетевые службы с помощью:
```
sudo systemctl restart systemd-networkd
```
Проверьте статус отключенных служб IPv6, выполнив:
```
cat /proc/sys/net/ipv6/conf/all/disable_ipv6
```
![](files/checking-the-status-of-ipv6-on-ubuntu.avif)
Если в качестве статуса вывода отображается «1», это означает, что IPv6 был успешно отключен в Ubuntu.
### Как временно отключить IPv6 в Ubuntu
Чтобы временно отключить IPv6 в Ubuntu, выполните следующую команду **systemctl** с опцией **-w** :
```
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
```
![](files/temporarily-disabling-the-ipv6-on-ubuntu-using-the-specified-command.avif)
Теперь перезапустите сетевые службы с помощью:
```
sudo systemctl restart systemd-networkd
```
В результате все зависимости будут настроены на временное отключение служб IPv6.