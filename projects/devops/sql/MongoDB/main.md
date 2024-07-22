#nosql #mongodb
![mongodb](https://miro.medium.com/v2/resize:fit:1200/0*xQv5bh2xmKXrZi7W.png)
### Подключение к MongoDB
##### Без аутентификации
Подключитесь к MongoDB с помощью командной строки MongoDB:
```shell
mongosh
```
##### С аутентификацией
```shell
mongosh --port 27017 --authenticationDatabase "admin" -u "myUserAdmin" -p
```
### Примеры основных команд
#### очистить экран
```javascript
cls
```
#### Посмотреть имеющиеся базы данных
```javascript
show dbs
```
#### перейти в базу данных
```javascript
use name_base
```
#### посмотреть все коллекции базы
```javascript
show collections 
```
#### посмотреть в какой базе мы находимся
```javascript
db 
```
#### добавить документ в базу
```javascript
db.first_tets.insertOne({"title": "Snatch", "year": 3333})
```
#### добавить несколько документов в базу
```javascript
db.first_tets.insertMany([{"first": 1111}, {"two": 2222}])
```
#### find
##### найти в базе все значения
```javascript
db.first_tets.find()
//выводит на єкран по 20 значений, для єкономии ресурсов компьютера
//для просмотра следущей страници вводим `it`
```
##### найти в базе значения по значению `title`
```javascript
db.first_tets.find({title: 'Snatch'})

//вывод
{
  _id: ObjectId('668848ea805e7fec0b17945d'),
  title: 'Snatch',
  year: 444
}
```
##### показать все документы у которых `title: 'Snatch'` выводя только поле `title`
```javascript
db.first_tets.find({title: 'Snatch'}, {title:1})

//вывод
{
  _id: ObjectId('668848ea805e7fec0b17945d'),
  year: 444
}
```
##### вывести по ид один точный результат
```javascript
db.first_tets.findOne({_id: ObjectId('6687d5733654ff9b66062b18')})

//вывод
{
	_id: ObjectId('6687d5733654ff9b66062b18'),
	title: 'Ssss',
	year: 4444,
	genres: [
		'crime',
		'comedy'
	],
	rating: 9.4
}
```
##### вывести по ид один точный результат показав только `genres`
```javascript
db.first_tets.findOne({_id: ObjectId('6687d5733654ff9b66062b18')}, {genres: 1})

//вывод
{ _id: ObjectId('6687d5733654ff9b66062b18'),
  genres: ['crime', 'comedy']}
```
##### вывести список всех документов с 5штук
```javascript
db.first_tets.findOne().limit(5)
```
#### sort
##### сортируем по рейтингу
```javascript
db.first_tets.find({title: 'Snatch'}, {title:1}).sort({rating: -1}) //по убыванию рейтинга, 
// sort({rating: 1}) - по возрастанию
```
#### удалить коллекцию
```javascript
db.first_test.drop()
```
> если база имеет всего одну коллекцию и мы её удаляем, то удалится и сама база с этой коллекцией

### Какие роли доступны в MongoDB по умолчанию
MongoDB включает в себя ряд полезных ролей, которые объединяют схожие привилегии. Эти роли позволяют вам лаконично предоставлять и отзывать привилегии для ресурсов базы данных..

Чтобы просмотреть список действий, доступных в MongoDB, а также их функции, разверните раздел ниже.:
**Список ролей MongoDB по умолчанию**
##### read
Обеспечивает доступ для чтения к несистемным коллекциям.
    - Действия:
        - `changeStream`
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `find`
        - `killCursors`
        - `listIndexes`
        - `listCollections`
##### readWrite
Обеспечивает доступ для чтения и записи к несистемным коллекциям.
    - Действия:
        - `collStats`
        - `convertToCapped`
        - `createCollection`
        - `dbHash`
        - `dbStats`
        - `dropCollection`
        - `createIndex`
        - `dropIndex`
        - `find`
        - `insert`
        - `killCursors`
        - `listIndexes`
        - `listCollections`
        - `remove`
        - `renameCollectionSameDB`
        - `update`
##### dbAdmin
Обеспечивает доступ к административным задачам на уровне базы данных, исключая управление ролями и пользователями.
    - Действия в рамках `system.profile` коллекция:
        - `changeStream`
        - `collStats`
        - `convertToCapped`
        - `createCollection`
        - `dbHash`
        - `dbStats`
        - `dropCollection`
        - `find`
        - `killCursors`
        - `listCollections`
        - `listIndexes`
        - `planCacheRead`
    - Действия в несистемных коллекциях:
        - `bypassDocumentValidation`
        - `collMod`
        - `collStats`
        - `compact`
        - `convertToCapped`
        - `createCollection`
        - `createIndex`
        - `dbStats`
        - `dropCollection`
        - `dropDatabase`
        - `dropIndex`
        - `enableProfiler`
        - `listCollections`
        - `listIndexes`
        - `planCacheIndexFilter`
        - `planCacheRead`
        - `planCacheWrite`
        - `reIndex`
        - `renameCollectionSameDB`
        - `storageDetails`
        - `validate`
##### userAdmin
Предоставляет доступ для создания и изменения пользователей и ролей.
    - Действия:
        - `changeCustomData`
        - `changePassword`
        - `createRole`
        - `createUser`
        - `dropRole`
        - `dropUser`
        - `grantRole`
        - `revokeRole`
        - `setAuthenticationRestriction`
        - `viewRole`
        - `viewUser`
##### dbOwner
Обеспечивает административный доступ к базе данных, включая управление ролями и пользователями.
    - Роли, которые наследует эта роль:
        - `readWrite`
        - `dbAdmin`
        - `userAdmin`
##### clusterMonitor
Предоставляет доступ для чтения к кластеру.
    - Действия для всего кластера:
        - `checkFreeMonitoringStatus`
        - `connPoolStats`
        - `getCmdLineOpts`
        - `getDefaultRWConcern`
        - `getLog`
        - `getParameter`
        - `getShardMap`
        - `hostInfo`
        - `inprog`
        - `listDatabases`
        - `listSessions`
        - `listShards`
        - `netstat`
        - `replSetGetConfig`
        - `replSetGetStatus`
        - `serverStatus`
        - `setFreeMonitoring`
        - `shardingState`
        - `top`
    - Действия для всех баз данных в кластере:
        - `collStats`
        - `dbStats`
        - `getShardVersion`
        - `indexStats`
        - `useUUID`
    - Действия для всех `system.profile` Коллекции:
        - `find`
    - Действия над несистемными коллекциями в `config` БАЗА ДАННЫХ:
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `find`
        - `getShardVersion`
        - `indexStats`
        - `killCursors`
        - `listCollections`
        - `listIndexes`
        - `planCacheRead`
    - Действия по `system.js` коллекция в `config` БАЗА ДАННЫХ:
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `find`
        - `killCursors`
        - `listCollections`
        - `listIndexes`
        - `planCacheRead`
    - Действия над всеми коллекциями в `local` БАЗА ДАННЫХ:
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `find`
        - `getShardVersion`
        - `indexStats`
        - `killCursors`
        - `listCollections`
        - `listIndexes`
        - `planCacheRead`
    - Действия по `system.js` коллекция в `local` БАЗА ДАННЫХ:
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `find`
        - `killCursors`
        - `listCollections`
        - `listIndexes`
        - `planCacheRead`
    - Действия по `system.replset` и `system.profile` коллекции в `local` БАЗА ДАННЫХ:
        - `find`
##### clusterManager
Обеспечивает доступ к мониторингу и управлению кластером через `config` и `local` базы данных
    - Действия на всем кластере:
        - `addShard`
        - `appendOplogNote`
        - `applicationMessage`
        - `cleanupOrphaned`
        - `flushRouterConfig`
        - `getDefaultRWConcern`
        - `listSessions`
        - `listShards`
        - `removeShard`
        - `replSetConfigure`
        - `replSetGetConfig`
        - `replSetGetStatus`
        - `replSetStateChange`
        - `resync`
        - `setDefaultRWConcern`
        - `setFeatureCompatibilityVersion`
        - `setFreeMonitoring`
    - Действия со всеми базами данных в кластере:
        - `clearJumboFlag`
        - `enableSharding`
        - `refineCollectionShardKey`
        - `moveChunk`
        - `splitChunk`
        - `splitVector`
    - Действия над несистемными коллекциями в `config` БАЗА ДАННЫХ:
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `enableSharding`
        - `find`
        - `insert`
        - `killCursors`
        - `listCollections`
        - `listIndexes`
        - `moveChunk`
        - `planCacheRead`
        - `remove`
        - `splitChunk`
        - `splitVector`
        - `update`
    - Действия по `system.js` коллекция в `config` БАЗА ДАННЫХ:
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `find`
        - `killCursors`
        - `listCollections`
        - `listIndexes`
        - `planCacheRead`
    - Действия над всеми несистемными коллекциями в `local` БАЗА ДАННЫХ:
        - `enableSharding`
        - `insert`
        - `moveChunk`
        - `remove`
        - `splitChunk`
        - `splitVector`
        - `update`
    - Действия для `system.replset` коллекция в `local` БАЗА ДАННЫХ:
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `find`
        - `killCursors`
        - `listCollections`
        - `listIndexes`
        - `planCacheRead`
##### hostManager
Предоставляет возможность мониторинга и управления серверами..
    - Действия на всем кластере:
        - `applicationMessage`
        - `closeAllDatabases`
        - `connPoolSync`
        - `flushRouterConfig`
        - `fsync`
        - `invalidateUserCache`
        - `killAnyCursor`
        - `KillAnySession`
        - `killop`
        - `logRotate`
        - `resync`
        - `setParameter`
        - `shutdown`
        - `touch`
        - `unlock`
##### clusterAdmin
Предоставляет весь доступ к управлению кластером
    - Роли, которые наследует эта роль:
        - `clusterManager`
        - `clusterMonitor`
        - `hostManager`
    - Дополнительные действия:
        - `dropDatabase`
##### backup
Предоставляет привилегии, необходимые для резервного копирования данных.
    - Действия на всех ресурсах:
        - `listDatabases`
        - `listCollections`
        - `listIndexes`
    - Действия на всем кластере:
        - `appendOplogNote`
        - `getParameter`
        - `listDatabases`
        - `serverStatus`
    - Действия над несистемными коллекциями, `system.js` и `system.profile` коллекции, `admin.system.users` и `admin.system.roles` коллекции и `config.settings` коллекция:
        - `find`
    - Действия по `config.settings` коллекция:
        - `insert`
        - `update`
##### restore
Предоставляет права на восстановление данных в кластере.
    - Действия на всем кластере:
        - `getParameter`
    - Действия над несистемными коллекциями:
        - `bypassDocumentValidation`
        - `changeCustomData`
        - `changePassword`
        - `collMod`
        - `convertToCapped`
        - `createCollection`
        - `createIndex`
        - `createRole`
        - `createUser`
        - `dropCollection`
        - `dropRole`
        - `dropUser`
        - `grantRole`
        - `insert`
        - `revokeRole`
        - `viewRole`
        - `viewUser`
    - Действия по `system.js` коллекция:
        - `bypassDocumentValidation`
        - `collMod`
        - `createCollection`
        - `createIndex`
        - `dropCollection`
        - `insert`
    - Действия на любом ресурсе
        - `listCollections`
    - Действия над несистемными коллекциями в `config` и `local` базы данных:
        - `bypassDocumentValidation`
        - `collMod`
        - `createCollection`
        - `createIndex`
        - `dropCollection`
        - `insert`
    - Действия по `admin.system.version` коллекция:
        - `bypassDocumentValidation`
        - `collMod`
        - `createCollection`
        - `createIndex`
        - `dropCollection`
        - `find`
        - `insert`
    - Действия по `admin.system.roles` коллекция:
        - `createIndex`
    - Действия по `admin.system.users` коллекция:
        - `bypassDocumentValidation`
        - `collMod`
        - `createCollection`
        - `createIndex`
        - `dropCollection`
        - `find`
        - `insert`
        - `remove`
        - `update`
##### readAnyDatabase
Предоставляет те же привилегии, что и `read` ко всем базам данных, кроме `local` и `config`
    - Дополнительные действия на всем кластере:
        - `listDatabases`
##### readWriteAnyDatabase
Предоставляет те же привилегии, что и `readWrite` ко всем базам данных, кроме `local` и `config`
    - Дополнительные действия на всем кластере:
        - `listDatabases`
##### userAdminAnyDatabase
Предоставляет те же привилегии, что и `userAdmin` во всех базах данных, кроме `local` и `config`.
    - Дополнительные действия на всем кластере:
        - `authSchemaUpgrade`
        - `invalidateUserCache`
        - `listDatabases`
    - Дополнительные действия по `system.users` и `system.roles` кластеры в `admin` БАЗА ДАННЫХ:
        - `collStats`
        - `dbHash`
        - `dbStats`
        - `find`
        - `killCursors`
        - `planCacheRead`
##### dbAdminAnyDatabase
Предоставляет те же привилегии, что и `dbAdmin` для всех баз данных, кроме `local` и `config`.
    - Дополнительные действия на всем кластере:
        - `listDatabases`
##### root
Обеспечивает полный доступ ко всей системе..
    - Роли, которые наследует эта роль:
        - `readWriteAnyDatabase`
        - `dbAdminAnyDatabase`
        - `userAdminAnyDatabase`
        - `clusterAdmin`
        - `restore`
        - `backup`
    - Дополнительные действия по `system` Коллекции:
        - `validate`

### Как включить авторизацию в MongoDB

Прежде чем MongoDB сможет использовать авторизацию для управления привилегиями пользователей, вы должны включить эту функцию на своем сервере или кластере. Для этого вам необходимо войти на свой сервер с помощью `root` или другие административные привилегии.

**Примечание:** Прежде чем включать авторизацию, дважды проверьте, есть ли у вас доступ хотя бы к одной роли с привилегиями, необходимыми для управления ролями..

Измените конфигурацию сервера MongoDB, открыв `/etc/mongod.conf` файл в текстовом редакторе от имени администратора. Эта команда откроет файл с помощью текстового редактора, определенного в `EDITOR` переменная среды и вернуться к `vi`, который доступен практически во всех системах:

```bash
sudo ${EDITOR:-vi} /etc/mongod.conf
```

В файле конфигурации MongoDB используется [формат сериализации YAML](https://en.wikipedia.org/wiki/YAML) чтобы определить конфигурацию. Раскомментируйте или добавьте `security:` Ключ раздела к файлу. Под этим ключом сделайте отступ в строке, используя пробелы (табуляция не разрешена в YAML) и установите `authorization` к `enabled`:

```javascript
security:  
	authorization: enabled
```

Сохраните и закройте файл, когда закончите..

Чтобы включить новые настройки, перезапустите процесс сервера MongoDB. Если ваш сервер MongoDB работает на хосте Linux, операция будет выглядеть следующим образом.:

```bash
sudo systemctl restart mongod.service
```

После перезапуска процесса структура авторизации MongoDB должна быть включена в базе данных..
### Просмотр привилегий и ролей
Прежде чем приступить к назначению ролей пользователям, рекомендуется ознакомиться с тем, как просматривать информацию о привилегиях и ролях внутри системы..
##### Посмотреть все роли в системе
Чтобы просмотреть все роли, доступные в системе, включая все встроенные роли и связанные с ними привилегии, используйте команду `db.getRoles()` метод с `showPrivileges` и `showBuiltinRoles` параметры установлены на `true`:

```javascript
db.getRoles({   
	rolesInfo: 1,
	showPrivileges: true, 
	showBuiltinRoles: true
	})
```
Возвращенный список будет включать в себя целый список вложенной информации о каждой из ролей и привилегиях, которые они имеют на различных ресурсах в системе..
##### Информация о конкретной роли
Чтобы получить информацию о конкретной роли, используйте команду `db.getRole()` вместо этого метод. Перед выполнением команды вы должны находиться в базе данных, где определен пользователь.:

```javascript
use admin
db.getRole(    "root",    {        showPrivileges: true,        showBuiltinRoles: true    })
```
##### Посмотреть роли пользователей базы
Чтобы проверить, какие роли предоставлены каждому пользователю, перейдите к интересующей вас базе данных и используйте команду `db.getUsers()` Метод:
```javascript
use admin
db.getUsers()
```
##### Посмотреть роли конкретного пользователя базы
Чтобы проверить роли, связанные с конкретным пользователем, используйте `db.getUser()` вместо:
```javascript
use admin
db.getUser("root")
```
##### Посмотреть все роли всех пользователей
```javascript
use admin
db.system.users.find().pretty() // в новых версиях можно без `pretty()`, читабельный вывод формируется по умолчанию
```
### Работа с пользователями
##### create user
```javascript
db.createUser(
  {
    user: "adminUser",
    pwd: passwordPrompt(),
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```
> вместо `passwordPrompt()`(дает возможность скрыть ввод пароля) можно просто указать пароль в ""
##### create other users
После того как вы подключились с правами администратора, вы можете создавать других пользователей с различными правами доступа. Например, для создания пользователя для конкретной базы данных:
```javascript
use yourDatabase
db.createUser(
  {
    user: "yourUser",
    pwd: passwordPrompt(),   // or cleartext password
    roles: [ { role: "readWrite", db: "yourDatabase" } ]
  }
)
```
или на несколько баз
```javascript
use test
db.createUser(
	{
	    user: "myTester",
	    pwd:  passwordPrompt(),
	    roles: [
		    { role: "readWrite", db: "test" },
		    { role: "read", db: "reporting" }
		]
	}
)
```
##### delete user
```javascript
db.dropuser("UsedForDelete")
```
##### delete all users in base
```
db.dropAllUsers()
```
### Назначение и отзыв ролей у пользователей
##### Добавить пользователю роль
`db.grantRolesToUser()` Метод позволяет указать дополнительные роли, которые вы хотите добавить пользователю. Его первый аргумент — это пользователь, которому вы хотите предоставить дополнительные привилегии, а второй аргумент — это массив дополнительных ролей, которые вы хотите добавить.:
```javascript
db.grantRolesToUser( 
	"sally",
	[ "read", "backup" ]
)
```
> Если роли определены в текущей базе данных, вы можете использовать приведенное выше сокращение, указывающее роль по имени без упоминания базы данных..
##### Добавить пользователю роли на несколько баз
Чтобы предоставить роли, связанные с другой базой данных, или, если быть более явным, укажите роли в виде документа, определяющего `role` и `db` вместо:
```javascript
db.grantRolesToUser(    
	"sally",    
	[
		{ role: "read", db: "sales"},
		{ role: "readWrite", db: "callLogs"}
    ]
)
```
##### Отозвать роли у пользователя
Чтобы отозвать роли у пользователя, вы можете использовать сопутствующий метод под названием `db.revokeRolesFromUser()`. Синтаксис аргумента работает точно так же, но на этот раз команда удаляет роли из указанной учетной записи..

Чтобы удалить роли, определенные в текущей базе данных, вы можете использовать имена ролей без упоминания базы данных.:
```javascript
db.revokeRolesFromUser(
	"sally",
	[ "read", "backup"]
)
```
##### Отозвать роли у пользователя в нескольких базах
Чтобы указать роли, связанные с другими базами данных, используйте полную форму, указав `role` и `db` в документе:
```javascript
db.revokeRolesFromUser(   
	"sally",
	[
	    { role: "read", db: "sales"},
	    { role: "readWrite", db: "callLogs"}
	]
)
```
### Создание пользовательских ролей и управление ими
Бывают случаи, когда встроенные роли системы не соответствуют типам разрешений, которые вам необходимо назначить. В этих случаях вы можете создавать свои собственные роли..
##### Создание новых ролей
`db.createRole()` Метод позволяет вам определить новую роль, которой вы можете назначить привилегии и другие роли. Затем вы можете предоставить свою новую роль пользователям, чтобы предоставить им определенные вами определенные привилегии..

Основной синтаксис `db.createRole()` Метод включает передачу документа, определяющего характеристики роли. Документ может иметь следующие поля:

- `role`: Имя, которое вы хотите дать роли
- `privileges`: Массив, содержащий набор свободных привилегий, которые вы хотите назначить роли. Каждая привилегия определяется во вложенном документе, определяющем `resource` документ (с указанием ресурсов, к которым применяется эта привилегия), а также массив `actions` которые предоставляются
- `roles`: Массив дополнительных ролей, от которых должна наследовать эта роль. Новая роль получит все привилегии, предоставленные любой из ролей, перечисленных здесь..
- `authenticationRestrictions`: Массив, задающий любые ограничения на аутентификацию для роли. Это позволяет вам отказать в привилегиях роли, если пользователь не прошел аутентификацию способом, одобренным ролью..

Первые три поля обязательны для каждой новой создаваемой роли..

В качестве примера давайте создадим роль под названием `salesMonitor` который обеспечивает доступ только для чтения к `sales` БАЗА ДАННЫХ:

```javascript
db.createRole(
	{ role: "salesMonitor",
	privileges: [],
	roles: [
		{
			role: "read",
			db: "sales"
		}
	],
})
```

Мы могли бы выразить аналогичную (но более ограниченную) роль, используя `privileges` поле вместо `read` роль, набрав:

```javascript
db.createRole(
	{ role: "salesMonitor",
	privileges: [
		{ 
			resource: { db: "sales",	collection: ""},
			actions: [ "find" ]
		}
	],
	roles: []
})
```
##### Просмотр информации о настраиваемых ролях
Как и раньше, вы можете получить информацию о своих ролях с помощью `db.getRole()` Метод:
```javascript
db.getRole(
	"salesMonitor",
	{showPrivileges: true }
)
```
##### Предоставление дополнительных привилегий пользовательским ролям
Чтобы предоставить дополнительные привилегии существующей пользовательской роли, вы можете использовать команду `db.grantPrivilegesToRole()` метод. Он принимает массив привилегий, которые определяются документами, содержащими `resource` документ и `actions` массив, как мы видели выше с `db.createRole()`.

Например, чтобы добавить `listCollections` привилегия `salesMonitor` роль, вы можете ввести:

```javascript
db.grantPrivilegesToRole(
	"salesMonitor",
	[{resource: { db: "sales", collection: "" },
	  actions: [ "listCollections" ]}]
)
```
##### Отзыв привилегий у пользовательских ролей
Если вы передумаете, вы можете использовать `db.revokePrivilegesFromRole()` метод удаления `listCollections` действие с использованием того же формата:
```javascript
db.revokePrivilegesFromRole(
	"salesMonitor",
		[{ resource: { db: "sales", collection: "" },
		   actions: [ "listCollections" ]}]
)
```
##### Назначение ролей пользовательским ролям
Чтобы добавить привилегии, определенные ролью, к другой роли, вы можете использовать команду `db.grantRolesToRole()` метод. Метод принимает в качестве аргументов роль, которую вы хотите изменить, и массив ролей, которые вы хотите добавить к ней..

Чтобы указать, что вы хотите использовать `read` роль для `salesMonitor` в конце концов, вы можете сделать это, набрав:
```javascript
db.grantRolesToRole( "salesMonitor", [ "read" ] )
```
##### Отзыв ролей из пользовательских ролей
Если вы снова передумаете, вы можете отозвать доступ к роли, используя команду `db.revokeRolesFromRole()` метод, который использует тот же синтаксис аргументов:
```javascript
db.revokeRolesFromRole( "salesMonitor", [ "read" ])
```
##### Замена значений пользовательской роли
Чтобы переопределить характеристики определяемой пользователем роли, вы можете использовать команду `db.updateRole()` команда. Это работает _замена_ поля, которые он указывает вместо _добавление_ ИЛИ _усечение_ их. По этой причине рекомендуется соблюдать осторожность при вводе команды, чтобы случайно не перезаписать важную информацию..

Синтаксис для `db.updateRole()` Команда включает передачу имени роли в качестве первого аргумента и документа, указывающего поле или поля, которые вы хотите заменить, в качестве второго аргумента. Поля, которые можно заменить, включают в себя `privileges` массив, `roles` массив, и `authenticationRestrictions` множество. Хотя бы один из них должен быть включен в документ..

Например, как только мы наконец решили, что хотим `salesMonitor` роль использовать `read` роль в `sales` базы данных, мы можем захотеть переопределить привилегии роли и массивы ролей, чтобы очистить любые дополнительные привилегии, которые остались после наших экспериментов. Вы можете сделать это, обновив роль новой информацией, которую вы хотите установить.:

```javascript
db.updateRole(
	"salesMonitor",
	{
		privileges: [],
		roles: 
			[{
				role: "read", 
				db: "sales"
            }]
    }
)
```
##### Удаление пользовательских ролей
Вы можете удалить ненужные роли с помощью `db.dropRole()` Метод.
Чтобы удалить роль, просто передайте ее имя методу:
```javascript
db.dropRole("RoleForDelete")
```
Роль будет удалена из системы, и любые привилегии, предоставленные пользователям этой ролью, больше не будут доступны..

[Источник](https://www.prisma.io/dataguide/mongodb/authorization-and-privileges#creating-new-roles)