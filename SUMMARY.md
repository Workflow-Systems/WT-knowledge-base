# Table of contents

* [База знаний](README.md)

## Guidelines

* [Код](guidelines/code.md)
* [Интерфейс](guidelines/interfeis.md)
* [Шпаргалки и дорожные карты](guidelines/shpargalki-i-dorozhnye-karty.md)

## Workflow XML Editor

* [Сочетания клавиш](workflow-xml-editor/keyboard-shortcuts.md)
* [Patterns](workflow-xml-editor/patterns.md)

## Платформа WT <a href="#platform-wt" id="platform-wt"></a>

* [Файлы конфигурации](platform-wt/configuration-files/README.md)
  * [Сервер](platform-wt/configuration-files/server/README.md)
    * [appsettings.json](platform-wt/configuration-files/server/appsettings-json.md)
    * [hosting.json](platform-wt/configuration-files/server/hosting-json.md)
  * [Служба обновления](platform-wt/configuration-files/update-service/README.md)
    * [appsettings.json](platform-wt/configuration-files/update-service/appsettings-json.md)
  * [Клиент Desktop](platform-wt/configuration-files/desktop/README.md)
    * [WorkflowForms.dll.config](platform-wt/configuration-files/desktop/workflowforms.dll.config.md)
  * [Клиент Mobile](platform-wt/configuration-files/mobile/README.md)
    * [app.settings.json](platform-wt/configuration-files/mobile/app.settings-json.md)
  * [Клиент Web](platform-wt/configuration-files/web/README.md)
    * [appsettings.json](platform-wt/configuration-files/web/appsettings-json.md)
* [Аутентификация](platform-wt/authentication.md)
* [Права доступа](platform-wt/access-rights.md)
* [Системные переменные и параметры конфигурации](platform-wt/system-var-and-config-params.md)
* [Временные зоны](platform-wt/time_zone.md)
* [Сохранение вложенных сущностей](platform-wt/saving-nested-entities.md)
* [Диаграммы](platform-wt/charts.md)
* [Загрузка данных](platform-wt/data-loading.md)

## SQL

* [Пользовательские функции](sql/custom-functions/README.md)
  * [Функции на языке запросов (SQL)](sql/custom-functions/sql-functions.md)
  * [Функции на процедурном языке PL/pgSQL](sql/custom-functions/pl-functions.md)
* [Функции nextval и currval](sql/function_nextval_currval.md)
* [Индексы](sql/indexes.md)
* [Настройка postgresql.conf](sql/nastroika-postgresql.conf.md)

## Кастомизация Клиента <a href="#customization-client" id="customization-client"></a>

* [Создание и настройка проекта](customization-client/create-project.md)
* [Классы платформы (С#)](customization-client/platform-classes/README.md)
  * [TimeZoneHelper](customization-client/platform-classes/time_zone_helper.md)
  * [DataBinding](customization-client/platform-classes/data_binding.md)
  * [XmlParser](customization-client/platform-classes/xmlparser/README.md)
    * [Методы](customization-client/platform-classes/xmlparser/methods/README.md)
      * [GetAttributeValue](customization-client/platform-classes/xmlparser/methods/get-attribute-value.md)
      * [GetRequiredAttributeValue](customization-client/platform-classes/xmlparser/methods/get-required-attribute-value.md)
      * [GetElementDataBinding](customization-client/platform-classes/xmlparser/methods/get-element-data-binding.md)
      * [GetRequiredElementDataBinding](customization-client/platform-classes/xmlparser/methods/get-required-element-data-binding.md)
* [Языки в кастомках](customization-client/languages-and-custom.md)
* [Шаблоны кастомок](customization-client/custom-templates/README.md)
  * [MyObject](customization-client/custom-templates/custom-object.md)
  * [DataConnection](customization-client/custom-templates/custom-data_connection.md)
  * [Condition](customization-client/custom-templates/custom_condition.md)
  * [Command](customization-client/custom-templates/custom_command.md)

## Кастомизация Сервера <a href="#customization-server" id="customization-server"></a>

* [Создание и настройка проекта](customization-server/create-project.md)
* [Классы платформы (С#)](customization-server/platform-classes/README.md)
  * [XmlParser](customization-server/platform-classes/xmlparser/README.md)
    * [Методы](customization-server/platform-classes/xmlparser/methods/README.md)
      * [GetAttributeValue](customization-server/platform-classes/xmlparser/methods/get_attribute_value.md)
      * [GetRequiredAttributeValue](customization-server/platform-classes/xmlparser/methods/get_required_attribute_value.md)
      * [GetElementValue](customization-server/platform-classes/xmlparser/methods/get_element_value.md)
      * [GetRequiredElementValue](customization-server/platform-classes/xmlparser/methods/get_required_element_value.md)
* [Шаблоны кастомок](customization-server/custom-templates/README.md)
  * [Command](customization-server/custom-templates/custom_command.md)
  * [SqlQuery](customization-server/custom-templates/custom-sql_query.md)
