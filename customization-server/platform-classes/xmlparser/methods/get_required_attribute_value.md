# GetRequiredAttributeValue

## **GetRequiredAttributeValue\<T>(XmlNode, String, String)** <a href="#get_required_attribute_value" id="get_required_attribute_value"></a>

Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено исключение.

```csharp
public static T GetRequiredAttributeValue<T>(XmlNode node,
                                             string path,
                                             string attribute)
```

### **Параметры** <a href="#parameters" id="parameters"></a>

`node` System.Xml.XmlNode\
Узел XmlNode, содержащий нужный элемент.

`path` System.String\
Путь до нужного элемента.

`attribute` System.String\
Атрибут, из которого нужно получить значение.

### **Возвращает** <a href="#returns" id="returns"></a>

T\
Значение атрибута нужного элемента.

### Исключения <a href="#exceptions" id="exceptions"></a>

[InvalidXmlException](/broken/pages/58gMUdOHK6kWSzV2vhR7)\
Если в описании узла `node` отсутствует элемент по пути `path` или его атрибут `attribute`.



## **GetRequiredAttributeValue\<T>(XmlNode, String, String, String)** <a href="#get_required_attribute_value-with-add-text" id="get_required_attribute_value-with-add-text"></a>

Возвращает значение из XmlNode, содержащееся в атрибуте элемента по указанному пути. Если элемент или атрибут отсутствуют, будет возвращено исключение, сообщение которого будет дополнено переданным текстом.

```csharp
public static T GetRequiredAttributeValue<T>(XmlNode node,
                                             string path,
                                             string attribute,
                                             string additionalMessage)
```

### **Параметры** <a href="#parameters" id="parameters"></a>

`node` System.Xml.XmlNode\
Узел XmlNode, содержащий нужный элемент.

`path` System.String\
Путь до нужного элемента.

`attribute` System.String\
Атрибут, из которого нужно получить значение.

`additionalMessage` System.String\
Дополнительное сообщение, которое будет добавлено в текст исключения.

### **Возвращает** <a href="#returns" id="returns"></a>

T\
Значение атрибута нужного элемента.

### Исключения <a href="#exceptions" id="exceptions"></a>

[InvalidXmlException](/broken/pages/58gMUdOHK6kWSzV2vhR7)\
Если в описании узла `node` отсутствует элемент по пути `path` или его атрибут `attribute`.

### Примечания <a href="#remarks" id="remarks"></a>

Вы можете использовать этот метод, чтобы дополнить текст сообщения об ошибке информацией, раскрывающей суть обязательного атрибута. Таким образом, сообщения об ошибках будут полными и заменять документацию.



## **Примеры** <a href="#examples" id="examples"></a>

#### Пример 1 <a href="#example-1" id="example-1"></a>

В следующем примере извлекается значение атрибута `Name` тэга `<SettingsSqlQuery>`:

```csharp
XmlParser.GetRequiredAttributeValue<string>(node, "SettingsSqlQuery", "Name");
```

Описание команды типа MyCommand в xml-файле:

```xml
<Command Name="MyCommand" Type="MyCommand" Assembly="TemplateEngine">
  <SettingsSqlQuery Name="SettingsSelectSqlQuery" />
</Command>
```

#### Пример 2 <a href="#example-2" id="example-2"></a>

В следующем примере извлекается значение атрибута `Name` тэга `<SettingsSqlQuery>`, вложенного в тэг `<SqlQueries>`:

```csharp
XmlParser.GetRequiredAttributeValue<string>(
    node, "SqlQueries/SettingsSqlQuery", "Name",
    "В элементе SettingsSqlQuery указывается имя запроса на получение настроек.");
```

В метод [GetRequiredAttributeValue](get_required_attribute_value.md#get_required_attribute_value-with-add-text) последним параметром передается дополнительное сообщение, которое будет добавлено в текст исключения.

Описание команды типа MyCommand в xml-файле:

```xml
<Command Name="MyCommand" Type="MyCommand" Assembly="TemplateEngine">
  <SqlQueries>
    <SettingsSqlQuery Name="SettingsSelectSqlQuery" />
    <SaveSqlQuery Name="UpdateSqlQuery" />
  </SqlQueries>
</Command>
```
