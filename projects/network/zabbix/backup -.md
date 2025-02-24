backup
я себе накидал такой мануал и придерживаюсь его ( все работает)
Установка

rpm -ivh https://repo.postgrespro.ru/pg_probackup/keys/pg_probackup-repo-centos.noarch.rpm
yum install pg_probackup-13
yum install pg_probackup-13-debuginfo
создаем инстансы
pg_probackup-13 add-instance -B /var/lib/pgsql/DB-zabbix/pro_backup/ --instance node -D /var/lib/pgsql/13/data/

смотрим какие инстансы есть
pg_probackup-13 show -B /var/lib/pgsql/DB-zabbix/pro_backup/ --instance node

pg_probackup-13 show -B /mnt/test/pro_backup/ --instance node

* где /var/lib/pgsql/DB-zabbix/pro_backup/   - это место размещения нашей папки с архивом где будет располагаться бэкап

смотрим конфиг инстанса 
pg_probackup-13 show-config -B /var/lib/pgsql/DB-zabbix/pro_backup/ --instance node

запускаем бэкап

 pg_probackup-13 backup -B /var/lib/pgsql/DB-zabbix/pro_backup/ --instance node_backup -d zabbix --stream -b full --temp-slot
--temp-slot  # временный слот для бэкапа
--progress    # показывает статус выполнения
-j2           # создает 2 потока архивирования
--compress    # сжать
-- external-dirs=/tmp/123 # задаем доп директорию которую необходимо добавить в архив (например директорию с файлами конфигурации)

Восстановление базы из бэкапа

1) при восстановлении созданного бэкапа обязательно необходмио убедиться что версии postgresql  и timescaledb идентичны той базе с которой создан бэкап!!

1. останавливаем сервер 
systemctl stop postgresql-13   # останавливаем службу 
systemctl status postgresql-13 # проверяем статус службы что она остановлена

2 . очищаем директории где лежит наша база 
rm -rf /var/lib/pgsql/DB-zabbix/*
rm -rf /var/lib/pgsql/13/data/*

если папка pg_wall находится в другом месте на примонтированном диске также очищаем и ее
rm -rf /mnt/pg_wal/pg_wal/*

запускаем восстановление обязательно из под пользователя postgres !!!

su - postgres
pg_probackup-13 restore -B /var/lib/pgsql/DB-zabbix/pro_backup/ --instance node_backup 
 можно использовать ключи (позволяет ускорить процесс восстановления, но есть риск что некорректно восстановится)
--no-sync  # не синхронизировать
--no-validate  # не проводить валидацию