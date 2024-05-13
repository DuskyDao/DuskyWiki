[Docker](/projects/my_wiki/devops/docker/)
Docker-compose является мощным инструментом для создания и управления Docker-контейнерами. Этот инструмент позволяет создавать, запускать и управлять множеством контейнеров, взаимодействуя друг с другом. В этой статье мы рассмотрим несколько команд, которые могут быть полезны при работе с Docker-compose.  
  
Итак, приступим.  
 

1. **Команды для управления контейнерами:**

- **docker-compose up**: для запуска всех контейнеров, определенных в файле docker-compose.yml. 

> Например, команда `docker-compose up` запустит все контейнеры из файла docker-compose.yml в текущей директории.

- **docker-compose down**: для остановки и удаления всех контейнеров, определенных в файле docker-compose.yml. 

> Например, команда `docker-compose down` остановит и удалит все контейнеры, которые были запущены с помощью файла **docker-compose.yml**.  
 

2. **Команды для управления службами:**
**docker-compose ps**: для отображения списка запущенных контейнеров и их статусов. Например, команда `docker-compose ps` покажет все запущенные контейнеры из файла **docker-compose.yml**.  
 
  ![[files/1.png]]

- **docker-compose up -d [SERVICE]**: для запуска конкретной службы из файла docker-compose.yml. 

        Например, команда "docker-compose up nextcloud" запустит только контейнер nextcloud из файла docker-compose.yml который был остановлен.  
  
![[files/2.png]]
![[files/2-1.png]]
3. **Команды для управления образами:**

- **docker-compose pull:** для загрузки всех образов, определенных в файле docker-compose.yml. Например, команда "docker-compose pull" загрузит все образы, указанные в файле docker-compose.yml.
- **docker-compose build:** для сборки образов, определенных в файле docker-compose.yml. Например, команда "docker-compose build" соберет все образы, указанные в файле docker-compose.yml.

4. **Команды для управления логами:**

- **docker-compose logs**: для просмотра логов всех контейнеров, определенных в файле docker-compose.yml. Например, команда "docker-compose logs" выведет логи всех контейнеров из файла docker-compose.yml.  
![[files/3.png]]
- **docker-compose logs [SERVICE]:** для просмотра логов конкретной службы из файла docker-compose.yml. Например, команда "docker-compose logs nextcloud_db" выведет логи только контейнера базы данных из файла docker-compose.yml.
![[files/4.png]]

5. **Команды для управления контейнерами в работе:**

- **docker-compose stop**: для остановки всех контейнеров, определенных в файле docker-compose.yml, но без их удаления. Например, команда "docker-compose stop" остановит все контейнеры из файла docker-compose.yml.
![[files/5.png]]
- **docker-compose start**: для запуска всех остановленных контейнеров, определенных в файле docker-compose.yml. Например, команда "docker-compose start" запустит все остановленные контейнеры из файла docker-compose.yml.
![[files/6.png]]
- **docker-compose up --force-recreate** заставляет Docker Compose пересоздать контейнеры, даже если их конфигурация или образ не изменился. Это означает, что Compose остановит и удалит существующие контейнеры для указанных в файле docker-compose.yml сервисов, а затем создаст новые контейнеры с нуля, используя последнюю версию образов, определенных в файле. Использование опции **--force-recreate** полезно в ситуациях, когда вы внесли изменения в файл **docker-compose.yml**, которые затрагивают конфигурацию контейнера, например, переменные среды или отображение портов.

6. **Команды для управления сетями:**

- **docker-compose network** **ls**: для отображения списка сетей, определенных в файле docker-compose.yml если они есть. Так же можно воспользоваться командой **docker network** **ls.**
![[files/7.png]]
- **docker-compose network create [NETWORK]:** для создания новой сети в файле docker-compose.yml. Например, команда "docker-compose network create frontend" создаст новую сеть с названием frontend в файле docker-compose.yml.
- **docker-compose network rm [NETWORK]:** для удаления сети, определенной в файле docker-compose.yml. Например, команда "docker-compose network rm frontend" удалит сеть с названием frontend из файла docker-compose.yml.

7. **Команды для управления переменными окружения:**

- **docker-compose config:** для проверки корректности файла docker-compose.yml и отображения всех переменных окружения, определенных в нем. Например, команда "docker-compose config" проверит корректность файла docker-compose.yml и покажет все переменные окружения, определенные в нем.
![[files/8.png]]
- **docker-compose exec [SERVICE] [COMMAND]:** для выполнения команды внутри контейнера, определенного в файле docker-compose.yml. Например, команда "docker-compose exec nextcloud ls" выполнит команду "ls" внутри контейнера nextcloud, который определен в файле docker-compose.yml.

![[files/9.png]]
8. **Команды для управления объемами:**

- **docker-compose volume ls**: для отображения списка всех объемов, определенных в файле docker-compose.yml. Например, команда "docker-compose volume ls" или "**docker volume ls**" покажет все объемы, которые определены в файле docker-compose.yml.
![[files/10.png]]
- **docker-compose volume create [VOLUME]:** для создания нового объема, определенного в файле docker-compose.yml. Например, команда "docker-compose volume create db_data" создаст новый объем с названием db_data в файле docker-compose.yml.
- **docker-compose volume rm [VOLUME]:** для удаления объема, определенного в файле docker-compose.yml. Например, команда "docker-compose volume rm db_data" удалит объем с названием db_data из файла docker-compose.yml.