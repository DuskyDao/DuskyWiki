#windows #rdp-wrapper
#### RDP Wrapper удалён антивирусом.
Нужно восстановить стандартный DLL в реестре с помощью одной команды от администратора:
reg add HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters /v ServiceDll /t REG_EXPAND_SZ /d "%SystemRoot%\system32\termsrv.dll" /f

Если не работает (Service status = unknown), то нужно восстановить реестр службы от чистой ОС.:
Windows 10 21H1 здесь https://t.me/rdpwrap/38026
Windows 11 21H2 здесь https://t.me/rdpwrap/40042
Windows 10 LTSC 1809 здесь https://t.me/rdpwrap/41015

 Восстановление ветки реестра из резервной копии:
1. Загрузите файл .reg
2. Откройте его в программе по-умолчанию (двойной клик ЛКМ)
3. Согласитесь с внесением изменений в реестр
4. Перезагрузите систему.

После выполнения данной инструкции нужно заново установить RDP Wrapper.
> Обновить \*.ini файл можно [тут](https://raw.githubusercontent.com/sebaxakerhtc/rdpwrap.ini/master/rdpwrap.ini)
> Download [sebaxakerhtc](https://github.com/sebaxakerhtc/rdpwrap/releases)
#### правильная установка
1. Download RDP Wrapper 1.6.2 and extract this to any directory.
2. Place `rdpwrap.ini` in the same directory.
3. Open CMD as administrator
4. Execute this commands
```
cd C:\path\to\directory\
rdpwinst -u -k
rdpwinst -i
```
5. Enjoy!