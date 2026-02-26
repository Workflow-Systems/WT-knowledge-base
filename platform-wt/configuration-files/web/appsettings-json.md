# appsettings.json

## Шаблон <a href="#template" id="template"></a>

```json
{
  "Serilog": {
    "MinimumLevel": "Warning",
    "WriteTo": [
      {
        "Name": "Async",
        "Args": {
          "configure": [
            {
              "Name": "RollingFile",
              "Args": {
                "pathFormat": "Logs\\log-{Date}.txt",
                "fileSizeLimitBytes": null,
                "retainedFileCountLimit": null,
                "outputTemplate": "{Timestamp:o} [{Level:u3}] {SourceContext} ({RequestId}/{ThreadId}) {Message}{NewLine}{Exception}{NewLine}",
                "buffered": false
              }
            },
            {
              "Name": "LiterateConsole",
              "Args": {
                "outputTemplate": "{Timestamp:o} [{Level:u3}] {SourceContext} ({RequestId}/{ThreadId}) {Message}{NewLine}{Exception}{NewLine}"
              }
            },
            { "Name": "Debug" },
            {
              "Name": "EventLog",
              "Args": {
                "logName": "Workflow Technology",
                "source": "Workflow Web Forms",
                "outputTemplate": "{Timestamp:o}{NewLine}[{Level:u3}] {SourceContext} ({RequestId}/{ThreadId}){NewLine}{Message}{NewLine}{Exception}{NewLine}"
              }
            }
          ]
        }
      }
    ],
    "Enrich": [ "FromLogContext", "WithMachineName", "WithThreadId", "WithRequestId" ]
  },
  "AllowedHosts": "*",
  "FormsAppParameters": {
    "ServerUrl": "http://localhost:50002",
    "ConnectionTimeout": "00:05:00",
    "HostUrl": "http://192.168.7.88:4990",
    "StartAssembly": "Carrent",
    "XmlFolder": "D:\\WorkflowTechnology\\Clients\\Carrent\\Projects\\1. Carrent\\WebForms",
    "TempFileFolder": "D:\\Web\\TempFile",
    "EngineOnSameMachine": true,
    "EngineStorages": [ "D:\\WorkflowEngine\\Carrent.v3\\Upload" ],
    "SystemLocale": "en-US",
    "Translate": false,
    "HelpDeskEnabled": true,
    "LoginPageUrl": "login",
    "TimeoutEnabled": true,
    "TimeoutInterval":  240,
    "AnonymousUserName": "WS_GUEST",
    "AnonymousPassword": "123",
    "LogLocale": "en-US",
    "DebugMode": false,
    "DebugPath": "D:\DebugDC"
  }
}
```



## Серверная часть <a href="#server_settings" id="server_settings"></a>

### ServerUrl <a href="#server_url" id="server_url"></a>

IP-адрес (или доменное имя) и порт серверной части, к которой будет обращаться клиентская часть.

Обязательное поле. Ожидается URL-адрес.

Значение по умолчанию: отсутствует

```json
"ServerUrl": "http://localhost:50002"
```

### ConnectionTimeout <a href="#connection_timeout" id="connection_timeout"></a>

Задает значение Timeout для попытки установления соединения с сервером.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"00:05:00"`

```json
"ConnectionTimeout": "00:02:00"
```

## Настройки Web-службы <a href="#web-service-settings" id="web-service-settings"></a>

### HostUrl <a href="#host_url" id="host_url"></a>

IP-адрес (или доменное имя) и порт, на котором будет запущено  Web-приложения и доступно для работы в браузере.

Необязательное поле. Ожидается URL-адрес.

Значение по умолчанию: `"http://localhost:4990/"`

```json
"HostUrl": "http://192.168.7.88:4990"
```

### StartAssembly <a href="#start_assembly" id="start_assembly"></a>

Задает имя библиотеки, в которой описывается шаблон web-страницы. На основе этого шаблона будут генерироваться web-страницы.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"WorkflowWebForms.dll"`

```json
"StartAssembly": "Carrent"
```

### XmlFolder <a href="#xml_folder" id="xml_folder"></a>

Абсолютный путь до папки с xml-файлами форм и вложенной папкой Images, содержащей графические файлы. Из этих xml-файлов будут генерироваться web-страницы.

