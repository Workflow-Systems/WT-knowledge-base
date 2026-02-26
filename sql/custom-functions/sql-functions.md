# Функции на языке запросов (SQL)

SQL-функция выполняет произвольный набор команд на языке SQL, разделённых точкой с запятой, и возвращает результат последнего запроса в списке. Помимо запросов `SELECT`, эти команды могут включать запросы, изменяющие данные (`INSERT`, `UPDATE` и `DELETE` и `MERGE`), а также другие SQL-команды.&#x20;

{% hint style="warning" %}
В SQL-функциях нельзя использовать команды управления транзакциями, например `COMMIT`, `SAVEPOINT`, и некоторые вспомогательные команды, в частности `VACUUM`.
{% endhint %}

Последней командой должна быть `SELECT` или команда с предложением `RETURNING`, возвращающая результат с типом возврата функции. В простом случае будет возвращена первая строка результата последнего запроса. Если последний запрос не вернёт ни одной строки, то будет возвращено значение NULL.

{% hint style="info" %}
Помните, что если отсутствует предложение `ORDER BY`, то порядок строк в результате не гарантирован, а значит ответ функции на тех же данных может меняться.
{% endhint %}

Функции можно разделить по типу возвращаемого значения:

* Без возврата значений;
* Возвращающие единственную запись базового типа, составного типа или типа record;
* C выходными параметрами;
* Возвращающие множество (SETOF) записей базового типа, составного типа или типа record;
* Возвращающие таблицу (TABLE).

