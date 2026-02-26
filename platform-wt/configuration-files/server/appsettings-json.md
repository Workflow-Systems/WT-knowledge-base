# appsettings.json

## Шаблон <a href="#template" id="template"></a>

```json
{
  "Version": "1.0",
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
                "pathFormat": "%CONTENT_ROOT%\\Logs\\log-{Date}.txt",
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
                "source": "Workflow Engine",
                "outputTemplate": "{Timestamp:o}{NewLine}[{Level:u3}] {SourceContext} ({RequestId}/{ThreadId}){NewLine}{Message}{NewLine}{Exception}{NewLine}"
              }
            }
          ]
        }
      }
    ],
    "Enrich": [ "FromLogContext", "WithMachineName", "WithThreadId", "WithRequestId" ]
  },
  "Database": {
    "ServerAddress": "localhost",
    "ServerPort": "5432",
    "User": "postgres",
    "Password": "postgres",
    "Database": "template_project"
  },
  "Storages": {
    "Default": {
      "Path": "Upload",
      "Format": "yyyy\\\\MM\\\\dd"
    }
  },
  "FormsFolder": "C:\\WorkflowSystems\\Projects",
  "BinaryPath": "C:\\WorkflowSystems\\Distr\\WorkflowForms",
  "TempUpdateDir": "C:\\WorkflowSystems\\TempUpdateTest",
  "WebFormsFolder": "",
  "WebBinaryPath": "",
  "WorkflowWebServiceName": "",
  "MobileFormsFolder": "C:\\WorkflowSystems\\Projects",
  "MobileBinaryPath": "D:\\WorkflowEngine\\_Template.v3 (mobile)\\MobileBin",
  "WorkflowServer": {
    "ServerUrl": "http://ws00:7070",
    "WorkflowEngineUpdateService": {
      "Name": "WorkflowEngineUpdateService",
      "Port": 9090
    },
    "HelpDesk": {
      "Enabled": false
    },
    "Update": {
      "Enabled": false
    }
  },
  "SchedulerLog":  "False",
  "SystemLocale": "ru-RU",
  "LogLocale":  "ru-RU",
  "BackupCloudPath": "",
  "BackupCloudInterval": {
    "Start": "01:00",
    "Finish": "05:00"
  },
  "DebugMode": "False",
  "DebugPath": "D:\DebugDC",
  "PerformanceChecking": {
    "Enabled": "False",
    "Delayed": "True",
    "RamThreshold": 2048
  },
  "TimeZone": {
    "TimeZone": "Asia/Yekaterinburg"
  },
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    },
    "Certificates": {
      "Default": {
        "Path": "D:\\WorkflowEngine\\Tachki.v3\\certificate-https.pfx",
        "Password": "niBCev&n2FNA#clw"
      }
    }
  }
}
```

## Настройки сервера <a href="#server-settings" id="server-settings"></a>

### TimeZone

Задает временную зону сервера, в которой будут храниться в базе данных все даты со временем.

Необязательное поле. Ожидается название временной зоны [в формате IANA](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List).

Значение по умолчанию: `"Etc/GMT"`

```json
"TimeZone": {
  "TimeZone": "Asia/Yekaterinburg"
}
```

### SystemLocale <a href="#system_locale" id="system_locale"></a>

Задает код культуры, который определяет язык системных сообщений, отображаемых в программе.

Необязательное поле. Ожидается одно из допустимых значение:&#x20;

<table data-header-hidden><thead><tr><th width="199"></th><th></th></tr></thead><tbody><tr><td>en-US</td><td>английский (США)</td></tr><tr><td>ru-RU</td><td>русский (Россия)</td></tr></tbody></table>

Значение по умолчанию: `"en-US"`

```json
"SystemLocale": "ru-RU"
```

## Настройки подключения к базе данных <a href="#database-settings" id="database-settings"></a>

Обязательное поле `Database` зачастую содержит основные поля для доступа к базе данных:

