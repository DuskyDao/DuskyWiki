#mikrotik #cli
### Структура меню
Ввиду того, что существует огромное количество возможных команд, они разбиты по иерархически организованным группам. В зависимости от того в каком месте иерархии происходит управление, будут доступны разные команды и разные наборы дальнейших путей для дальнейшего перемещения по структуре. Такой принцип организации структуры называется "контекстно-ориентированным". Самая верхняя точка меню называется "корень иерархической структуры" или просто "корень".  
Пример доступных путей и команд из корня консоли:
```shell
[admin@MikroTik] > 
caps-man     interface  partitions  routing        user    password
certificate  ip         port        snmp           beep    ping    
console      ipv6       ppp         special-login  blink   quit    
disk         log        queue       system         export  redo    
file         mpls       radius      tool           import  undo 
```
Пример доступных путей и команд из раздела /ip:

```
[admin@MikroTik] /ip >
address      dhcp-server  kid-control  route     ssh           export
arp          dns          neighbor     service   tftp          
cloud        firewall     packing      settings  traffic-flow  
dhcp-client  hotspot      pool         smb       upnp          
dhcp-relay   ipsec        proxy        socks     vrf  
```         

Пример доступных путей и команд из раздела /ip/address:

[admin@MikroTik] /ip/address>
add  comment  disable  edit  enable  export  find  print  remove  reset  set       

В оригинальном отображении командной строки для разных элементов используются разные цвета. Пример:
```
[admin@MikroTik] > /system/clock set time-zone-name=Europe/Moscow
```
В приведенном выше примере:

    /system/clock – путь,
    set – команда,
    time-zone-name= – параметр, который необходимо задать,
    Europe/Moscow – значение параметра, которое должно быть задано.

Чем между собой различаются путь, команда, параметр и значение параметра мы разберем далее в этой статье. Формально имя пути и команда это не одно и тоже. Но для облегчения изучения материала под термином "команда" мы будем подразумевать одновременно оба термина: "имя пути" и "команда".

Структуры интерфейса командной строки (CLI) и графического интерфейса (GUI) чаще всего совпадают, но иногда встречаются и различия. Различия могут заключаться, как в написании отдельных слов, так и в указании полного пути до интересующего раздела с настройками.



### Разница между командными строками RouterOS v6 и RouterOS v7
Командные строки RouterOS v6 и RouterOS v7 идентичны. Единственная разница заключается в формате записи пути. В RouterOS v6 при указании пути разные иерархические уровни разделяются пробелами, а в RouterOS v7 – с помощью слэша (/). Разница касается только указания пути. Сами команды записываются одинаково в обеих версиях операционной системы.  
Пример указания пути и создания правила брандмауэра в RouterOS v6:
```cmd
[admin@MikroTik] /ip firewall filter> add action=accept chain=forward connection-state=established,related
```
Пример указания пути и создания правила брандмауэра в RouterOS v7:
```cmd
[admin@MikroTik] /ip/firewall/filter> add action=accept chain=forward connection-state=established,related
```
В обоих приведенных выше примерах до знака > указан путь, а после команда. Поэтому различается только левая часть, которая содержит в себе запись пути.  
Далее в этой статье примеры будут приводиться в формате записи для RouterOS v7. Но любая информация из статьи применима и для консоли RouteroOS v6. 
### Добавить `ether` интерфейсы в бридж кроме `ether49`
```r
 :foreach i in=[/interface find where (name~"^ether" && name!="ether49")] do={/interface bridge port add interface=$i bridge=bridge.ceha}
```

:foreach i in=[/interface find where (name~"^ether")] do={/interface bridge port add interface=$i bridge=bridge.cold}