{% hint style="info" %}
Стартовая страница web-приложения задается атрибутом [`StartPage`](https://wfsys.gitbook.io/workflow-web-forms-syntax#start_page) в тэге `<Form>` xml-файла.

Если несколько форм имеют атрибут `StartPage` со значением True, то это приведет к некорректной маршрутизации.

Если ни одна из форм не имеет атрибут `StartPage` со значением True, то попытка обращения к стартовой странице вызовет ошибку 404 (страница не найдена).
{% endhint %}

Обязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: отсутствует

```json
"XmlFolder": "D:\\WorkflowTechnology\\Carrent\\Projects\\1. Carrent\\WebForms"
```

### TempFileFolder <a href="#temp_file_folder" id="temp_file_folder"></a>

Задает путь до папки, куда будут загружаться временные файлы web-службы.\
Временные файлы - любые файлы, которые служба получает для последующей передачи пользователю, или файлы прикрепляемые в команде [EmailSendCommand](https://wfsys.gitbook.io/workflow-web-forms-syntax/workflow_webforms/commands/email_send_command).

{% hint style="info" %}
Если web-служба и серверная часть (служба Engine) находятся на разных компьютерах, то все системные файлы и файлы загруженные пользователями, должны быть скачаны web-службой на свой компьютер в папку временных файлов. Затем web-служба сможет использовать эти файлы, чтобы предоставлять их клиенту.

Если web-служба и серверная часть (служба Engine) находятся на одном компьютере, то это отмечается в настройке [EngineOnSameMachine](appsettings-json.md#engine_on_same_machine), а в настройке [EngineStorages](appsettings-json.md#engine_storages) перечисляются пути до всех папок, в которых могут храниться файлы.
{% endhint %}

Необязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: `"TempFile"`

```json
"TempFileFolder": "D:\\Web\\TempFile"
```

{% hint style="info" %}
Если используется значение по умолчанию, то в корневой папке приложения WorkflowWebForms будет создана директория TempFile, куда будут загружаться временные файлы веб-службы.
{% endhint %}

### EngineOnSameMachine <a href="#engine_on_same_machine" id="engine_on_same_machine"></a>

Признак, отмечающий, что web-служба и серверная часть приложения будут запущены на разных компьютерах.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `false`

```json
"EngineOnSameMachine": true
```

### EngineStorages <a href="#engine_storages" id="engine_storages"></a>

Задает список абсолютных путей до папок, в которых хранятся системные файлы и файлы, загруженные клиентами.\
Используется, если настройка [EngineOnSameMachine](appsettings-json.md#engine_on_same_machine) имеет значение True.

{% hint style="info" %}
Если web-службе нужно найти определенный файл, то она последовательно пройдет по всем каталогам, указанным в настройке, пока не найдет файл.
{% endhint %}

Необязательное поле. Ожидается линейный массив путей до папок.

Значение по умолчанию: отсутствует.

```json
"EngineStorages": [ "D:\\WorkflowEngine\\Carrent.v3\\Upload" ]
```

### SystemLocale <a href="#system_locale" id="system_locale"></a>

Задает код культуры, который определяет язык системных сообщений, отображаемых в программе.

Необязательное поле. Ожидается одно из допустимых значение:&#x20;

<table data-header-hidden><thead><tr><th width="199"></th><th></th></tr></thead><tbody><tr><td>en-US</td><td>английский (США)</td></tr><tr><td>ru-RU</td><td>русский (Россия)</td></tr></tbody></table>

Значение по умолчанию: `"en-US"`

```json
"SystemLocale": "en-US"
```

### Translate <a href="#translate" id="translate"></a>

Признак, определяющий будет ли работать автоперевод страниц в браузере.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `true`

```json
"Translate": false
```

### HelpDeskEnabled <a href="#help_desk_enabled" id="help_desk_enabled"></a>

Признак, включающий возможность отправлять обращения в сервис HelpDesk.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `true`

```json
"HelpDeskEnabled": false
```

## Аутентификация <a href="#authentication" id="authentication"></a>

### LoginPageUrl <a href="#login_page_url" id="login_page_url"></a>

Задает адрес web-страницы, на которую будет перенаправляться пользователь если еще не прошел аутентификацию, или не проявлял активности в течение времени, указанного в настройке [TimeoutInterval](appsettings-json.md#timeout_interval).

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"login"`

```json
"LoginPageUrl": "login"
```

### TimeoutEnabled <a href="#timeout_enabled" id="timeout_enabled"></a>

Признак, включающий механизм повторной аутентификации если пользователь не проявлял активности в течение времени, указанного в настройке [TimeoutInterval](appsettings-json.md#timeout_interval).

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `false`

```json
"TimeoutEnabled": true
```

### TimeoutInterval <a href="#timeout_interval" id="timeout_interval"></a>

Задает интервал времени, если в течение него пользователь не проявлял активности, то будет отображаться окно входа в приложение для повторной аутентификации.

Необязательное поле. Ожидается положительное целочисленное значение.

Значение по умолчанию: `240`

```json
"TimeoutInterval":  240
```

## Анонимный пользователь <a href="#anonymous-user" id="anonymous-user"></a>

Анонимная учетка необходима для подписания запросов к серверной части, когда пользователь не авторизовался в приложении. Например, запрос на получение списка пользователей для окна входа в программу.

### AnonymousUserName <a href="#anonymous_user_name" id="anonymous_user_name"></a>

Логин анонимного пользователя.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"WS_GUEST"`

```json
"AnonymousUserName": "WS_GUEST"
```

### AnonymousPassword <a href="#anonymous_password" id="anonymous_password"></a>

Пароль анонимного пользователя.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"wsGuestPwd123"`

```json
"AnonymousPassword": "wsGuestPwd123"
```

## Настройки логирования <a href="#logging-settings" id="logging-settings"></a>

### LogLocale <a href="#log_locale" id="log_locale"></a>

Задает код культуры, который определяет язык сообщений об ошибке публикуемых в журнале событий Windows.

Необязательное поле. Ожидается одно из допустимых значение:&#x20;

<table data-header-hidden><thead><tr><th width="199"></th><th></th></tr></thead><tbody><tr><td>en-US</td><td>английский (США)</td></tr><tr><td>ru-RU</td><td>русский (Россия)</td></tr></tbody></table>

Значение по умолчанию: `"en-US"`

```json
"LogLocale": "en-US"
```

### Serilog <a href="#serilog" id="serilog"></a>

Настройка логирования. Подробнее на [github.com](https://github.com/serilog/serilog/wiki/Configuration-Basics).

```json
"Serilog": {
  "MinimumLevel": "Warning",
  "WriteTo": [
    {
      "Name": "Async",
      "Args": {
        "configure": [
          {
            "Name": "RollingFile",
            "Args": {
              "pathFormat": "Logs\\log-{Date}.txt",
              "fileSizeLimitBytes": null,
              "retainedFileCountLimit": null,
              "outputTemplate": "{Timestamp:o} [{Level:u3}] {SourceContext} ({RequestId}/{ThreadId}) {Message}{NewLine}{Exception}{NewLine}",
              "buffered": false
            }
          },
          {
            "Name": "LiterateConsole",
            "Args": {
              "outputTemplate": "{Timestamp:o} [{Level:u3}] {SourceContext} ({RequestId}/{ThreadId}) {Message}{NewLine}{Exception}{NewLine}"
            }
          },
          { "Name": "Debug" },
          {
            "Name": "EventLog",
            "Args": {
              "logName": "Workflow Technology",
              "source": "Workflow Web Forms",
              "outputTemplate": "{Timestamp:o}{NewLine}[{Level:u3}] {SourceContext} ({RequestId}/{ThreadId}){NewLine}{Message}{NewLine}{Exception}{NewLine}"
            }
          }
        ]
      }
    }
  ],
  "Enrich": [ "FromLogContext", "WithMachineName", "WithThreadId", "WithRequestId" ]
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

Значение по умолчанию: `false`

```json
"DebugMode": false
```

### DebugPath <a href="#debug_path" id="debug_path"></a>

Путь до папки, в которую будут сохраняться файлы логов клиентской части.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"D:\\DebugDC"`

```json
"DebugPath": "D:\\DebugDC"
```
