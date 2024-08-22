# Отключение протоколов NetBIOS и LLMNR в Windows с помощью GPO

Широковещательные протоколы **NetBIOS over TCP/IP** и **LLMNR** являются устаревшими и в большинстве современных сетей они используются только для совместимости с legacy версиями Windows. Оба протокола подвержены спуфингу и атакам MITM. В том же Metasploit есть готовые модули, позволяющие легко использовать уязвимости в широковещательных протоколах NetBIOS и LLMNR для перехвата учетных данных пользователей в локальной подсети (в т.ч. можно получить хэши [NTLMv2](https://winitpro.ru/index.php/2019/03/13/otklyuchenie-ntlm-autentifikacii-v-domene-ad/)). Для повышения безопасности необходимо отключать эти старые протоколы в доменной сети. Разберемся, как отключить протоколы LLMNR и NetBIOS в Windows 10/Windows 2016 вручную или через групповые политики.

Содержание:

- [Протокол LLMNR](https://winitpro.ru/index.php/2017/08/21/otklyuchenie-netbios-cherez-tcpip-i-llmnr-v-domene-s-pomoshhyu-gpo/#h2_1)
- [Протокол NetBIOS over TCP/IP](https://winitpro.ru/index.php/2017/08/21/otklyuchenie-netbios-cherez-tcpip-i-llmnr-v-domene-s-pomoshhyu-gpo/#h2_2)
- [Отключение протокола LLMNR с помощью GPO](https://winitpro.ru/index.php/2017/08/21/otklyuchenie-netbios-cherez-tcpip-i-llmnr-v-domene-s-pomoshhyu-gpo/#h2_3)
- [Отключение протокола NetBIOS over TCP/IP в Windows 10](https://winitpro.ru/index.php/2017/08/21/otklyuchenie-netbios-cherez-tcpip-i-llmnr-v-domene-s-pomoshhyu-gpo/#h2_4)
- [Как отключить NetBIOS через групповые политики?](https://winitpro.ru/index.php/2017/08/21/otklyuchenie-netbios-cherez-tcpip-i-llmnr-v-domene-s-pomoshhyu-gpo/#h2_5)

## Протокол LLMNR

**LLMNR** (UDP/5355, Link-Local Multicast Name Resolution — механизм широковещательного разрешения имен) – протокол присутствует во всех версиях Windows, начиная с Vista и позволяет IPv6 и IPv4 клиентам разрешать имена соседних компьютеров без использования DNS сервера за счет широковещательных запросов в локальном сегменте сети L2. Этот протокол также автоматически используется при недоступности DNS (в рабочих группах Windows этот протокол используется для [сетевого обнаружения](https://winitpro.ru/index.php/2018/04/23/windows-10-ne-vidit-kompyutery-v-setevom-okruzhenii/)/Network Discovery). Соответственно, при работающих DNS-серверах в домене, этот протокол абсолютно не нужен.

## Протокол NetBIOS over TCP/IP

**Протокол NetBIOS over TCP/IP** или NBT-NS (UDP/137,138;TCP/139) – является широковещательным протоколом-предшественником LLMNR и используется в локальной сети для публикации и поиска ресурсов. Поддержка NetBIOS over TCP/IP по умолчанию включена для всех интерфейсов во всех версиях Windows.

В Windows вы можете вывести статистику протокола NetBIOS и текущий подключений TCP/IP по NBT с помощью команды nbtstat. Чтобы по IP адресу получить имя компьютера, выполните:

`nbtstat -A 192.168.31.90`

![nbtstat обнаружение соседних компьютеров через netbios](https://winitpro.ru/wp-content/uploads/2017/08/nbtstat-obnaruzhenie-sosednih-kompyuterov-cherez-net.png)

Как вы видите, утилита с помощью NetBIOS обнаружила в локальной сети компьютер и вернула его имя.

Можете вывести все записи о соседних компьютерах в локальных сети в кэше NetBIOS:

`nbtstat -c`

Протоколы **NetBIOS** и **LLMNR** позволяют компьютерам в локальной сети найти друг друга при недоступности DNS сервера. Возможно они и нужны в рабочей группе, но в доменной сети оба этих протокола можно отключить.

**Совет**. Перед массовым внедрением данных политики в домене, настоятельно рекомендуем протестировать работу компьютеров с отключенными NetBIOS и LLMNR на тестовых группах компьютеров и серверах. И если проблем с отключением LLMNR обычно нет, то отключение NetBIOS может парализовать работу устаревших систем

## Отключение протокола LLMNR с помощью GPO

Можно отключить LLMNR на компьютере Windows локально через [реестр](https://winitpro.ru/index.php/2017/02/07/rabotaem-s-reestrom-windows-cherez-powershell/) с помощью следующей команды PowerShell:

`New-Item  "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT" -Name DNSClient  -Force   New-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" -Name EnableMultiCast -Value 0 -PropertyType DWORD  -For` ce

В доменной среде широковещательные запросы LLMNR на компьютерах и серверах домена проще отключить с помощью групповой политики. Для этого:

1. В консоли `GPMC.msc` [создайте новую или отредактируйте имеющуюся политику GPO](https://winitpro.ru/index.php/2022/11/15/gpmc-upravlenie-gruppovymi-politikami-active-directory/), применяемую ко всем рабочим станциям и серверам;
2. Перейдите в раздел **Computer Configuration -> Administrative Templates -> Network -> DNS Client;**
3. Включите политику **Turn off smart multi-homed name resolution**, изменив ее значение на **Enabled;**  
    ![отключение протокола LLMNR через GPO Turn off smart multi-homed name resolution](https://winitpro.ru/wp-content/uploads/2017/08/otklyuchenie-protokola-llmnr-cherez-gpo-turn-off-smar.png)
4. Дождитесь [обновления параметров GPO](https://winitpro.ru/index.php/2020/07/09/obnovlenie-gruppovyx-politik-windows/) на клиентах или обновите их вручную командой `gpupdate /force` .

Либо вы можете [через GPP распространить на компьютеры домена параметр реестра](https://winitpro.ru/index.php/2016/01/19/nastrojka-klyuchej-reestra-s-pomoshhyu-gruppovyx-politik/) EnableMulticast = 0 (HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient).

## Отключение протокола NetBIOS over TCP/IP в Windows 10

**Примечание**. Протокол NetBIOS может использовать старыми версиями Windows и некоторыми не-Windows системами, поэтому процесс его отключения в конкретной среде стоит тестировать.

Вы можете отключить NetBIOS в Windows вручную:

1. Откройте свойства сетевого подключения;
2. Выберите протокол **TCP/IPv4** и откройте его свойства;
3. Нажмите кнопку **Advanced**, затем перейдите на вкладку **WINS** и выберите опцию **Disable NetBIOS over TCP** (Отключить NetBIOS через TCP/IP);
4. Сохраните изменения.

![отключить netbios в свойствах сетевого адаптера windows](https://winitpro.ru/wp-content/uploads/2017/08/otklyuchit-netbios-v-svojstvah-setevogo-adaptera-wi.png)

Если у вас на компьютере несколько сетевых интерфейсов (или [отдельных VLAN](https://winitpro.ru/index.php/2020/03/17/nastrojka-vlan-interfejsov-v-windows/)), нужно будет отключить NetBIOS в свойствах каждого их них.

Вы можете проверить статус NetBIOS over TCP/IP для сетевых адаптеров из командной строки Windows:

`ipconfig /all |find "NetBIOS"`

NetBIOS over Tcpip. . . . . . . . : Disabled

![проверить, что netbios отключен NetBIOS over Tcpip. . . . . . . . : Disabled](https://winitpro.ru/wp-content/uploads/2017/08/proverit-chto-netbios-otklyuchen-netbios-over-tcpip.png)

Отключить поддержку NetBIOS для конкретного сетевого адаптера можно и из реестра. Для каждого сетевого адаптера компьютера есть отдельная ветка с его TCPIP_GUID внутри **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NetBT\Parameters\Interfaces**.

Чтобы отключить NetBIOS для конкретного сетевого адаптера, нужно открыть его ветку и изменить значение параметра **NetbiosOptions** на **2** (по умолчанию значение – 0).

![отключение netbios через реестр](https://winitpro.ru/wp-content/uploads/2017/08/otklyuchenie-netbios-cherez-reestr.png)

На клиентах домена, получающих IP адреса [с DHCP на Windows Server](https://winitpro.ru/index.php/2015/11/10/nastrojka-dhcp-servera-s-pomoshhyu-powershell/), вы можете отключить NetBIOS через настройку опций DHCP сервера.

1. Для этого откройте консоль `dhcpmgmt.msc` и выберите настройки зоны Scope Option (или сервера – Server Options);
2. Перейдите на вкладку **Advanced**, в выпадающем списке Vendor class выберите **Microsoft Windows 2000 Options;**
3. Включите опцию **001 Microsoft Disable Netbios Option** и измените ее значение на **0x2.**

![Отключение NetBios ддя клиентов DHCP опцией 001 Microsoft Disable Netbios Option](https://winitpro.ru/wp-content/uploads/2017/08/otklyuchenie-netbios-ddya-klientov-dhcp-opciej-001-mi.png)

## Как отключить NetBIOS через групповые политики?

В [редакторе групповых политик](https://winitpro.ru/index.php/2015/10/02/redaktor-gruppovyx-politik-dlya-windows-10-home-edition/) или [последней версии административных шаблонов](https://winitpro.ru/index.php/2021/02/03/obnovlenie-ustanovka-admx-shablonov-gpo/) для Windows 10/Windows Server 2019 нет отдельного параметра, позволяющего отключить протокол NETBIOS over TCP/IP для всех сетевых адаптеров компьютера. Чтобы отключить NETBIOS для всех адаптеров компьютера воспользуйтесь таким [логон скриптом PowerShell](https://winitpro.ru/index.php/2017/09/01/zapusk-logon-skriptov-powershell-s-pomoshhyu-gpo/).

`$regkey = "HKLM:SYSTEM\CurrentControlSet\services\NetBT\Parameters\Interfaces"   Get-ChildItem $regkey |foreach { Set-ItemProperty -Path "$regkey\$($_.pschildname)" -Name NetbiosOptions -Value 2 -Verbose}`

Сохраните этот код в файл disableNetbios.ps1, скопируйте его в каталог вашей GPO и запускайте на клиентах через **Computer Configuration -> Policies -> Windows Settings -> Scripts -> Startup- > PowerShell Scripts.**

Если на клиентах [настройки политики выполнения PowerShell](https://winitpro.ru/index.php/2020/06/03/powershell-execution-policy-zapusk-scriptov/) блокируют запуск этого скрипта, нужно [подписать PS1 скрипт](https://winitpro.ru/index.php/2016/11/17/kak-podpisat-skript-powershell-sertifikatom/) или запускать его в режиме –bypass.

![групповая политика для отключения NetBios в Windows с помощью скрипта PowerShell](https://winitpro.ru/wp-content/uploads/2017/08/gruppovaya-politika-dlya-otklyucheniya-netbios-v-window.png)

**Примечание**. Для вступления изменений в силу, нужно отключить/включить сетевые адаптеры или перезагрузить компьютер.

Затем откройте командную строку и проверьте, что NetBIOS отключен для ваших сетевых адаптеров (кроме туннельных интерфейсов):

`wmic nicconfig get caption,index,TcpipNetbiosOptions`

![проверить, что netbios отключен wmic nicconfig get caption,index,TcpipNetbiosOptions](https://winitpro.ru/wp-content/uploads/2017/08/proverit-chto-netbios-otklyuchen-wmic-nicconfig-get.png)

---

![Предыдущая статья](https://winitpro.ru/wp-content/themes/lite/img/arrow-left.PNG)[Предыдущая статья](https://winitpro.ru/index.php/2017/08/18/sozdanie-obraza-vosstanovleniya-sistemy-dart-10/) [Следующая статья](https://winitpro.ru/index.php/2017/08/24/oshibka-ustanovki-setevogo-printera-0x00000002/) ![Следующая статья](https://winitpro.ru/wp-content/themes/lite/img/arrow-rigth.PNG)