* [Database](appsettings-json.md#db_database_name) - имя базы данных;
* [ServerPort](appsettings-json.md#db_server_port) - порт сервера базы данных;
* [Password](appsettings-json.md#db_password) - пароль для доступа к серверу базы данных.

Поле [ServerAddress](appsettings-json.md#db_server_address) не указывается, так как служба Engine и сервер СУБД чаще всего находятся на одном компьютере. Для остальных полей достаточно значений по умолчанию.&#x20;

```json
"Database": {
  "ServerAddress": "localhost",
  "ServerPort": "5432",
  "User": "postgres",
  "Password": "postgres",
  "Database": "template_project"
}
```

### ServerAddress <a href="#db_server_address" id="db_server_address"></a>

IP-адрес или доменное имя компьютера, на котором развернута база данных.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"localhost"`

```json
"ServerAddress": "localhost"
```

### ServerPort <a href="#db_server_port" id="db_server_port"></a>

Порт компьютера, который слушает сервер базы данных.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"5432"`

```json
"ServerPort": "5432"
```

### User <a href="#db_user" id="db_user"></a>

Имя пользователя, имеющего доступ к серверу СУБД, под которым служба Engine будет обращаться к базе данных.

{% hint style="info" %}
При установке PostgreSQL суперпользователь с именем **postgres** создается по умолчанию.
{% endhint %}

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"postgres"`

```json
"User": "postgres"
```

### Password <a href="#db_password" id="db_password"></a>

Пароль пользователя, имеющего доступ к серверу СУБД, под которым служба Engine будет обращаться к базе данных.

{% hint style="info" %}
При установке PostgreSQL пароль для суперпользователь **postgres** задается вручную и может отличаться.
{% endhint %}

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"postgres"`

```json
"Password": "postgres"
```

### Database <a href="#db_database_name" id="db_database_name"></a>

Имя базы данных, с которой будет взаимодействовать служба Engine.

Обязательное поле. Любое значение будет переведено в текстовое.

```json
"Database": "template_project"
```

## Пути до папок клиентских частей <a href="#paths-to-client-parts-folders" id="paths-to-client-parts-folders"></a>

### FormsFolder <a href="#forms_folder" id="forms_folder"></a>

Путь до папки, в которой лежат xml-файлы desktop-версии WT-приложения.\
Если в файле конфигурации desktop-клиента настройка [UseSourceCache](https://wfsys.gitbook.io/wt-knowledge-base/platform-wt/configuration-files/desktop/workflowforms.dll.config#use_source_cache) имеет значение **True**, то сервер будет отдавать на клиентскую машину xml-файлы из этой папки.

Необязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: отсутствует

```json
"FormsFolder": "C:\\WorkflowSystems\\Projects"
```

### BinaryPath <a href="#binary_path" id="binary_path"></a>

Абсолютный путь до папки, в которой лежат бинарные файлы desktop-версии WT-приложения.\
Если в файле конфигурации desktop-клиента настройка [CheckBinaryFiles](https://wfsys.gitbook.io/wt-knowledge-base/platform-wt/configuration-files/desktop/workflowforms.dll.config#check_binary_files) имеет значение **True**, то сервер будет отдавать на клиентскую машину бинарные файлы из этой папки.

Необязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: отсутствует

```json
"BinaryPath": "C:\\WorkflowSystems\\Distr\\WorkflowForms"
```

### TempUpdateDir <a href="#temp_update_dir" id="temp_update_dir"></a>

Абсолютный путь до папки временных файлов обновления. Так же в папке хранятся файлы, которые позволяют откатиться к ним, т.е. предоставляют возможность восстановить старую версию программы и базы данных.

Необязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: отсутствует

```json
"TempUpdateDir": "C:\\WorkflowSystems\\TempUpdateTest"
```

### WebFormsFolder <a href="#web_forms_folder" id="web_forms_folder"></a>

Путь до папки, в которой лежат xml-файлы web-версии WT-приложения.

Необязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: отсутствует

```json
"WebFormsFolder": ""
```

### WebBinaryPath <a href="#web_binary_path" id="web_binary_path"></a>

Абсолютный путь до папки, в которой лежат бинарные файлы web-версии WT-приложения.

Необязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: отсутствует

```json
"WebBinaryPath": ""
```

### MobileFormsFolder <a href="#mobile_forms_folder" id="mobile_forms_folder"></a>

Путь до папки, в которой лежат xml-файлы mobile-версии WT-приложения.

Необязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: отсутствует

```json
"MobileFormsFolder": "C:\\WorkflowSystems\\Projects"
```

### MobileBinaryPath <a href="#mobile_binary_path" id="mobile_binary_path"></a>

Абсолютный путь до папки, в которой лежат бинарные файлы mobile-версии WT-приложения.

Необязательное поле. Ожидается абсолютный путь до папки.

Значение по умолчанию: отсутствует

```json
"MobileBinaryPath": "D:\\WorkflowEngine\\_Template.v3\\MobileBin"
```

## Настройки файловых хранилищ <a href="#file-storage-settings" id="file-storage-settings"></a>

### Storages <a href="#storages" id="storages"></a>

Поле содержит массив объектов, описывающих настройки файловых хранилищ.

Имя вложенного объекта (элемента массива) определяет ключ, по которому можно обратиться к хранилищу в тэге [Storage](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/commands/upload_file_command#storage) команды [UploadFileCommand](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/commands/upload_file_command).

```json
"Storages": {
  "Default": {
    "Path": "Upload"
  },
  "Audio": {
    "Path": "D:\\WorkflowSystems\\Audio",
    "Format": "yyyy\\\\MM\\\\dd"
  }
}
```

{% hint style="info" %}
Хранилище _Default_ существует в платформе по умолчанию. Указывая его в поле `Storages`, можно переопределить путь до его корневой папки и/или структуру вложенных папок.
{% endhint %}

### Path <a href="#storage_path" id="storage_path"></a>

Путь до корневой папки хранилища.

Обязательное поле. Ожидается абсолютный путь или относительный путь, относительно корневой папки службы Engine.

```json
"Path": "D:\\WorkflowSystems\\Audio"
```

### Format <a href="#storage_format" id="storage_format"></a>

Задает  структуру вложенных папок.

Необязательное поле. Если не указано, то файлы будут сохраняться в корневую папку хранилища.

```json
"Format": "yyyy\\\\MM\\\\dd"
```

## Службы WorkflowServer

Поле WorkflowServer содержит настройки для [системы лицензирования](https://wfsys.gitbook.io/workflow-technology/platform-description/platform-architecture#workflow-licenser), [службы обновления](https://wfsys.gitbook.io/workflow-technology/platform-description/platform-architecture#workflow-updater) и [HelpDesk](https://wfsys.gitbook.io/workflow-technology/platform-description/platform-architecture#workflow-helpdesk).

```json
"WorkflowServer": {
  "ServerUrl": "http://lic.wfsys.ru:7070",
  "WorkflowEngineUpdateService": {
    "Name": "WorkflowEngineUpdateService",
    "Port": 9090
  },
  "HelpDesk": {
    "Enabled": true
  },
  "Update": {
    "Enabled": true
  }
}
```

### ServerUrl <a href="#server_url" id="server_url"></a>

Задает IP-адрес и порт сервера [службы лицензирования](https://wfsys.gitbook.io/workflow-technology/platform-description/platform-architecture#workflow-licenser).

Обязательное поле.

```json
"ServerUrl": "http://lic.wfsys.ru:7070"
```

### WorkflowEngineUpdateService <a href="#workflow_engine_update_service" id="workflow_engine_update_service"></a>

Настройки службы обновления серверной части WT-программы: имя службы и порт, на котором будет запускаться служба.\
Служба всегда запускается на _localhost_ для повышения вероятности запуска службы.

```json
"WorkflowEngineUpdateService": {
  "Name": "WorkflowEngineUpdateService",
  "Port": 9090
}
```

### HelpDesk <a href="#helpdesk" id="helpdesk"></a>

Признак, включающий возможность отправлять обращения в сервис [HelpDesk](https://wfsys.gitbook.io/workflow-technology/platform-description/platform-architecture#workflow-helpdesk).

Необязательное поле. Для вложенного поля Enabled ожидается логическое значение.

Значение по умолчанию: `false`

```json
"HelpDesk": {
  "Enabled": true
}
```

### Update

Признак, включающий возможность взаимодействия со [службой обновления](https://wfsys.gitbook.io/workflow-technology/platform-description/platform-architecture#workflow-updater).

Необязательное поле. Для вложенного поля Enabled ожидается логическое значение.

Значение по умолчанию: `false`

```json
"Update": {
  "Enabled": true
}
```

## Резервное копирование <a href="#backup" id="backup"></a>

### BackupCloudPath <a href="#backup_cloud_path" id="backup_cloud_path"></a>

Задает адрес облачного хранилища, в которое будет происходить резервное копирование.

Необязательное поле.

Значение по умолчанию: отсутствует

```json
"BackupCloudPath": ""
```

### BackupCloudInterval <a href="#backup_cloud_interval" id="backup_cloud_interval"></a>

Задает интервал времени, каждый день в течение которого будет производиться резервное копирование. Время начала и окончания периода резервирования задается с точностью до минуты.

Необязательное поле.

Значение по умолчанию: `"01:00"` и `"05:00"`

```json
"BackupCloudInterval": {
  "Start": "01:00",
  "Finish": "05:00"
}
```

## Настройки логирования <a href="#logging-settings" id="logging-settings"></a>

### LogLocale <a href="#log_locale" id="log_locale"></a>

Задает код культуры, который определяет язык сообщений об ошибке публикуемых в журнале событий Windows.

Необязательное поле. Ожидается одно из допустимых значение:&#x20;

<table data-header-hidden><thead><tr><th width="199"></th><th></th></tr></thead><tbody><tr><td>en-US</td><td>английский (США)</td></tr><tr><td>ru-RU</td><td>русский (Россия)</td></tr></tbody></table>

Значение по умолчанию: `"en-US"`

```json
"LogLocale": "ru-RU"
```

### SchedulerLog <a href="#scheduler_log" id="scheduler_log"></a>

Признак, включающий логирование работы планировщика задач, встроенного в платформу. Пишутся метки начала и окончания выполнения задач.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `false`

```json
"SchedulerLog": false
```

### Serilog

Настройка логирования. Подробнее на [github.com](https://github.com/serilog/serilog/wiki/Configuration-Basics).

%CONTENT\_ROOT% - системная переменная указывающая на папку с сервером.

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
              "pathFormat": "%CONTENT_ROOT%\\Logs\\log-{Date}.txt",
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
              "source": "Workflow Engine",
              "outputTemplate": "{Timestamp:o}{NewLine}[{Level:u3}] {SourceContext} ({RequestId}/{ThreadId}){NewLine}{Message}{NewLine}{Exception}{NewLine}"
            }
          }
        ]
      }
    }
  ],
  "Enrich": [ "FromLogContext", "WithMachineName", "WithThreadId", "WithRequestId" ]
}
```

## Отладка <a href="#debug" id="debug"></a>

В режиме отладки сервер пишет в файлы информацию:

* время начала и время окончания выполнения запроса к базе данных;
* время начала и время окончания обработки запроса от клиентской части.

Для работы с логами отладки используется дополнительная утилита - _WorkflowVisualizer._

### DebugMode <a href="#debug_mode" id="debug_mode"></a>

Признак, включающий на сервере режим отладки.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `False`

```json
"DebugMode": "True"
```

### DebugPath <a href="#debug_path" id="debug_path"></a>

Путь до папки, в которую будут сохраняться файлы логов клиентской части.

Необязательное поле. Ожидается абсолютный путь.

Значение по умолчанию: `"D:\\DebugDC"`

```json
"DebugPath": "D:\\DebugDC"
```

### PerformanceChecking <a href="#performance_checking" id="performance_checking"></a>

Задает настройки проверки производительности: служба Engine каждый час выполняют проверку, насколько загружена оперативная память.

Необязательное поле.&#x20;

```json
"PerformanceChecking": {
  "Enabled": "False",
  "Delayed": "True",
  "RamThreshold": 2048
}
```

#### Enabled <a href="#performance_checking-enabled" id="performance_checking-enabled"></a>

Признак, включающий проверку производительности.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `false`

#### Delayed <a href="#performance_checking-delayed" id="performance_checking-delayed"></a>

Признак, включающий задержку в 5 минут при старте проверки производительности.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `true`

#### RamThreshold <a href="#performance_checking-ram_threshold" id="performance_checking-ram_threshold"></a>

Задает ограничение по объему используемой оперативной памяти (в МБ).

Необязательное поле. Ожидается целочисленное положительное значение.

Значение по умолчанию: `2048`



Условия выполнения проверки:

1. Проверка запускается через 5 минут после старта сервера;
2. Если зафиксировано превышение, то далее проверки выполняются каждую минуту. Если зафиксировано 15 превышений подряд, то запускается логирование;
3. Если превышения нет, то следующая проверка выполнится через час.

Логи пишутся в папку _C:\ProgramData\Workflow Systems_ в файлы:

* PerformanceHighUsage.txt - значение проверки;
* PerformanceHighUsage\_YYYY-MM-DD-HH24.txt - записывается время начала и окончания всех операций и действий на форме.

## Настройки HTTPS <a href="#https-settings" id="https-settings"></a>

Web-сервер Kestrel поддерживает защиту конечных точек с помощью HTTPS. Для повышения безопасности передаваемых между клиентом и сервером данных, протокол HTTPS предусматривает их шифрование с помощью [протокола TLS](https://tools.ietf.org/html/rfc5246).

Для HTTPS требуется сертификат TLS, который должен хранится на сервере и быть явно настроен.

Подробнее про настройку HTTPS можно прочитать в [статье](https://learn.microsoft.com/ru-ru/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-9.0#configure-https).

```json
"Kestrel": {
  "EndpointDefaults": {
    "Protocols": "Http1"
  },
  "Certificates": {
    "Default": {
      "Path": "D:\\WorkflowEngine\\Template.v3\\certificate-https.pfx",
      "Password": "niBCev&n2FNA#clw"
    }
  }
}
```

### Protocols <a href="#https_protocols" id="https_protocols"></a>

Задает версию протокола HTTP.

Доступные значения описаны в разделе [Настройка протоколов HTTP](https://learn.microsoft.com/ru-ru/aspnet/core/fundamentals/servers/kestrel/endpoints?view=aspnetcore-9.0#configure-http-protocols) официальной статьи от Microsoft.

### Path <a href="#https_path" id="https_path"></a>

Задается абсолютный путь до файла сертификата TLS.

### Password <a href="#https_password" id="https_password"></a>

Задается пароль для доступа к данным сертификата TLS.
