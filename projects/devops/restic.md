#linux #rectic
#### Установка
1. Ставим **scoop** для установки **restica**
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```
дальше ставим сам **restic**
```powershell
scoop install restic
```
2. Качаем с гита  зип архив, разархивируем в диск С, переменовываем для удобства в restic.exe. Создаем переменную окружения advanced system ( свойства системы ) > переменные среды, добавляем в Patch новую запись с папкой рестика. Теперь можно вызывать рестик с повершела или смд.
#### Синтаксис:
`restic [параметр]--help` (справка по командам)
`restic version` (посмотреть версию)
`restic self-update` (обновить)
Для начала нужно создать репозиторий (папку где будут лежать бекапы) 
```
restic init -r E:/rep_backup_files
```
#### Создать снапшот (бекапить будем папку С:/test): 
```
restic -r C:/rep_backup_files backup E:/files
```
* путь прописанный в *C:/restic/file.txt*
```
restic -r C:/rep_backup_files backup E:/files --files-from=”C:/restic/file.txt”
```
* кроме папки *Stuff*
```
restic -r C:/rep_backup_files backup C:/test --exclude C:/test/Stuff
```
* в файле *C:/restic/file.txt* прописываем путь * к папке которую не нужно бекапить
```
restic -r C:/files backup C:/test--exclude-file=”C:/restic/file.txt”
```