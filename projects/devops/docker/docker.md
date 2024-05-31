**посмотреть запущенные контейнеры**
```shell
docker ps
```
**посмотреть все загруженные в хранилище контейнеры**
```shell
docker ps -a
```
**удалить контейнер**
```shell
docker rm nginx-test
```
**Запуск контейнера**
```shell
docker run -d --name nginx-test -p 80:80 -v /var/www/html:/usr/share/nginx/html nginx
```
* `--name nginx-test` - имя контейнера для удобочитаемости и дальнейшего использования в командах при работе с контейнером
* `-p 80:80` - порт хоста: порт докер контейнера
* `-v /var/www/html:/usr/share/nginx/html `- папка хоста: папка в контейнере
* `--restart alwais` - добавляем для самозапуска контейнера при включении хоста

**Посмотреть историю команд при создании контейнера**
```shell
docker history nginx-test
```
**Зайти в баш контейнера**
```shell
docker exec -ti nginx-test bash
```
**посмотреть все нстройки контейнера**
```shell
docker inspect nginx-test
```
**посомтреть логи**
```shell
docker logs nginx-test
```
**посмотреть сети**
```shell
docker network ls
```
* `docker run nginx ` - проброс портов во внутреннюю сеть контейнера (172.17.0.х)
* `docker run --network=none nginx` - полностью изолированный контейнер от сети
* `docker run --network=host nginx` - используется порт хостовой машины