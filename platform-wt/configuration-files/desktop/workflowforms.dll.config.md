# WorkflowForms.dll.config



## Шаблон <a href="#template" id="template"></a>

```xml
<?xml version="1.0"?>
<configuration>
  <configSections>
    <sectionGroup name="applicationSettings" type="System.Configuration.ApplicationSettingsGroup, System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" >
      <section name="WorkflowForms.Properties.Settings" type="System.Configuration.ClientSettingsSection, System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </sectionGroup>
  </configSections>
  <startup>
    <supportedRuntime version="v4.0" />
  </startup>
  <applicationSettings>
    <WorkflowForms.Properties.Settings>
      <setting name="LogName" serializeAs="String">
        <value>Workflow Technology</value>
      </setting>
      <setting name="LogSourceName" serializeAs="String">
        <value>Workflow Forms</value>
      </setting>
      <setting name="LogEntryFormat" serializeAs="String">
        <value>
          Form: {form}

          {message}

          User: {user}
        </value>
      </setting>
      <setting name="LogEntryDateTimeFormat" serializeAs="String">
        <value>yyyy-MM-dd HH:mm:ss</value>
      </setting>
      <setting name="UseSourceCache" serializeAs="String">
        <value>False</value>
      </setting>
      <setting name="CheckBinaryFiles" serializeAs="String">
        <value>False</value>
      </setting>
      <setting name="AppDataFolder" serializeAs="String">
        <value>WorkflowForms</value>
      </setting>
      <setting name="CheckForUpdatesInterval" serializeAs="String">
        <value>00:00:00</value>
      </setting>
      <setting name="AnonymousUserName" serializeAs="String">
        <value>WS_GUEST</value>
      </setting>
      <setting name="AnonymousPassword" serializeAs="String">
        <value>123</value>
      </setting>
      <setting name="ServerUrl" serializeAs="String">
        <value>http://localhost:50707</value>
      </setting>
      <setting name="ConnectionTimeout" serializeAs="String">
        <value>00:05:00</value>
      </setting>
      <setting name="StartFormFileName" serializeAs="String">
        <value>D:\WT\Projects\Template\Projects\1. Template\Forms\TemplateStart.xml</value>
      </setting>
      <setting name="DebugMode" serializeAs="String">
        <value>True</value>
      </setting>
      <setting name="DebugPath" serializeAs="String">
        <value>D:\Template\DebugDC</value>
      </setting>
      <setting name="SplashIcon" serializeAs="String">
        <value />
      </setting>
      <setting name="SystemLocale" serializeAs="String">
        <value>en-US</value>
      </setting>
      <setting name="LogLocale" serializeAs="String">
        <value>en-US</value>
      </setting>
      <setting name="ServiceUrl" serializeAs="String">
        <value>http://localhost:5001</value>
      </setting>
      <setting name="ServiceName" serializeAs="String">
        <value>WorkflowFormsUpdateService</value>
      </setting>
      <setting name="PerformanceCheckingMode" serializeAs="String">
        <value>False</value>
      </setting>
    </WorkflowForms.Properties.Settings>
  </applicationSettings>
</configuration>
```

## Основные настройки <a href="#main-settings" id="main-settings"></a>

### ServerUrl <a href="#server_url" id="server_url"></a>

IP-адрес (или доменное имя) и порт серверной части, к которой будет обращаться клиентская часть.

Необязательное поле. Ожидается URL-адрес.

Значение по умолчанию: `"http://localhost:5000"`

```xml
<setting name="ServerUrl" serializeAs="String">
  <value>http://localhost:50707</value>
</setting>
```

### ConnectionTimeout <a href="#connection_timeout" id="connection_timeout"></a>

Задает значение Timeout для попытки установления соединения с сервером.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"00:05:00"`

```xml
<setting name="ConnectionTimeout" serializeAs="String">
  <value>00:05:00</value>
</setting>
```

### StartFormFileName <a href="#start_form_file_name" id="start_form_file_name"></a>

Путь до стартовой формы.\
В зависимости от значения [UseSourceCache](workflowforms.dll.config.md#use_source_cache), Ожидается абсолютный путь или относительный путь относительно [AppDataFolder](workflowforms.dll.config.md#app_data_folder).&#x20;

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: отсутствует

```xml
<setting name="StartFormFileName" serializeAs="String">
  <value>D:\WT\Projects\Template\Projects\1. Template\Forms\TemplateStart.xml</value>
