### Пинг в файл с отображением времени
```powershell
ping.exe -t 10.9.44.120 | foreach{"{0} - {1}" -f (get-date),$_} >> E:\ping\ping120.txt
```