Далее рассмотрим все варианты SQL-функций. А подробный материал по функциям на языке SQL можно найти по [ссылке](https://postgrespro.ru/docs/postgrespro/15/xfunc-sql).

## Аргументы функции <a href="#function-arguments" id="function-arguments"></a>

К аргументам SQL-функции можно обращаться по именам или индексу.

Объявив аргумент с именем, это имя можно использовать в теле функции:

```plsql
CREATE OR REPLACE FUNCTION template.user_get_full_name(in_user_id smallint)
  RETURNS character varying AS
$BODY$
  SELECT
    user_full_name
  FROM
    template.user_info
  WHERE
    user_id = in_user_id;
$BODY$
  LANGUAGE sql;
```

{% hint style="info" %}
Если имя аргумента совпадает с именем какого-либо столбца в текущей SQL-команде внутри функции, имя столбца всегда будет иметь приоритет. Чтобы избежать неоднозначности, дополните имя аргумента именем самой функции: _`имя_функции.имя_аргумента`_.
{% endhint %}

При обращении к аргументу по индексу используется запись вида `$n`. Нумерация аргументов начинается с единицы: `$1` обозначает первый аргумент, `$2` - второй и т. д. Обращение по номеру будет работать, и когда аргументам назначены имена.

```plsql
CREATE OR REPLACE FUNCTION template.is_administrator(bigint)
  RETURNS boolean AS
$BODY$
  SELECT
    "group".name = 'AdministratorGroup'
  FROM
    template.group
    JOIN template.user_group USING(group_id)
  WHERE
    user_id = $1;
$BODY$
  LANGUAGE sql;
```

## Без возврата значений <a href="#void" id="void"></a>

Если нужна SQL-функция, выполняющая действие, но не возвращающая полезное значение, можно объявить её как возвращающую тип `void`.

Например, эта функция удаляет черновые записи клиентов из таблицы:

```plsql
CREATE OR REPLACE FUNCTION template.delete_client_drafts()
  RETURNS void AS
$BODY$
  DELETE FROM template.client
  WHERE
    NOT added;
$BODY$
  LANGUAGE sql;
```

## Одна строка <a href="#returning-single" id="returning-single"></a>

### Базовый тип <a href="#returning-single-basic-type" id="returning-single-basic-type"></a>

Простейшая возможная функция SQL не имеет аргументов и возвращает базовый тип. Такая функция возвращает таблицу с одним столбцом и одной строкой.

Например, функция ниже возвращает идентификатор типа `smallint`:

```plsql
CREATE OR REPLACE FUNCTION template.order_status_new()
  RETURNS smallint AS
$BODY$
  SELECT
    order_status_id
  FROM
    template.order_status
  WHERE
    order_status.name = 'new';
$BODY$
  LANGUAGE sql;
```

{% hint style="info" %}
Если последняя команда `SELECT` или `RETURNING` в SQL-функции возвращает результат типа отличного от возвращаемого типа функции, то PostgreSQL автоматически приведёт возвращаемое значение к нужному типу, если это возможно с применением приведения присваивания или неявным образом. В противном случае должно быть явное приведение.
{% endhint %}

### Составной тип <a href="#returning-single-composite-type" id="returning-single-composite-type"></a>

Также возможно создать функцию, возвращающую составной тип. В таком случае функция возвращает таблицу из одной строки со столбцами для каждого атрибута составного типа.

Например, функция получения настроек SMTP-сервера будет возвращать одну строку типа _template.settings_:

```plsql
CREATE OR REPLACE FUNCTION template.get_settings()
  RETURNS template.settings AS
$BODY$
  SELECT
    settings_id,
    smtp_server,
    smtp_port,
    email_address,
    email_password,
    ssl
  FROM
    template.settings
  LIMIT 1;
$BODY$
  LANGUAGE sql;

SELECT * FROM template.get_settings();
```

Тогда в ответе получим:

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Составной тип возвращаемого значения накладывает ограничение: тип, количество и порядок столбцов ответа должны совпадать с описанием столбцов таблицы.
{% endhint %}

### Запись (тип record) <a href="#returning-single-record" id="returning-single-record"></a>

Если составной тип не подходит в качестве типа возвращаемого значения, так как содержит избыточные столбцы, то можно объявить функцию, возвращающую тип record. Такие переменные не имеют предопределённой структур&#x44B;**.** Количество и тип столбцов результата будет равно количеству полей последнего запроса в теле функции.

Например, функция получения настроек SMTP-сервера из предыдущего примера будет переписана следующим образом:

```plsql
CREATE OR REPLACE FUNCTION template.get_settings()
  RETURNS record AS
$BODY$
  SELECT
    smtp_server,
    smtp_port,
    email_address,
    email_password,
    ssl
  FROM
    template.settings
  LIMIT 1;
$BODY$
  LANGUAGE sql;

SELECT * FROM template.get_settings() AS aliases(
  "smtp_server" text,
  "smtp_port" integer,
  "email_address" text,
  "email_password" text,
  "ssl" boolean);
```

Важный момент: у функций, возвращающих запись, должен быть список определений столбцов - для этого, в запросе на получение результата функции задаются псевдонимы и типы колонок ответа.

Ответ функции будет иметь вид:

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

### C выходными параметрами <a href="#returning-single-with-out-params" id="returning-single-with-out-params"></a>

Альтернативный способ описать результаты функции - определить её с выходными параметрами. Например:

```plsql
CREATE OR REPLACE FUNCTION template.get_order_status_id(
  IN in_status_name text,
  OUT order_status_id smallint
) AS
$BODY$
  SELECT
    order_status_id
  FROM
    template.order_status
  WHERE
    order_status.name = in_status_name;
$BODY$
  LANGUAGE sql;
```

Но, основное преимущество выходных параметров в том, что они позволяют определять функции, возвращающие несколько столбцов. Например:

```plsql
CREATE OR REPLACE FUNCTION template.get_order_number(
  IN in_order_id bigint,
  OUT order_number character varying,
  OUT order_date timestamp
) AS
$BODY$
  SELECT
    order_number,
    order_date
  FROM
    template.order O
  WHERE
    O.order_id = in_order_id;
$BODY$
  LANGUAGE sql;


SELECT * FROM template.get_order_number(17::bigint);
```

В ответ получим:

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

## Множество (SETOF) <a href="#returning-setof" id="returning-setof"></a>

Можно объявить SQL-функцию как возвращающую множество. В этом случае будут возвращены все строки результата последнего запроса.&#x20;

Когда SQL-функция объявляется как возвращающая `SETOF`_`некий_тип`_, конечный запрос функции выполняется до завершения и каждая строка выводится как элемент результирующего множества.

Это обычно используется, когда функция вызывается в предложении `FROM`. В этом случае каждая строка, возвращаемая функцией, становится строкой таблицы, появляющейся в запросе.

### Множество базового типа <a href="#returning-setof-basic-type" id="returning-setof-basic-type"></a>

Функция может возвращать множество простого типа, тогда в ответе будет таблица с одним столбцом.

```plsql
CREATE OR REPLACE FUNCTION template.get_material(in_client_id bigint)
  RETURNS SETOF bigint AS
$BODY$
  SELECT
    material_id
  FROM
    template.order_position OP
    LEFT JOIN template.order O USING(order_id)
  WHERE
    O.client_id = in_client_id;
$BODY$
  LANGUAGE sql;


SELECT template.get_material(4);
```

В ответ получим:

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

### Множество составного типа  <a href="#returning-setof-composite-type" id="returning-setof-composite-type"></a>

Также возможно создать функцию, возвращающую множество составной тип. В таком случае функция возвращает таблицу с несколькими строками со столбцами для каждого атрибута составного типа.

```plsql
CREATE OR REPLACE FUNCTION template.get_active_users_by_group_name(in_group_name text)
  RETURNS SETOF template.user_info AS
$BODY$
  SELECT
    UI.user_id,
    UI.user_name,
    UI.user_full_name
  FROM
    template.user_info UI
    JOIN template.user_group UG USING(user_id)
    LEFT JOIN template.group G USING(group_id)
  WHERE
    NOT UI.archive AND
    G.name = in_group_name;
$BODY$
  LANGUAGE sql;


SELECT * FROM template.get_active_users_by_group_name('ManagerGroup');
```

В ответ получим:

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Составной тип возвращаемого значения накладывает ограничение: тип, количество и порядок столбцов ответа должны совпадать с описанием столбцов таблицы.
{% endhint %}

### Множество записей <a href="#returning-setof-record" id="returning-setof-record"></a>

Также возможно выдать несколько строк со столбцами, определяемыми выходными параметрами. Для этого необходимо указать функцию возвращающую `SETOF record`.

Например, функция возвращающая список идентификаторов товаров и их стоимость:

```plsql
CREATE OR REPLACE FUNCTION template.get_order_position_by_client_id(
  IN in_client_id bigint,
  OUT material_id bigint,
  OUT total_price numeric
)
RETURNS SETOF record AS
$BODY$
  SELECT
    OP.material_id,
    OP.quantity * OP.unit_price
  FROM
    template.order_position OP
    LEFT JOIN template.order O USING(order_id)
  WHERE
    O.client_id = in_client_id;
$BODY$
  LANGUAGE sql;


SELECT * FROM template.get_order_position_by_client_id(4::bigint);
```

В ответ получим таблицу:

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

Тип `record` позволяет использовать динамическую структуру выходной таблицы.

## TABLE <a href="#returning-table" id="returning-table"></a>

Есть ещё один способ объявить функцию, возвращающую множества, - использовать синтаксис `RETURNS TABLE(`_`столбцы`_`)`. Это равнозначно использованию одного или нескольких параметров `OUT` с объявлением функции, возвращающей `SETOF record`.

Например, предыдущий пример с товарами и их стоимостью можно переписать следующим образом:

```plsql
CREATE OR REPLACE FUNCTION template.get_order_position_by_client_id(in_client_id bigint)
  RETURNS TABLE (material_id bigint, total_price numeric) AS
$BODY$
  SELECT
    OP.material_id,
    OP.quantity * OP.unit_price
  FROM
    template.order_position OP
    LEFT JOIN template.order O USING(order_id)
  WHERE
    O.client_id = in_client_id;
$BODY$
  LANGUAGE sql;
```

Запись `RETURNS TABLE` не позволяет явно указывать `OUT` и `INOUT` для параметров - все выходные столбцы необходимо записать в списке `TABLE`.
