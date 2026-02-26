# Системные переменные и параметры конфигурации

## Системные переменные

SqlQuery, описанные в серверном xml-файле поддерживают системные переменные, значения которых серверная часть автоматически формирует и подставляет вместе со значениями пользовательских переменных. Значения системных переменных можно переопределить, передав в запрос параметры с такими же именами.

Текущим пользователем считается пользователь, от имени которого был отправлен запрос с клиентской части на сервер.

### Переменные пользователя <a href="#user-variables" id="user-variables"></a>

Таблицы, из которых будут браться значения, задаются тэгом [\<UserSettings>](https://wfsys.gitbook.io/workflow-engine-syntax/#user_settings) описанном в серверном xml-файле. Название таблицы пользователей глобальной схемы данных задается атрибутом `PublicUserTable` (по умолчанию _public.user_), а название таблицы пользователей в локальной схеме данных - атрибутом `Table`. Название таблицы соответствий пользователей и групп в локальной схеме данных указывается в атрибуте `UserGroupTable`.

<table data-header-hidden><thead><tr><th width="227"></th><th></th></tr></thead><tbody><tr><td>{UserId}</td><td>Идентификатор текущего пользователя из таблицы пользователей <em>локальной</em> схемы данных.<br>Название поля задается атрибутом <code>IdField</code>.</td></tr><tr><td>{PublicUserId}</td><td>Идентификатор текущего пользователя из таблицы пользователей <em>глобальной</em> схемы данных.<br>Название поля задается атрибутом <code>PublicUserIdField</code>.</td></tr><tr><td>{UserName}</td><td>Системное имя текущего пользователя из таблицы пользователей <em>глобальной</em> схемы данных.<br>Название поля задается атрибутом <code>PublicUserNameField</code>.</td></tr><tr><td>{UserFullName}</td><td>Видимое имя текущего пользователя из таблицы пользователей <em>глобальной</em> схемы данных.<br>Название поля задается атрибутом <code>PublicUserFullNameField</code>.</td></tr><tr><td>{UserEnabled}</td><td>Признак активного состояния текущего пользователя из таблицы пользователей <em>глобальной</em> схемы данных.<br>Название поля задается атрибутом <code>PublicUserEnabledField</code>.</td></tr><tr><td>{UserGroupIds}</td><td>Список идентификаторов групп, в которых состоит текущий пользователь.<br>Значения берутся из таблицы соответствий пользователей и групп в <em>локальной</em> схеме данных.</td></tr><tr><td>{UserGroupIdsArray}</td><td>Массив идентификаторов групп, в которых состоит текущий пользователь.<br>Значения берутся из таблицы соответствий пользователей и групп в <em>локальной</em> схеме данных.</td></tr></tbody></table>

### Переменные даты и времени <a href="#datetime-variables" id="datetime-variables"></a>

Дата и время отображается в часовом поясе сервера, который задается в файле конфигурации сервера [appsettings.json](configuration-files/server/appsettings-json.md) в поле [TimeZone](configuration-files/server/appsettings-json.md#timezone).

<table data-header-hidden><thead><tr><th width="227"></th><th></th></tr></thead><tbody><tr><td>{Now}</td><td>Текущая дата со временем на сервере</td></tr><tr><td>{NowTime}</td><td>Текущее время на сервере</td></tr><tr><td>{NowDate}</td><td>Текущая дата на сервере</td></tr><tr><td>{NowYear}</td><td>Текущий год на сервере</td></tr><tr><td>{NowMonth}</td><td>Текущая месяц на сервере</td></tr><tr><td>{NowHour}</td><td>Возвращает количество часов из текущего времени на сервере</td></tr><tr><td>{NowMinute}</td><td>Возвращает количество минут из текущего времени на сервере</td></tr><tr><td>{NowSecond}</td><td>Возвращает количество секунд из текущего времени на сервере</td></tr></tbody></table>

### Переменные временной зоны <a href="#timezone-variables" id="timezone-variables"></a>

Временная зона сервера задается в файле конфигурации сервера [appsettings.json](configuration-files/server/appsettings-json.md) в поле [TimeZone](configuration-files/server/appsettings-json.md#timezone).

<table data-header-hidden><thead><tr><th width="227"></th><th></th></tr></thead><tbody><tr><td>{ServerTimeZone}</td><td>Текущая временная зона сервера.</td></tr><tr><td>{TimeZoneOffset}</td><td><em>Устаревший</em><br>Величина смещения временной зоны текущего пользователя относительно Etc/GMT.</td></tr><tr><td>{ServerTimeZoneOffset}</td><td><em>Устаревший</em><br>Величина смещения временной зоны сервера относительно Etc/GMT.</td></tr><tr><td>{TimeZoneDiff}</td><td><em>Устаревший</em><br>Разница между временной зоной сервера и временной зоной текущего пользователя.</td></tr></tbody></table>

Как правило в sql-запросах и функциях используем параметры конфигураций.

## Параметры конфигурации <a href="#configuration-parameters" id="configuration-parameters"></a>

Перед выполнением каждого SqlQuery сервер выполняет запрос на установку параметров конфигурации:

```sql
SELECT
    set_config('ws.user_id', (1)::varchar, False),
    set_config('ws.public_user_id', (1)::varchar, False),
    set_config('ws.server_time_zone', ('Etc/GMT')::varchar, False),

    -- Устаревшие
    set_config('ws.time_zone_offset', (3)::varchar, False),
    set_config('ws.time_zone_diff', (3)::varchar, False),
    set_config('ws.server_time_zone_offset', (0)::varchar, False);
```

Метод set\_config устанавливает для параметра значение, которое будет доступно до конца текущего сеанса. Чтобы получить значение параметра необходимо использовать метод current\_setting:

```sql
SELECT current_setting('ws.server_time_zone');
```

Платформа поддерживает следующие параметры конфигурации:

<table data-header-hidden><thead><tr><th width="265"></th><th></th></tr></thead><tbody><tr><td>ws.user_id</td><td>Идентификатор текущего пользователя из таблицы пользователей <em>локальной</em> схемы данных.<br>Название поля задается атрибутом <code>IdField</code>.</td></tr><tr><td>ws.public_user_id</td><td>Идентификатор текущего пользователя из таблицы пользователей <em>глобальной</em> схемы данных.<br>Название поля задается атрибутом <code>PublicUserIdField</code>.</td></tr><tr><td>ws.server_time_zone</td><td>Текущая временная зона сервера.</td></tr><tr><td>ws.server_time_zone_offset</td><td><em>Устаревший</em><br>Величина смещения временной зоны сервера относительно Etc/GMT.</td></tr><tr><td>ws.time_zone_offset</td><td><em>Устаревший</em><br>Величина смещения временной зоны текущего пользователя относительно Etc/GMT.</td></tr><tr><td>ws.time_zone_diff</td><td><em>Устаревший</em><br>Разница между временной зоной сервера и временной зоной текущего пользователя.</td></tr></tbody></table>

Так же перед выполнением SqlQuery сервер выполняет запрос на установку временной зоны для сервера PostgreSQL:

```sql
SET time zone 'Etc/GMT';
```

Временная зона для сервера PostgreSQL устанавливается только для текущего сеанса. Это необходимо, чтобы исключить неприятные ситуации из-за редактированием настроек сервера PostgreSQL и изменения его временной зоны. В качестве значения используется временная зона сервера.
