#linux #git
**Инициализация репозитория локального (команду набираем с папки в которой будет лежать репозиторий)**
```bash
git init
```
**Проверить статус**
```bash
git status
```
**Добавить файл с репозитория в гит**
```bash
git add namefile.ty
```
**Добавить все файлы с репозитория**
```bash
git add .
```
**Удалить файл с репозитория**
```bash
git rm file
```
**Добавить коммит**
```bash
git commit -m “Commit name”
```
**Посмотреть в какой мы ветке разработки**
```bash
git branch
```
**Создать новую ветку**
```bash
git branch test
```
**Удалить ветку**
```bash
git branch -D test
```                      
**Переименовать master ветку репозитория в main**
```bash
git branch -M main
```            
**Перейти в ветку readmy**
```bash
git checkout readmy
```
**Создаем новую ветку и переходим в неё**
```bash
git checkout -b new
```
**Обьеденить ветку readmy с основной (нужно находиться в оновной ветке)**
```bash
git merge readmy
```

**Файл в котором прописываем какие файлы/папки игнорить и не добавлять в репозиторий**
```bash
.gitignore
```

**Вводим ник в репозиторие гибхаба**
```bash
git config –global user.name “Name”
```
**Вводим почту для инициализации в гитхабе**
```bash
git config –global user.email “Email”
```    

**Первое добавление локального репозитория на гитхаб**
```bash
git remote add origin https://github.com/DuskyDao/first_rep_test.git
```
```bash
git remote
```     
**Добавляем наш локальный репозиторий на гитхаб**
```bash
git push -u origin main
```
**дальше после изменений достаточно этой команды для обновления на гитхаб локального репозитория**
```bash
git push
```

```bash
Добавляем репозиторий с гитхаба на локальный (нужно находиться в папке новой)
```
```bash
git clone https://ссылка из гитхаба
```
**Забрать все обновления с гитхаба в открытый репозиторий**
```bash
git pull
```
**Посмотреть последние изменения в репозитории**
```bash
git log
```
**сбрасіваем реп до вібранного коммита**
```bash
git reset --hard <commit>
git reset --hard origin/master
```