</setting>
```

### SystemLocale <a href="#system_locale" id="system_locale"></a>

Задает код культуры, который определяет язык сообщений, отображаемых в программе.

Необязательное поле. Ожидается одно из допустимых значение:&#x20;

<table data-header-hidden><thead><tr><th width="199"></th><th></th></tr></thead><tbody><tr><td>en-US</td><td>английский (США)</td></tr><tr><td>ru-RU</td><td>русский (Россия)</td></tr></tbody></table>

Значение по умолчанию: `"en-US"`

```xml
<setting name="SystemLocale" serializeAs="String">
  <value>en-US</value>
</setting>
```

### SplashIcon <a href="#splash_icon" id="splash_icon"></a>

Задает путь до файла с изображением, которое будет отображаться вместо логотипа компании в окне проверки обновления и загрузки приложения.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: пустая строка.

```xml
<setting name="SplashIcon" serializeAs="String">
  <value>D:\WorkflowForms\Carrent.v3\Icons\custom_splash.png</value>
</setting>
```

## Проверка обновлений <a href="#checking-for-updates" id="checking-for-updates"></a>

### ServiceUrl <a href="#service_url" id="service_url"></a>

IP-адрес (или доменное имя) и порт, на котором будет запускаться служба для обновления WT-приложения.

{% hint style="info" %}
Рекомендуется всегда указывать localhost для повышения вероятности запуска службы.
{% endhint %}

Необязательное поле. Ожидается URL-адрес.

Значение по умолчанию: `"http://localhost:5001"`

```xml
<setting name="ServiceUrl" serializeAs="String">
  <value>http://localhost:50707</value>
</setting>
```

### ServiceName <a href="#service_name" id="service_name"></a>

Задает название службы для обновления WT-приложения.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"WorkflowFormsUpdateService"`

```xml
<setting name="LogName" serializeAs="String">
  <value>WorkflowFormsUpdateService</value>
</setting>
```

### UseSourceCache <a href="#use_source_cache" id="use_source_cache"></a>

