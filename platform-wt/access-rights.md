# Права доступа

Есть две задачи в организации прав доступа для групп пользователей:&#x20;

* Настройка доступа к элементам интерфейса и выполнению команд на клиентской части;
* Настройка разрешений на выполнение запросов и команд на стороне сервера.

Обе задачи должны решаться одновременно. Например, если у пользователя нет права на выполнение запроса на получения списка клиентов, то он не должен видеть элементы интерфейса, которые открывают форму списка пользователей. Так же, если пользователь не имеет возможности редактировать данные клиентов, то он не должен иметь права на выполнение соответствующих запросов на сервере.

Управлять доступом к графическим объектам на формах можно через тэги `<Visible>` и `<Enabled>` этих объектов, а для проверки прав доступа существует специальный элемент [`<AccessPoint>`](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/values/access_point).

Для управления разрешениями на выполнение запросов и команд на сервере можно использовать статически или динамический подход. Суть статического подхода заключается в том, что разработчик через серверный xml-файл настраивает разрешения для групп пользователей. А динамический подход предоставляет пользователю возможность через интерфейс программы самому настраивать права доступа.

Настройку прав доступа всегда следует начинать с расстановки AccessPoint на формах. Это позволит определить действия, которые могут совершать пользователи. Затем в серверном xml-файле описываются все точки доступа, которые добавляются в Permission с запросами, необходимыми для этих действий. После этого можно приступать к назначению разрешений группам пользователей, используя статический или динамический подход.

## AccessPoints

### Точки доступа на форме <a href="#accesspoint-on-form" id="accesspoint-on-form"></a>

Первым делом для каждого элемента формы в тэги `<Visible>` и `<Enabled>` необходимо прописать AccessPoint с именем, описывающем суть действия. Например, для пунктов главного меню на открытие форм списка городов и клиентов:

```xml
<MenuItem Name="ListMenuItem" Type="MenuItem">
  <Title>Списки</Title>
  <Visible>
    <Or>
      <AccessPoint Name="CityViewAccessPoint" />
      <AccessPoint Name="ClientViewAccessPoint" />
    </Or>
  </Visible>
  <Items>  
    <MenuItem Name="CityMenuItem" Type="MenuItem">
      <Title>Города...</Title>
      <Command Name="CityListFormShowCommand" />
      <Visible>
        <AccessPoint Name="CityViewAccessPoint" />
      </Visible>
    </MenuItem>
  
    <MenuItem Name="ClientListMenuItem" Type="MenuItem">
      <Title>Клиенты...</Title>
      <Command Name="ClientListFormShowCommand" />
      <Visible>
        <AccessPoint Name="ClientViewAccessPoint" />
      </Visible>
    </MenuItem>
  </Items>
</MenuItem>
```

Таким образом, определяются точки доступа, необходимые для совершения действий на форме. Все эти точки доступа должны быть перечислены в [AccessPointDataConnection](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/dataconnections/access_point_dc), который будет с сервера получать информацию о правах доступа для текущего пользователя:

```xml
<DataConnection Name="AccessPointDataConnection" Type="AccessPointDataConnection" Assembly="DataConnections">
  <Workflow Name="Template" />
  <AccessPoints>
    <AccessPoint Name="CityViewAccessPoint" />
    <AccessPoint Name="ClientViewAccessPoint" />
  </AccessPoints>
</DataConnection>
```

При расстановке AccessPoint на форме не стоит забывать и про [Execution](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/executions), так как в них могут вызываться команды и запросы, на выполнение которых у пользователя может не быть разрешений. Например, на форме списка городов есть Execution, в котором по двойному клику по строке в таблице DatabaseTable откроется карточка города:

```xml
<Execution>
  <ConditionExpression>
    <Condition Name="CityDatabaseTableCellDoubleClickCondition" />
  </ConditionExpression>
  <Commands>
    <If>
      <When>
        <Not>
          <AccessPoint Name="CityEditAccessPoint" />
        </Not>
      </When>
      <Then>
        <Command Name="WarningMessageBoxCommand">
          <Input Name="Caption">Редактирование</Input>
          <Input Name="Text">>У вас недостаточно прав для выполнения команды.</Input>
        </Command>
      </Then>
      <Else>
        <Command Name="CityEditFormShowCommand" />
      </Else>
    </If>
  </Commands>
</Execution>
```

Так же на форме списка городов будет свой список точек доступа, которые используются на кнопках редактирования таблицы:

