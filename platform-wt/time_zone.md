---
description: >-
  В статье собрана полная информация по работе с временными зонами в платформе
  WT.
---

# Временные зоны

## Временные зоны в БД

В таблице **public.time\_zone\_info** хранится полный список доступных временных зон.

```sql
CREATE TABLE public.time_zone_info
(
  time_zone_info_id smallint NOT NULL DEFAULT nextval('time_zone_info_id_seq'::regclass),
  name text NOT NULL,
  id_title text NOT NULL,
  by_default boolean NOT NULL DEFAULT false,
  CONSTRAINT pk_time_zone_info_id PRIMARY KEY (time_zone_info_id)
);
```

В колонке **name** указано имя временной зоны в формате IANA. В колонке **id\_title** указан ключ для получения названия временной зоны из таблицы строковых ресурсов public.strings с учетом локали приложения. В колонке **by\_default** отмечается временная зона по умолчанию, которая будет присваиваться новым пользователям.

В таблице **public.timezone\_intervals** для каждой временной зоны хранится полная история сдвигов относительно UTC.

```sql
CREATE TABLE public.timezone_intervals
(
  time_zone_info_id smallint NOT NULL,
  date_utc timestamp without time zone NOT NULL,
  date_local timestamp without time zone NOT NULL,
  "offset" character varying(10),
  CONSTRAINT fk_time_zone_info_id FOREIGN KEY (time_zone_info_id)
      REFERENCES public.time_zone_info (time_zone_info_id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION
);
```

В колонках **date\_utc** и **date\_local** указана начальная дата периода, в котором применялся сдвиг из колонки **offset**.

## Настройки

### Временная зона сервера <a href="#server_timezone" id="server_timezone"></a>

Даты со временем хранятся в базе данных во временной зоне сервера, по умолчанию это **Etc/GMT**. Чтобы задать иную временную зону, необходимо в серверный файл настроек (appsettings.json) добавить строки:

{% code title="appsettings.json" %}
```json
"TimeZone": {
  "TimeZone": "Europe/Moscow",
}
```
{% endcode %}

В качестве значения вложенного поля TimeZone ожидается имя временной зоны в формате IANA, которое можно взять в таблице **public.time\_zone\_info** в поле **name**.

### Временная зона клиента <a href="#client_timezone" id="client_timezone"></a>

Для задания временной зоны клиента используется колонка **time\_zone\_info\_id** в таблице **public.user**.

По умолчанию новым клиентам присваивается идентификатор временной зоны со значением `True` в колонке `by_default`.

{% hint style="warning" %}
На временную зону клиента **не влияют** настройки часового пояса в системе Windows.
{% endhint %}

## Правила передачи времени между сервером и клиентом <a href="#rules_timezone" id="rules_timezone"></a>

### Дата со временем

Дата со временем (в формате даты) между клиентом и сервером всегда передается в UTC.

Когда дату со временем передаем с формы на сервер, клиентская часть автоматически переводит время из временной зоны клиента в UTC, а сервер полученную дату переводит из UTC в свою временную зону.

Аналогичное преобразование происходит и в обратную сторону. Сервер получает дату со временем из база данных, переводит ее в UTC и передает клиентской части. Клиент, получив дату со временем, переводит ее из UTC в свою временную зону.

### Дата или время

Если между клиентом и сервером передается только дата или только время, то значение не переводится в UTC. Сервер, получив дату или время, запишет их в базу данных без приведения к своей временной зоне.

## Как работает приведение

### Автоматическое преобразование

Автоматическое преобразование происходит на стороне сервера и клиента только для дат со временем.

Для текущей временной зоны пользователя на основе таблицы **public.timezone\_intervals** строится два словаря временных интервалов: один для перевода времени из UTC в клиентскую временную зону, второй для обратного приведения. Для первого словаря используются колонки **date\_utc** и **offset**, а для второго - **date\_local** и **offset**. Оба словаря передаются клиентскому приложению. Подобные словари строятся и для сервера для его временной зоны.

Для даты со временем находится наименьший интервал, значение сдвига которого либо добавляется к дате со временем, если идет преобразование из UTC, либо вычитается, если идет преобразование в UTC.

### Ручное преобразование

