#proxmox #mikrotik 

**взято** [тут](https://www.youtube.com/watch?v=WR2j0aXUfj4)
### Создаем виртуальную машину
![](files/Pasted%20image%2020240613221728.png)
без диска
![](files/Pasted%20image%2020240613221855.png)
выбираем тип носителя
![](files/Pasted%20image%2020240613222030.png)
удаляем диск, мы добавим готовый образ
![](files/Pasted%20image%2020240613222206.png)
указываем цпу и тип 
![](files/Pasted%20image%2020240613222303.png)
указываем количество оперативной памяти с минимальным значением
![](files/Pasted%20image%2020240613223038.png)
указываем сетевые интерфейсы
### Добавляем сетевые интерфейсы к созданной машине
![](files/Pasted%20image%2020240613223723.png)
![](files/Pasted%20image%2020240613223622.png)
### Качаем образ ROuteOS
Переходим на оф сайт и в вкладке загрузок копируем ссылку на RAW образ (ссылку вставим в терминале Proxmox)
![](files/Pasted%20image%2020240613224031.png)
Переходим в терминал Proxmox (не виртуальной машины)
```bash
mkdir /mnt/mikrotik && cd /mnt/mikrotik
wget https://download.mikrotik.com/routeros/7.15.1/chr-7.15.1.img.zip
unzip chr-7.15.1.img.zip
```
Добавляем образ в хранилище
```
qm importdisk 102 chr-7.15.1.img bigdata --format qcow2 
```
- *102* номер виртуальной машини микротика
- *chr-7.15.1.img* файл образа скачанный
- *bigdata* имя пула куда будет перемещён образ
- *--format qcow2* формат диска операционной системы

Переходим в настройки виртуальной машины и инициализируем добавленный образ
![](files/Pasted%20image%2020240613225540.png)
Edit => Add
![](files/Pasted%20image%2020240613225651.png)
Перемещаем добавленный диск в самый верх приоритета загрузки
![](files/Pasted%20image%2020240613230036.png)
Можно стартовать машину!
###
###
### Лицензии
сменить айдишник
```
/system license generate-new-id
```
Для активации лицензии на 60дней (нужен чистый айди)
```
/system license renew
```