```xml
<DataConnection Name="AccessPointDataConnection" Type="AccessPointDataConnection" Assembly="DataConnections">
  <Workflow Name="Template" />
  <AccessPoints>
    <AccessPoint Name="CityAddAccessPoint" />
    <AccessPoint Name="CityEditAccessPoint" />
    <AccessPoint Name="CityDeleteAccessPoint" />
  </AccessPoints>
</DataConnection>
```

### Точки доступа на сервере <a href="#accesspoint-on-server" id="accesspoint-on-server"></a>

В серверном xml-файле необходимо объявить тэг `<AccessPoints>` (используется на одном уровне с `<SqlQueries>` и `<Permissions>`) и перечислить в нем все AccessPoint, которые используются на формах:

```xml
<AccessPoints>
  <AccessPoint Name="CityViewAccessPoint" />
  <AccessPoint Name="ClientViewAccessPoint" />
  <AccessPoint Name="CityAddAccessPoint" />
  <AccessPoint Name="CityEditAccessPoint" />
  <AccessPoint Name="CityDeleteAccessPoint" />
</AccessPoints>
```

Затем эти AccessPoint необходимо распределить по [`<Permission>`](https://wfsys.gitbook.io/workflow-engine-syntax/workflow_engine/permissions/permission) с запросами, которые используются для выполнения действий.

## Статические права доступа <a href="#static-permissions" id="static-permissions"></a>

Как говорилось ранее, суть статических прав доступа заключается в том, что надо заранее в серверном xml-файле настроить разрешения для групп пользователей. А именно прописать разрешения, распределить разрешения по ролям, а роли назначить группам пользователей.

### Permissions <a href="#permissions-for-static" id="permissions-for-static"></a>

Определение разрешений самый простой этап, так как здесь группируем запросы по сущности, с которой они работают, и по типу - просмотр или редактирование.

При формировании разрешений следует придерживаться рекомендаций:

* Запросы на получение данных из базы объединяем в один `<Permission>`, который именуем по шаблону: _имя\_сущностиViewPermission_;
* Если к запросам на получение данных у групп пользователей может быть разный доступ, то запросы разделяем на разные `<Permission>` при этом соблюдаем рекомендации по имени.
* Запросы на редактирование данных объединяем в один `<Permission>`, который именуем по шаблону: _имя\_сущностиEditPermission_;
* Если к запросам на редактирование данных у групп пользователей может быть разный доступ, то запросы разделяем на разные `<Permission>` при этом соблюдаем рекомендации по имени.

Пример описания разрешений с запросами на просмотр и редактирование списка городов и запросами для просмотра списка клиентов:

```xml
<Permissions>
  <Permission Name="CityViewPermission">
    <AccessPoint Name="CityViewAccessPoint" />
    <SqlQuery Name="CitySelectSqlQuery" />
    <SqlQuery Name="CityShortSelectSqlQuery" />
  </Permission>
  
  <Permission Name="CityEditPermission">
    <AccessPoint Name="CityAddAccessPoint" />
    <AccessPoint Name="CityEditAccessPoint" />
    <AccessPoint Name="CityDeleteAccessPoint" />
    <SqlQuery Name="CityInsertSqlQuery" />
    <SqlQuery Name="CityUpdateSqlQuery" />
    <SqlQuery Name="CityDeleteSqlQuery" />
  </Permission>
  
  <Permission Name="ClientViewPermission">
    <AccessPoint Name="ClientViewAccessPoint" />
    <SqlQuery Name="ClientSelectSqlQuery" />
    <SqlQuery Name="ClientByIdSelectSqlQuery" />
  </Permission>
</Permissions>
```

Если бы группа "Администраторы" могла просматривать форму со списком городов (CitySelectSqlQuery), а группа "Пользователи" не имела бы доступа к этой форме, но видела выпадающий список городов (CityShortSelectSqlQuery) в карточке клиента, то запрос CityShortSelectSqlQuery следовало бы вынести в отдельный `<Permission>` , например в CityShortViewPermission.

После того, как описаны все разрешения можно приступать к определению ролей [`<Role>`](https://wfsys.gitbook.io/workflow-engine-syntax/workflow_engine/roles).

### Roles

Смысл роли в том, что она описывает конкретное действие на форме, например, действие на просмотр списка городов или действие на редактирование списка. Определение ролей самый важный этап, так как роль должна содержать _набор разрешений, достаточный_ для выполнение действия. Так для открытия формы списка пользователю достаточно иметь доступ к запросу на получение этого списка, а для возможности редактировать этот список, пользователь должен иметь права на запросы для получение списка и на запросы редактирования списка.

При формировании ролей следует придерживаться рекомендаций:

* Если роль подразумевает только просмотр данных, то именуем ее по шаблону: _имя\_сущностиViewRole_;
* Если роль подразумевает редактирование данных, то именуем ее по шаблону: _имя\_сущностиEditRole._

Пример определения ролей для просмотра и редактирования списка городов и роли для просмотра списка клиентов:

```markup
<Roles>
  <Role Name="CityViewRole">
    <Permissions>
      <Permission Name="CityViewPermission" />
    </Permissions>
  </Role>
  
  <Role Name="CityEditRole">
    <Permissions>
      <Permission Name="CityViewPermission" />
      <Permission Name="CityEditPermission" />
    </Permissions>
  </Role>
  
  <Role Name="ClientViewRole">
    <Permissions>
      <Permission Name="ClientViewPermission" />
    </Permissions>
  </Role>
</Roles>
```

Если бы была необходимость разделить CitySelectSqlQuery и CityShortSelectSqlQuery на разные `<Permission>`, то в ClientViewRole добавили бы разрешение CityShortViewPermission, чтобы у пользователя были права на получение списка городов для выпадающего списка, даже если бы у него не было роли CityViewRole.

После того, как определены все роли и их разрешения можно распределять роль по группам.

### Groups

Имея детально проработанные роли и опираясь на бизнес-логику и предполагаемые в ней права доступа для пользователей, можно легко раскидать роли по группам.

В тэге `<Groups>` вложенные тэги [`<Group>`](https://wfsys.gitbook.io/workflow-engine-syntax/workflow_engine/groups) должны иметь названия, совпадающие со значениями в поле **name** в таблице **template.group**:

<figure><img src="../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

Пример распределения ролей для групп "Администраторы" и "Пользователи":

```xml
<Groups>
  <Group Name="AdministratorGroup">
    <Roles>
      <Role Name="CityEditRole" />
      <Role Name="ClientViewRole" />
    </Roles>
  </Group>

  <Group Name="UserGroup">
    <Roles>
      <Role Name="CityViewRole"/>
      <Role Name="ClientViewRole" />
    </Roles>
  </Group>

  <Group Name="GuestGroup">
    <Roles>
    </Roles>
  </Group>
</Groups>
```

Группе "Пользователи" списки городов и клиентов доступны только для просмотра, а пользователи из группы "Администраторы" могут не только просматривать списки, но и редактировать список городов. У группы "Гости" нет никаких прав доступа.

### Как работают статические права доступа <a href="#how-work-static-permissions" id="how-work-static-permissions"></a>

При старте формы AccessPointDataConnection отправляет запрос на сервер, движок проверяет в базе данных, в какой группе находится пользователь. Зная группу, сервер смотрит в xml-файле, какие роли с какими Permission доступны этой группе. Затем, сервер формирует таблицу с одной строкой, в которой для каждого AccessPoint из DataConnection будет стоять значение True или False, в зависимости от наличия соответствующего Permission у группы текущего пользователя.

Когда приходит запрос на выполнение SqlQuery, сервер так же восстанавливает группу пользователя и находит доступные этой группе Permission. Проверив, есть ли в этих Permission требуемый SqlQuery, сервер либо выполняет SqlQuery, либо вернет ошибку.

## Динамические права доступа <a href="#dynamic-permissions" id="dynamic-permissions"></a>

Настройка динамических прав доступа вынесена из серверного xml-файла в базу данных, что позволяет реализовать механизм управления разрешениями через интерфейс программы - тем самым переложить на пользователя ответственность за назначение прав доступа. Но, чтобы создать удобный и гибкий инструмент настройки прав доступа, разработчик должен четко расписать разрешения на все действия доступные в программе и указать в них все необходимые запросы.

Чтобы перевести серверную часть на работу с динамическими правами доступа, необходимо в серверном xml-файле прописать тэг `<UserSettings>` сразу после открывающего тэга `<Workflow>`:

```xml
<UserSettings Table="template.user"
              GroupTable="template.group"
              GroupGroupTable="template.group_group"
              UserGroupTable="template.user_group"
              PermissionTable="template.permission"
              GroupPermissionTable="template.group_permission" />
```

Таким образом, серверная часть будет знать, в каких таблицах находится необходимая информация:

* атрибут `Table` - таблица со списком пользователей;
* атрибут `GroupTable` - таблица со списком групп пользователей, поддерживаемых в программе;
* атрибут `GroupGroupTable` - таблица с описанием иерархии групп пользователей;
* атрибут `UserGroupTable` - таблица с описанием, в каких группах состоит пользователь;
* атрибут `PermissionTable` - таблица со списком разрешений, прописанных в серверном xml-файле;
* атрибут `GroupPermissionTable` - таблица с описанием, какие разрешения доступны группам пользователей.

Имея список точек доступа и переведя серверную часть в работу с динамическими права доступа, можно переходить к определению разрешений.

### Permissions <a href="#permissions-for-dynamic" id="permissions-for-dynamic"></a>

Механизм динамических прав доступа работает только с разрешениями и группами пользователей - тэги `<Roles>` и `<Groups>` не участвуют.  А значит, `<Permission>` должен  включать все запросы, которые связаны с действием - основные и все дополнительные запросы, например, запросы для выпадающих списков.

{% hint style="info" %}
Описание `<Roles>` и `<Groups>` в серверном xml-файле при динамических правах необязательно, но их наличие не будет вызывать ошибку. При совместном использовании статических и динамических прав доступа используется _принцип разрешения_. Если в xml-файле у группы нет прав на выполнения какого-то запроса, а в таблице в базе данных такие права есть, то сервер будет разрешать пользователю выполнять этот запрос. Или если у группы в базе данных нет разрешения, но в серверном xml-файле указан Permission, то сервер так же разрешит выполнение запроса.
{% endhint %}

При формировании разрешений следует придерживаться рекомендаций:

* Запросы на получение данных из базы объединяем в один `<Permission>`, который именуем по шаблону: _имя\_сущностиViewPermission_;
* Если к запросам на получение данных у групп пользователей может быть разный доступ, то запросы разделяем на разные `<Permission>` при этом соблюдаем рекомендации по имени.
* Запросы на редактирование данных объединяем в один `<Permission>`, который именуем по шаблону: _имя\_сущностиEditPermission_;
* Если к запросам на редактирование данных у групп пользователей может быть разный доступ, то запросы разделяем на разные `<Permission>` при этом соблюдаем рекомендации по имени.

Пример описания разрешений с запросами на просмотр и редактирование списка городов и запросами для просмотра списка клиентов:

```xml
<Permissions>
  <Permission Name="CityViewPermission">
    <AccessPoint Name="CityViewAccessPoint" />
    <SqlQuery Name="CitySelectSqlQuery" />
    <SqlQuery Name="CityShortSelectSqlQuery" />
  </Permission>
  
  <Permission Name="CityEditPermission">
    <!-- Чтение списка -->
    <AccessPoint Name="CityViewAccessPoint" />
    <SqlQuery Name="CitySelectSqlQuery" />
    <!-- Редактирование списка -->
    <AccessPoint Name="CityAddAccessPoint" />
    <AccessPoint Name="CityEditAccessPoint" />
    <AccessPoint Name="CityDeleteAccessPoint" />
    <SqlQuery Name="CityInsertSqlQuery" />
    <SqlQuery Name="CityUpdateSqlQuery" />
    <SqlQuery Name="CityDeleteSqlQuery" />
  </Permission>
  
  <Permission Name="ClientViewPermission">
    <AccessPoint Name="ClientViewAccessPoint" />
    <SqlQuery Name="ClientSelectSqlQuery" />
    <SqlQuery Name="ClientByIdSelectSqlQuery" />
    <!-- Дополнительно -->
    <SqlQuery Name="CityShortSelectSqlQuery" />
  </Permission>
</Permissions>
```

В примере в разрешение ClientViewPermission добавили запрос CityShortSelectSqlQuery, чтобы гарантировать, что у пользователя будут права на получение списка городов для выпадающего списка на форме.

В разрешение CityEditPermission добавлен запрос на получение списка городов (CitySelectSqlQuery) и соответствующая точка доступа, так как разрешения на чтение и редактирование могут предоставляться разным группам.

Обычно разделение разрешение на чтение и редактирование достаточно, но могут возникать ситуации, требующие более детального дробления разрешений. Например, только администраторам будет доступно удаление городов, а обычные пользователи смогут только создавать и изменять города. В таком случае, запрос CityDeleteSqlQuery и точку доступа CityDeleteAccessPoint нужно будет вынести в отдельное разрешение, не забыв про запрос на получение списка городов.

### База данных <a href="#database" id="database"></a>

После того, как описали все `<Permission>` в серверном xml-файле, их имена необходимо перенести в таблицу **template.permission**:

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

В  таблице **template.group\_permission** задаются соответствия групп пользователей и доступных им разрешений:

<figure><img src="../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

Помимо системных таблиц (template.permission и template.group\_permission) создается пара  дополнительных таблиц, которые позволяют сделать удобный интерфейс для пользователя.

<details>

<summary>Скрипты создания таблиц</summary>

```sql
CREATE SEQUENCE template.permission_block_id_seq;
CREATE TABLE template.permission_block
(
  permission_block_id smallint NOT NULL DEFAULT nextval('template.permission_block_id_seq'::regclass),
  id_title character varying NOT NULL,
  title character varying NOT NULL,
  by_default boolean NOT NULL DEFAULT false,
  CONSTRAINT pk_permission_block_id PRIMARY KEY (permission_block_id),
  CONSTRAINT uniq_permission_block_name UNIQUE (id_title)
);

CREATE SEQUENCE template.permission_block_item_id_seq;
CREATE TABLE template.permission_block_item
(
  permission_block_item_id smallint NOT NULL DEFAULT nextval('template.permission_block_item_id_seq'::regclass),
  permission_block_id smallint NOT NULL,
  id_title character varying NOT NULL,
  title character varying NOT NULL,
  CONSTRAINT pk_permission_block_item_id PRIMARY KEY (permission_block_item_id),
  CONSTRAINT fk_permission_block_id FOREIGN KEY (permission_block_id)
      REFERENCES template.permission_block (permission_block_id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION,
  CONSTRAINT uniq_permission_block_item_name UNIQUE (id_title)
);
```

</details>

Первая таблица описывает категории разрешений - template.permission\_block:

<figure><img src="../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

Вторая описывает разрешение или группу разрешений - template.permission\_block\_item:

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

В примере разрешения на чтения и редактирования каждой сущности объединены в одну группу. Например, разрешения CityViewPermission и CityEditPermission относятся к одной группе "Города", но, при необходимости, их можно разделить на две - "Просмотр списка городов" и "Редактирование списка городов", - чтобы пользователь мог разным группам дать разные права. Такое разделение отработает без ошибок, так как разрешение CityEditPermission уже содержит запрос на чтение списка городов. А если бы был отдельный Permission на удаление городов, то создали бы третью группу - "Удаление городов".

### Как работают динамические права доступа <a href="#how-work-dynamic-permissions" id="how-work-dynamic-permissions"></a>

При старте формы AccessPointDataConnection отправляет запрос на сервер, движок проверяет в базе данных, в какой группе находится пользователь. Зная группу, сервер смотрит в таблицы template.group\_permission и template.permission и получает список имен доступных Permission. Затем в своем xml-файле проверяет эти Permission и формирует таблицу с одной строкой, в которой для каждого AccessPoint из DataConnection будет стоять значение True или False, в зависимости от наличия соответствующего Permission у группы текущего пользователя.

Когда приходит запрос на выполнение SqlQuery, сервер так же восстанавливает доступные  Permission и проверяет, есть ли в этих Permission требуемый SqlQuery. И возвращает ошибку, если у пользователя нет прав.

## Базовые права доступа <a href="#basic-access-rights" id="basic-access-rights"></a>

В проекте могут быть запросы, разрешение на выполнение которых должны иметь все пользователи. Например, запрос на получение списка пользователей для формы входа и запрос на получение данных текущего пользователя.

Такие запросы следует объединять в один Permission и давать ему имя BaseViewPermission или BasePermission.

```xml
<Permission Name="BaseViewPermission">
  <SqlQuery Name="UserLoginSelectSqlQuery" />
  <SqlQuery Name="UserCurrentSelectSqlQuery" />
</Permission>
```

Если используется механизм статических правах доступа, то создается BaseRole:

```xml
<Role Name="BaseRole">
  <Permissions>
    <Permission Name="BaseViewPermission" />
  </Permissions>
</Role>
```

Если используется механизм статических прав доступа, то базовая роль добавляется во все группы:

```xml
<Groups>
  <Group Name="AdministratorGroup">
    <Roles>
      <Role Name="BaseRole" />
      <Role Name="CityEditRole" />
      <Role Name="ClientViewRole" />
    </Roles>
  </Group>

  <Group Name="UserGroup">
    <Roles>
      <Role Name="BaseRole" />
      <Role Name="CityViewRole"/>
      <Role Name="ClientViewRole" />
    </Roles>
  </Group>

  <Group Name="GuestGroup">
    <Roles>
      <Role Name="BaseRole" />
    </Roles>
  </Group>
</Groups>
```

В примере базовая роль дана даже группе GuestGroup, так как она предоставляет доступ к запросу на список пользователей для формы входа.

Если используется механизм динамических прав доступа, то базовое разрешение должно по умолчанию добавляться в новые группы. Так же, пользователь не должен иметь возможность убирать базовое разрешение у групп при их редактировании.

