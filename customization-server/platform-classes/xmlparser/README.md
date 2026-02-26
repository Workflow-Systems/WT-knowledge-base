# XmlParser

## Определение <a href="#definition" id="definition"></a>

Пространство имен: Common\
Сборка: Common.dll

Предоставляет методы для получения значений атрибутов и тэгов описанных в xml-файле.

## Примеры <a href="#examples" id="examples"></a>

В следующем примере кода извлекается значение атрибута `Name` тэга `<SettingsSqlQuery>`, описанного в команде типа MyCommand:

```csharp
XmlParser.GetAttributeValue<string>(node, "SettingsSqlQuery", "Name", null);
```

Описание команды типа MyCommand в xml-файле:

```xml
<Command Name="MyCommand" Type="MyCommand" Assembly="TemplateEngine">
  <SettingsSqlQuery Name="SettingsSelectSqlQuery" />
</Command>
```

## Методы <a href="#methods" id="methods"></a>

<table data-header-hidden data-full-width="true"><thead><tr><th width="345"></th><th></th></tr></thead><tbody><tr><td><a href="methods/get_attribute_value.md#get_attribute_value">GetAttributeValue&#x3C;T><br>(XmlNode, String, String, T)</a></td><td>Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено значение по умолчанию.</td></tr><tr><td><a href="methods/get_required_attribute_value.md#get_required_attribute_value">GetRequiredAttributeValue&#x3C;T><br>(XmlNode, String, String)</a></td><td>Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено исключение.</td></tr><tr><td><a href="methods/get_required_attribute_value.md#get_required_attribute_value-with-add-text">GetRequiredAttributeValue&#x3C;T><br>(XmlNode, String, String, String)</a></td><td>Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено исключение, сообщение которого будет дополнено переданным текстом.</td></tr><tr><td><a href="methods/get_element_value.md#get_element_value">GetElementValue&#x3C;T><br>(XmlNode, String, String, Object, T)</a></td><td>Возвращает значение из XmlNode, содержащееся в элементе по указанному пути. Если элемент отсутствует, будет возвращено значение по умолчанию.</td></tr><tr><td><a href="methods/get_required_element_value.md#get_required_element_value">GetRequiredElementValue&#x3C;T><br>(XmlNode, String, String, Object)</a></td><td>Возвращает значение из XmlNode, содержащееся в элементе по указанному пути. Если элемент отсутствует, будет возвращено исключение.</td></tr><tr><td><a href="methods/get_required_element_value.md#get_required_element_value-with-add-text">GetRequiredElementValue&#x3C;T><br>(XmlNode, String, String, Object, String)</a></td><td>Возвращает значение из XmlNode, содержащееся в элементе по указанному пути. Если элемент отсутствует, будет возвращено исключение, сообщение которого будет дополнено переданным текстом.</td></tr></tbody></table>
