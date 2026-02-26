# GetElementValue

## **GetElementValue\<T>(XmlNode, String, String, Object, T)** <a href="#get_element_value" id="get_element_value"></a>

Возвращает значение из XmlNode, содержащееся в элементе по указанному пути. Если элемент отсутствует, будет возвращено значение по умолчанию.

```csharp
public static T GetElementValue<T>(XmlNode node,
                                   string path,
                                   string name,
                                   object targetObject,
                                   T defaultValue)
```

### **Параметры** <a href="#parameters" id="parameters"></a>

`node` System.Xml.XmlNode\
Узел XmlNode, содержащий нужный элемент.

`path` System.String\
Путь до нужного элемента.

`name` System.String\
Имя объекта, в котором происходит получение значения.

`targetObject` System.Object\
Класс объекта, в котором происходит получение значения.

`defaultValue` T\
Значение по умолчанию, которое возвращается, если элемент отсутствуют.

### **Возвращает** <a href="#returns" id="returns"></a>

T\
`defaultValue`, если элемент отсутствуют; в противном случае - значение нужного элемента.

### **Примеры** <a href="#examples" id="examples"></a>

В следующем примере извлекается значение тэга `<Text>`, вложенного в тэг `<SettingsSqlQuery>`:

```csharp
XmlParser.GetElementValue<string>(node, "SettingsSqlQuery/Text", Name, this, null);
```

Описание команды типа MyCommand в xml-файле:

```xml
<Command Name="MyCommand" Type="MyCommand" Assembly="TemplateEngine">
  <SettingsSqlQuery>
    <Text>
      SELECT smtp_server, smtp_port
      FROM template.settings
      LIMIT 1;
    </Text>
  </SettingsSqlQuery>
</Command>
```
