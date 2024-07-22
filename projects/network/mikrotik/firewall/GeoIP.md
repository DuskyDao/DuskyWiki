#mikrotik #geoip #firewall 
### Address list
Скачать **address list** под нужную страну можно на [сайте](https://mikrotikconfig.com/firewall/#address)
![](files/Pasted%20image%2020240624232845.png)
### Добавляем лист в фаервол
#### Для начала загрузим его в RouteOS
![](files/Pasted%20image%2020240624233110.png)
#### Импортируем адрес лист в настройки через New Terminal
```bash
import file-name=IP-Firewall-Address-List.rsc
```
Если всё прошло хорошо, в файрволе, в адрес листах, у нас появятся добавленные адреса которые теперь мы можем использовать в правилах!
![](files/Pasted%20image%2020240624233526.png)
### Добавим блокирующие правила
Запретим доступ со всех стран, кроме отмеченной в первом пункте (в нашем случае - Украины). Для разгрузки процессора добавим его в цепочку RAW
```
/ip firewall raw
add action=drop in-interface-list=WAN log=no log-prefix="" src-address-list=!CountryIPBlocks comment="GeoIP Block" chain=prerouting disabled=yes
```
![](files/Pasted%20image%2020240625000157.png)