Скачивать ли xml-файлы с сервера. Если значение True, то файлы будут скачиваться в папку, указанную в поле [AppDataFolder](workflowforms.dll.config.md#app_data_folder).

* **False** - для [StartFormFileName](workflowforms.dll.config.md#start_form_file_name) необходимо указать абсолютный путь до стартовой формы.
* **True** - для [StartFormFileName](workflowforms.dll.config.md#start_form_file_name) необходимо указать абсолютный путь или относительный путь относительно [AppDataFolder](workflowforms.dll.config.md#app_data_folder).

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `False`

```xml
<setting name="UseSourceCache" serializeAs="String">
  <value>False</value>
</setting>
```

### CheckBinaryFiles <a href="#check_binary_files" id="check_binary_files"></a>

Проверять ли обновление бинарных файлов (.dll).

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `False`

```xml
<setting name="CheckBinaryFiles" serializeAs="String">
  <value>False</value>
</setting>
```

### AppDataFolder <a href="#app_data_folder" id="app_data_folder"></a>

Путь до папки, в которую будут скачиваться xml и dll файлы.\
Необходимо указать абсолютный путь или относительный путь относительно папки **%appdata%**.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"WorkflowForms"`

```xml
<setting name="AppDataFolder" serializeAs="String">
  <value>WorkflowForms</value>
</setting>
```

### CheckForUpdatesInterval <a href="#check_for_updates_interval" id="check_for_updates_interval"></a>

Задает интервал проверки обновлений.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"00:00:00"`

```xml
<setting name="CheckForUpdatesInterval" serializeAs="String">
  <value>00:10:00</value>
</setting>
```

## Анонимный пользователь <a href="#anonymous-user" id="anonymous-user"></a>

Анонимная учетка необходима для подписания запросов к серверной части, когда пользователь не авторизовался в приложении. Например, запрос на получение списка пользователей для окна входа в программу.

### AnonymousUserName <a href="#anonymous_user_name" id="anonymous_user_name"></a>

Логин анонимного пользователя.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"WS_GUEST"`

```xml
<setting name="AnonymousUserName" serializeAs="String">
  <value>WS_GUEST</value>
</setting>
```

### AnonymousPassword <a href="#anonymous_password" id="anonymous_password"></a>

Пароль анонимного пользователя.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"wsGuestPwd123"`

```xml
<setting name="AnonymousPassword" serializeAs="String">
  <value>123</value>
</setting>
```

## Настройки логирования <a href="#logging-settings" id="logging-settings"></a>

### LogLocale <a href="#log_locale" id="log_locale"></a>

Задает код культуры, который определяет язык сообщений об ошибке публикуемых в журнале событий Windows.

Необязательное поле. Ожидается одно из допустимых значение:&#x20;

<table data-header-hidden><thead><tr><th width="199"></th><th></th></tr></thead><tbody><tr><td>en-US</td><td>английский (США)</td></tr><tr><td>ru-RU</td><td>русский (Россия)</td></tr></tbody></table>

Значение по умолчанию: `"en-US"`

```xml
<setting name="LogLocale" serializeAs="String">
  <value>en-US</value>
</setting>
```

### LogName <a href="#log_name" id="log_name"></a>

Задает название журнала событий Windows, в который будут писаться сообщения об ошибке.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"Workflow Technology"`

```xml
<setting name="LogName" serializeAs="String">
  <value>Workflow Technology</value>
</setting>
```

### LogSourceName <a href="#log_source_name" id="log_source_name"></a>

Задает название источника, от имени которого будут писаться сообщения об ошибке в журнале событий Windows.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"Workflow Forms"`

```xml
<setting name="LogSourceName" serializeAs="String">
  <value>Workflow Forms</value>
</setting>
```

### LogEntryFormat <a href="#log_entry_format" id="log_entry_format"></a>

Задает шаблон текста сообщения об ошибке в журнале событий Windows.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"Form: {form}\r\n\r\n{message}\r\n\r\nUser: {user}"`

```xml
<setting name="LogEntryFormat" serializeAs="String">
  <value>
    Form: {form}

    {message}

    User: {user}
  </value>
</setting>
```

### LogEntryDateTimeFormat <a href="#log_entry_date_time_format" id="log_entry_date_time_format"></a>

Задает шаблон даты и времени сообщения об ошибке в журнале событий Windows.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"yyyy-MM-dd HH:mm:ss"`

```xml
<setting name="LogEntryDateTimeFormat" serializeAs="String">
  <value>yyyy-MM-dd HH:mm:ss</value>
</setting>
```

## Отладка <a href="#debug" id="debug"></a>

В режиме отладки формы пишут в файлы информацию:

* время начала и время окончания выполнения команд;
* время начала и время окончания загрузки данных;
* результаты проверки условий;
* результат выполнения Execution.

{% hint style="info" %}
Для работы с логами отладки используется дополнительная утилита - _WorkflowVisualizer._
{% endhint %}

### DebugMode <a href="#debug_mode" id="debug_mode"></a>

Признак, включающий на клиентской части режим отладки.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `False`

```xml
<setting name="DebugMode" serializeAs="String">
  <value>True</value>
</setting>
```

### DebugPath <a href="#debug_path" id="debug_path"></a>

Путь до папки, в которую будут сохраняться файлы логов клиентской части.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"D:\DebugDC"`

```xml
<setting name="DebugPath" serializeAs="String">
  <value>D:\DebugDC</value>
</setting>
```

### PerformanceCheckingMode <a href="#performance_checking_mode" id="performance_checking_mode"></a>

Признак, включающий проверку производительности: формы каждый час выполняют проверку, насколько загружена оперативная память и процессор.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `False`

```xml
<setting name="PerformanceCheckingMode" serializeAs="String">
  <value>False</value>
</setting>
```

Условия выполнения проверки:

1. Проверка запускается через 5 минут после запуска форм;
2. Если зафиксировано превышение, то далее проверки выполняются каждую минуту. Если зафиксировано 15 превышений подряд, то запускается логирование;
3. Если превышения нет, то следующая проверка выполнится через час.

Ограничения: загрузка CPU больше 7% или загрузка памяти больше 500 МБ.

Логи пишутся в папку _C:\ProgramData\Workflow Systems_ в файлы:

* PerformanceHighUsage.txt - значение проверки;
* PerformanceHighUsage\_YYYY-MM-DD-HH24.txt - записывается время начала и окончания всех операций и действий на форме.
