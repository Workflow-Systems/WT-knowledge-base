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
                "source": "Workflow UpdateService",
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
  "WorkflowEngineServiceName": "",
  "BackupCloudPassword": "",
  "BackupSpaceSaving": false,
  "DebugMode": false
}
```

### WorkflowEngineServiceName <a href="#workflow_engine_service_name" id="workflow_engine_service_name"></a>

Имя службы WorkflowEngine.\
С периодичностью в одну минуту служба обновления проверяет, запущена ли служба WorkflowEngine. Если служба WorkflowEngine не запущена, то служба обновления будет пытаться ее запустить.

Необязательное поле.

Значение по умолчанию: отсутствует

```json
"WorkflowEngineServiceName": "WorkflowEngineName"
```

### BackupCloudPassword <a href="#backup_cloud_password" id="backup_cloud_password"></a>

Задает пароль от архива для облачного резервного копирования.

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: отсутствует

```json
"BackupCloudPassword": "Password123"
```

### BackupSpaceSaving <a href="#backup_space_saving" id="backup_space_saving"></a>

Параметр, отключающий копирование Upload-файлов в папку облачного резервного копирования.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `false`

```json
"BackupSpaceSaving": true
```

### DebugMode <a href="#debug_mode" id="debug_mode"></a>

Признак, включающий режим отладки для облачного резервного копирования.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `false`

```json
"DebugMode": true
```
