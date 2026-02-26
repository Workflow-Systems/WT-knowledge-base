# GetAttributeValue

## **GetAttributeValue\<T>(XmlNode, String, String, T)** <a href="#get_attribute_value" id="get_attribute_value"></a>

Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено значение по умолчанию.

{% code fullWidth="false" %}
```csharp
public static T GetAttributeValue<T>(XmlNode node,
                                     string path,
                                     string attribute,
                                     T defaultValue)
```
{% endcode %}

### **Параметры** <a href="#parameters" id="parameters"></a>

`node` System.Xml.XmlNode\
Узел XmlNode, содержащий нужный элемент.

`path` System.String\
Путь до нужного элемента.

`attribute` System.String\
Атрибут, из которого нужно получить значение.

`defaultValue` T\
Значение по умолчанию, которое возвращается, если элемент или атрибут отсутствуют.

### **Возвращает** <a href="#returns" id="returns"></a>

T\
`defaultValue`, если элемент или атрибут отсутствуют; в противном случае - значение атрибута нужного элемента.

## **Примеры** <a href="#examples" id="examples"></a>

#### Пример 1 <a href="#example-1" id="example-1"></a>

В следующем примере извлекается значение атрибута `Value` тэга `<ContinueIfError>`:

```csharp
XmlParser.GetAttributeValue(node, "ContinueIfError", "Value", false);
```

Описание команды типа MyCommand в xml-файле:

```xml
<Command Name="MyCommand" Type="MyCommand" Assembly="TemplateEngine">
  <ContinueIfError Value="True" />
</Command>
```

#### Пример 2 <a href="#example-2" id="example-2"></a>

В следующем примере извлекается значение атрибута `Name` тэга `<SaveSqlQuery>`, вложенного в тэг `<SqlQueries>`:

<pre class="language-csharp"><code class="lang-csharp"><strong>XmlParser.GetAttributeValue&#x3C;string>(node, "SqlQueries/SaveSqlQuery", "Name", null);
</strong></code></pre>

Описание команды типа MyCommand в xml-файле:

```xml
<Command Name="MyCommand" Type="MyCommand" Assembly="TemplateEngine">
  <SqlQueries>
    <SettingsSqlQuery Name="SettingsSelectSqlQuery" />
    <SaveSqlQuery Name="UpdateSqlQuery" />
  </SqlQueries>
</Command>
```
