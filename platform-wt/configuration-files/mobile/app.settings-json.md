# app.settings.json

## Шаблон <a href="#template" id="template"></a>

```json
{
  "ServerUrl": "",
  "DefaultServerUrl": "http://192.168.7.74:50707",
  "StartFormFileName": "",
  "ManualInit": true,
  "AnonymousUserName": "WS_GUEST",
  "AnonymousPassword": "wsGuestPwd123",
  "IsDebugMode": true,
  "UseLocalConfig": true,
  "MobileAppName": ""
}
```

## Серверная часть <a href="#server_settings" id="server_settings"></a>

### ServerUrl <a href="#server_url" id="server_url"></a>

IP-адрес (или доменное имя) и порт серверной части, к которой будет обращаться клиентская часть.\
Значение этого поля сохраняется в параметрах приложения и после может быть перезаписано.

Необязательное поле. Ожидается URL-адрес.

Значение по умолчанию: `"http://localhost:5000"`

```json
"ServerUrl": "http://192.168.7.74:50707"
```

### DefaultServerUrl <a href="#default_server_url" id="default_server_url"></a>

IP-адрес (или доменное имя) и порт серверной части, которые будут отображаться на экране приветствия (при первом запуске приложения), если не задан адрес сервера в поле [ServerUrl](app.settings-json.md#server_url).

Необязательное поле. Ожидается URL-адрес.

Значение по умолчанию: отсутствует

```json
"DefaultServerUrl": "http://localhost:50707"
```

## Клиентская часть <a href="#client_settings" id="client_settings"></a>

### StartFormFileName <a href="#start_form_file_name" id="start_form_file_name"></a>

Путь до стартовой формы, задается относительно папки, указанной в поле [MobileFormsFolder](https://wfsys.gitbook.io/wt-knowledge-base/platform-wt/configuration-files/server/appsettings.json#mobile_forms_folder) серверного файла конфигурации ([appsettings.json](https://wfsys.gitbook.io/wt-knowledge-base/platform-wt/configuration-files/server/appsettings.json)).

Необязательное поле. Любое значение будет переведено в текстовое.

Значение по умолчанию: `"Start.xml"`

```json
"StartFormFileName": "1. Template/MobileForms/Inspection/TemplateStart.xml"
```

### UseLocalConfig <a href="#use_local_config" id="use_local_config"></a>

Признак, определяющий с каким файлом конфигурации будет работать приложение: с типовым, определенном в исходном проекте, или локальным, определенным в индивидуальном проекте для отдельного решения.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `true`

```json
"UseLocalConfig": true
```

### MobileAppName <a href="#mobile_app_name" id="mobile_app_name"></a>

Имя мобильного приложения. Используется при идентификации приложения на сервере в проектах с несколькими мобильными приложениями.\
По этому имени сервер находит папки с нужными бинарниками и xml-файлами форм, которые передает на мобильное устройство.

{% hint style="info" %}
Для каждого мобильного приложения в таблице public.mobile\_app в базе данных на сервере должна быть запись с таким же значение в поле name.
{% endhint %}

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: отсутствует

```json
"MobileAppName": "Inspection"
```

### ManualInit <a href="#manual_init" id="manual_init"></a>

Признак, определяющий, будет ли показываться экран приветствия с полем для указания адреса сервера каждый раз при запуске приложения или только при первом запуске после установки, если в поле [ServerUrl](app.settings-json.md#server_url) не указан адрес.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `false`

```json
"ManualInit": true
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

## Отладка <a href="#debug" id="debug"></a>

### IsDebugMode <a href="#is_debug_mode" id="is_debug_mode"></a>

Признак, включающий отображение диалогового окна с описанием ошибки при возникновении исключения в работе приложения.

Необязательное поле. Ожидается логическое значение.

Значение по умолчанию: `false`

```json
"IsDebugMode": true
```