Ручное преобразование дат или времени на стороне сервера следует реализовывать в трех случаях.

#### Даты в фильтрах

Дата или время передаются с формы на сервер для фильтрации данных по полю типа timestamp (дата со временем).

Пример подробно разбирается в уроке 7 в разделе [Временные зоны и фильтры по дате](https://wfsys.gitbook.io/wt-practice/main/lesson_7#vremennye-zony-i-filtry-po-date).

Для такого преобразования используется функция:

```sql
CREATE OR REPLACE FUNCTION public.convert_date_filter(timestamp without time zone)
  RETURNS timestamp without time zone AS
$BODY$
  --используется при работе с фильтрами (когда с формы приходит дата БЕЗ времени)
  --преобразует дату с формы к временной зоне сервера
  --также можно использовать для преобразования любой даты (если она считается в часовом поясе клиента) ко времени сервера
DECLARE
  _time_zone_name text;
BEGIN
  
  _time_zone_name = time_zone_info.name
                    FROM
                      "user" pu
                      LEFT JOIN time_zone_info USING (time_zone_info_id)
                    WHERE
                      pu.user_id = current_setting('ws.public_user_id')::smallint;

  RETURN $1 at time zone _time_zone_name at time zone current_setting('ws.server_time_zone');
END;
$BODY$
  LANGUAGE plpgsql;
```

Первое использование конструкции `at time zone` означает, что дата указана во временной зоне пользователя (переменная \_time\_zone\_name), а второе использование конструкции `at time zone` указывает временную зону сервера, к которой необходимо преобразовать время. Для получения временной зоны сервера используется обращение к параметру конфигурации `ws.server_time_zone`.

В конце статьи есть описание [параметров конфигурации](time_zone.md#configuration-parameters) и [конструкции `at time zone`](time_zone.md#konstrukciya-at-time-zone).

#### Даты в JSON-объекте

Когда на сервер приходит JSON-объект, то он обрабатывается как строка без автоматического преобразования дат со временем. При формировании JSON-объекта, как правило, даты приводятся к UTC. А преобразование к временной зоне сервера необходимо реализовать самостоятельно.

JSON-объекты могут приходить либо с форм ([урок 26 - Работа с JSON на форме](https://wfsys.gitbook.io/wt-practice/advanced/lesson_json_on_form)), либо от сторонних сервисов через кастомные API-запросов ([урок 25 - Кастомные типы параметров](https://wfsys.gitbook.io/wt-practice/advanced/lesson_24#kastomnye-tipy-parametrov)).

Для такого преобразования используется функция:

```sql
CREATE OR REPLACE FUNCTION public.convert_date_json(timestamp without time zone)
  RETURNS timestamp without time zone AS
$BODY$
  --используется для дат со временем, полученных в json-строке
  --преобразует дату UTC к часовому поясу сервера
  BEGIN
    RETURN $1 at time zone 'utc' at time zone current_setting('ws.server_time_zone');
  END;
$BODY$
  LANGUAGE plpgsql;
```

#### Даты в строках

Иногда бывают ситуации, когда на стороне сервера собирается строка и отправляется клиенту. В таком случае необходимо сразу привести дату со временем из временной зоны сервера во временную зону клиента, которому отправится готовая строка.

Для такого преобразования используется функция:

```sql
CREATE OR REPLACE FUNCTION public.convert_date_to_user_timezone(timestamp without time zone)
  RETURNS time without time zone AS
$BODY$
  -- используется для преобразования даты со временем из часового пояса СЕРВЕРА к часовому поясу КЛИЕНТА
  DECLARE
    _time_zone_name text;
  BEGIN
  
  _time_zone_name = time_zone_info.name
                    FROM
                      "user" pu
                      LEFT JOIN time_zone_info USING (time_zone_info_id)
                    WHERE
                      pu.user_id = current_setting('ws.public_user_id')::smallint;

    RETURN $1 at time zone current_setting('ws.server_time_zone') at time zone _time_zone_name;
    
  END;
$BODY$
  LANGUAGE plpgsql;
```

## Дополнительные материалы

### Параметры конфигурации <a href="#configuration-parameters" id="configuration-parameters"></a>

В приведенных выше SQL-функциях используются параметры конфигурации, значения которым сервер задает, выполняя перед каждым обращением к базе данных системные запросы.

Первым запросом серверу PostgreSQL для текущего сеанса устанавливается временная зона:

```sql
SET time zone 'Etc/GMT';
```

В запросе используется значение временной зоны сервера из серверного файла настроек ([appsettings.json](https://wfsys.gitbook.io/wt_knowledge_base/platforma-wt/configuration-files/appsettings.json)).

Вторым запросом задаются значения параметрам конфигурации:

```sql
SELECT
  set_config('ws.user_id', (2)::varchar, False),
  set_config('ws.public_user_id', (2)::varchar, False),
  set_config('ws.server_time_zone', ('Etc/GMT')::varchar, False);
```

{% hint style="info" %}
Тексты этих запросов указываются в описании ошибки в журнале событий Windows.
{% endhint %}

### Конструкция  `at time zone`

Рассмотрим на примерах, как работает конструкция `at time zone`.

Допустим, что в качестве временной зоны сервера PostgreSQL задано значение _Europe/Moscow_, которую можно задать с помощью запроса:

```sql
SET time zone 'Europe/Moscow';
```

Начнем с запроса приведения строки с датой и временем к типу timestamp:

```sql
SELECT '2021-06-01 11:30:00'::timestamp;
```

Результатом запроса будет значение типа _timestamp **without** time zone_:

```
2021-06-01 11:30:00
```

Считается, что это значение во временной зоны сервера PostgreSQL (Europe/Moscow).&#x20;

Добавим в запрос конструкцию `at time zone` с заданием часового пояса UTC:

```sql
SELECT '2021-06-01 11:30:00'::timestamp
                            at time zone 'utc';
```

Результатом запроса будет значение типа _timestamp **with** time zone_:

```
2021-06-01 14:30:00+03
```

Время изменилось на 3 часа, что соответствует временному сдвигу для часового пояса Europe/Moscow относительно UTC.

Конструкция `at time zone` говорит, что дата и время **указаны** во временной зоне 'utc', **сколько это будет** во временной зоне сервера PostgreSQL(Europe/Moscow). Таким образом, произошло неявное приведение времени из UTC во временную зону сервера.

Рассмотрим пример, когда дата и время указаны во временной зоне Asia/Yekaterinburg, и мы хотим узнать, сколько это будет во временной зоне сервера PostgreSQL(Europe/Moscow):

```sql
SELECT '2021-06-01 11:30:00'::timestamp
                            at time zone 'Asia/Yekaterinburg';
```

Результатом запроса будет значение типа _timestamp **with** time zone_:

```
2021-06-01 09:30:00+03
```

Произошло вычитание 2х часов из времени, что соответствует разнице между зонами Asia/Yekaterinburg и Europe/Moscow.

Если необходимо явно привести время к нужной временной зоне, то следует указывать вторую конструкцию `at time zone`:

```sql
SELECT '2021-06-01 11:30:00'::timestamp
                            at time zone 'Europe/Moscow'
                            at time zone 'Asia/Yekaterinburg';
```

Результатом запроса будет значение типа _timestamp **without** time zone_:

```
2021-06-01 13:30:00
```

Первое использование конструкции `at time zone` означает, что дата указана во временной зоне Europe/Moscow, а второе использование конструкции `at time zone` указывает временную зону Asia/Yekaterinburg, к которой необходимо преобразовать время.

### Текущее время

При запуске серверной части приложения отправляется запрос на сторонний сервис для получение точного времени в UTC.

Если сервис не отвечает, то запрос отправляется на сервер WS.Server (общий для всех приложений), который предоставляет текущее время UTC из операционной системы.

Получив текущее время UTC, серверная часть приложения вычисляет смещение между полученным временем UTC и своим временем. Вычисленное смещение записывается в базу данных.

Если сервер WS.Server не отвечает и смещение уже записано в базу данных, то ничего не происходит. Если смещение отсутствует в базе данных, то берется текущее время UTC из Windows, на котором запускается веб-сервер.

Этот алгоритм отрабатывается раз в сутки, чтобы на сервере хранилась актуальная информация о смещении.

Смещение времени серверной части относительно точного времени UTC необходимо для корректного расчета текущего времени во временных зонах сервера и пользователя на формах.
