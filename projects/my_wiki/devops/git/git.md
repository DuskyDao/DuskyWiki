# Инициализация репозитория локального (команду набираем с папки в которой будет лежать репозиторий)
```bash
git init
```
# Проверить статус
```bash
git status
```
# Добавить файл с репозитория в гит
```bash
git add namefile.ty
```
# Добавить все файлы с репозитория
```bash
git add .
```
# Удалить файл с репозитория
```bash
git rm file
```
#Добавить коммит
```bash
git commit -m “Commit name”
```
# Посмотреть в какой мы ветке разработки
```bash
git branch
```
# Создать новую ветку
git branch test
# Удалить ветку
git branch -D test                      
# Переименовать master ветку репозитория в main
git branch -M main            
# Перейти в ветку readmy
git checkout readmy
# Создаем новую ветку и переходим в неё
git checkout -b new
# Обьеденить ветку readmy с основной (нужно находиться в оновной ветке)
git merge readmy

## файл в котором прописываем какие файлы/папки игнорить и не добавлять в репозиторий
.gitignore

# Вводим ник в репозиторие гибхаба
git config –global user.name “Name”
# Вводим почту для инициализации в гитхабе
git config –global user.email “Email”    

# Первое добавление локального репозитория на гитхаб
git remote add origin https://github.com/DuskyDao/first_rep_test.git
git remote     
# Добавляем наш локальный репозиторий на гитхаб
git push -u origin main
> дальше после изменений достаточно этой команды для обновления на гитхаб локального репозитория
git push      

# Добавляем репозиторий с гитхаба на локальный (нужно находиться в папке новой)
git clone https://ссылка из гитхаба
# забрать все обновления с гитхаба в открытый репозиторий
git pull
# Посмотреть последние изменения в репозитории
git log
# сбрасіваем реп до вібранного коммита
git reset --hard <commit>
git reset --hard origin/master
