# XmlParser

## Определение <a href="#definition" id="definition"></a>

Пространство имен: WorkflowForms\
Сборка: FormObjects.dll

Предоставляет методы для получения значений атрибутов и тэгов описанных в xml-файле.

## Примеры <a href="#examples" id="examples"></a>

В следующем примере кода извлекается значение атрибута `Name` тэга `<AnyCondition>`, описанного в команде типа MyCommand:

```csharp
XmlParser.GetAttributeValue<string>(node, "AnyCondition", "Name", null);
```

Описание команды типа MyCommand в xml-файле:

```xml
<Command Name="MyCommand" Type="MyCommand" Assembly="Template">
  <AnyCondition Name="MyCondition" />
</Command>
```

## Методы <a href="#methods" id="methods"></a>

<table data-header-hidden data-full-width="true"><thead><tr><th width="411"></th><th></th></tr></thead><tbody><tr><td><a href="methods/get-attribute-value.md">GetAttributeValue&#x3C;T><br>(IWorkflowForm, XmlNode, String, String, T)</a></td><td>Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено значение по умолчанию.</td></tr><tr><td><a href="methods/get-required-attribute-value.md#get_required_attribute_value">GetRequiredAttributeValue&#x3C;T><br>(IWorkflowForm, XmlNode, String, String)</a></td><td>Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено исключение.</td></tr><tr><td><a href="methods/get-required-attribute-value.md#get_required_attribute_value-with-add-text">GetRequiredAttributeValue&#x3C;T><br>(IWorkflowForm, XmlNode, String, String, String)</a></td><td>Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено исключение, сообщение которого будет дополнено переданным текстом.</td></tr><tr><td><p><a href="methods/get-element-data-binding.md#get-element-data-binding">GetElementDataBinding</a></p><p><a href="methods/get-element-data-binding.md#get-element-data-binding">(IDependable, IWorkflowForm, XmlNode, IDataBindingProvider, String, String, Object)</a></p></td><td>Возвращает элемент привязки данных из XmlNode, содержащийся в элементе по указанному пути. Если элемент отсутствует, будет возвращено значение null.</td></tr><tr><td><a href="methods/get-required-element-data-binding.md#get-required-element-data-binding">GetRequiredElementDataBinding<br>(IDependable, IWorkflowForm, XmlNode, IDataBindingProvider, String, String, Object)</a></td><td>Возвращает элемент привязки данных из XmlNode, содержащийся в элементе по указанному пути. Если элемент отсутствует, будет возвращено исключение.</td></tr><tr><td><a href="methods/get-required-element-data-binding.md#get-required-element-data-binding-with-add-text">GetRequiredElementDataBinding<br>(IDependable, IWorkflowForm, XmlNode, IDataBindingProvider, String, String, Object, String)</a></td><td>Возвращает элемент привязки данных из XmlNode, содержащийся в элементе по указанному пути. Если элемент отсутствует, будет возвращено исключение, сообщение которого будет дополнено переданным текстом.</td></tr></tbody></table>
