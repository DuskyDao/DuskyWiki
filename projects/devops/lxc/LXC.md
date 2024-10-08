#linux #container #lxc
### Контейнер LXC для веб-разработки как альтернатива Docker
Разработкой [LXC](https://linuxcontainers.org/lxc/introduction/) занимается компания Canonical, последняя версия LXC 4.0.10 вышла совсем недавно в _июле 2021_, а началась в 2008.

В чем разница LXC и Docker:

- Docker - это контейнер для упаковки одного процесса или службы;  
    на практике Docker это пачка легких контейнеров для упаковки одного веб-сервиса;
    
- LXC - это легкий контейнер на один веб-сервис или сайт, включающий все службы, которые нужны для его функционирования.
    

В каких случаях удобно применять LXC:

- Legacy: у вас уже есть веб-сервис/сайт размещенный на выделенном сервере и нужно упаковать его в форме контейнера, который можно отдать разработчикам, либо перенести на другой сервер (резервный или stage);
    
- Вам нравится работать в окружении, где все службы собраны в одном контейнере;
    
- Нужна изоляция на сервере чтобы каждый веб-сервис находился в своем контейнере, но при этом не терять производительность как в случае с виртуальными машинами;
    
- Если вы умеете настраивать основные сервисы в Linux, значит вы сможете создать LXC контейнер под ваш проект. LXC очень похож на обычную виртуалку.
    

**Наш опыт и небольшая победа над Legacy**  
  
Преобразовать наш существующий массивный веб-проект в форме LXC-контейнера оказалось проще в форме LXC.

Незадолго до начала пандемии, начали искать решение для удобства дистанционной работы. Было очевидно, что удобнее развернуть окружение на своем рабочем компьютере, если это делается одной командой. До контейнеров в компании мы пользовались отдельным сервером для разработки с расшаренными директориями и виртуальными хостами nginx под каждого разработчика.  
  
Docker не подошел, т.к. пришлось бы настроить и наладить с десяток контейнеров, хотелось получить результат быстрее.  
  
В форме LXC образа удалось за несколько дней все упаковать, образ очень похож на обычный виртуальный сервер, куда были установлены нужные версии программ, а данные скопированы. За несколько недель продакшен и разработка перешла на LXC.

Как побочный эффект, получили:

- способ развернуть stage-окружение для тестирования;
    
- живую резервную копию сервера, куда настроили репликацию БД и синхронизацию файлов;
    
- легкий переезд на другой сервер, когда понадобилось переехать в другой дата-центр.
    

  
**Порог вхождения отсутствует**

Чтобы настроить окружение под веб-проект из нескольких служб (mysql, nginx, php-fpm, memcached, postfix) дополнительных знаний не требуется. Видео-демонстрация: [как создать контейнер установить nginx и php-fpm](https://www.youtube.com/watch?v=8va135GTjQw&t=71s) за несколько минут.

Все что нужно знать уместится в этот пост:

Установка LXC под Ubuntu: `apt install lxc`

```bash
# 1. создать контейнер с именем myapp
sudo lxc-create -t download -n myapp -- -d ubuntu -r focal -a amd64

# 2. посмотреть список контейнеров
sudo lxc-ls -f

# 3. запустить контейнер
sudo lxc-start myapp

# 4. остановить
sudo lxc-stop myapp

# 5. зайти в shell контейнера
sudo lxc-attach myapp

# 6. посмотреть файловую систему контейнера
ls -al /var/lib/lxc/myapp/rootfs
```

Для разработки каталог с исходными файлами проекта пробрасываем с хоста в контейнер.

Как пробросить (и особенность настройки прав)

**Резюме по LXC**

- Низкий порог вхождения: можно создать свой контейнер за минуты;
    
- хорошо подходит для упаковки существующих проектов и для legacy;
    
- если нужно посмотреть чей-то веб проект, установить определенные версии mysql/nginx или другие программы, при этом не затрагивая основную систему;
    
- он легок, экономит время при отладке и настройке;
    
- проверено работает при наличии sudo, хотя фича unprivileged контейнеров есть;
    
- классно сочетается с zfs, в этом случае получаем мгновенные снэпшоты и дополнительную надежность.
    

**Когда Docker может быть лучше**

- Вам нужен IaC. Dockerfile это отраслевой стандарт.  
    В LXC похожее отсутствует. Концепция _Infrastructiure as Code_ мне очень близка, я попробовал создать shell-скрипт который является аналогом Dockerfile чтобы [воспроизводит LAMP окружение на LXC](https://github.com/agorlov/lxc-lamp/blob/main/lxc-lamp.sh), но с помощью скрипта не обновить одной командой продакшен.
    
- Если нужен готовый публичный образ какой-то программы или опубликовать свой образ.