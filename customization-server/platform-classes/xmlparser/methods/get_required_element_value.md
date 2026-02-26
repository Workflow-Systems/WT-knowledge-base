# GetRequiredElementValue

## **GetRequiredElementValue\<T>(XmlNode, String, String, Object)** <a href="#get_required_element_value" id="get_required_element_value"></a>

Возвращает значение из XmlNode, содержащееся в элементе по указанному пути. Если элемент отсутствует, будет возвращено исключение.

```csharp
public static T GetRequiredElementValue<T>(XmlNode node,
                                           string path,
                                           string name,
                                           object targetObject)
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

### **Возвращает** <a href="#returns" id="returns"></a>

T\
`defaultValue`, если элемент отсутствуют; в противном случае - значение нужного элемента.

### Исключения <a href="#exceptions" id="exceptions"></a>

[InvalidXmlException](/broken/pages/58gMUdOHK6kWSzV2vhR7)\
Если в описании узла `node` отсутствует элемент по пути `path` или его атрибут `attribute`.



## **GetRequiredElementValue\<T>(XmlNode, String, String, Object, String)** <a href="#get_required_element_value-with-add-text" id="get_required_element_value-with-add-text"></a>

Возвращает значение из XmlNode, содержащееся в элементе по указанному пути. Если элемент отсутствует, будет возвращено исключение, сообщение которого будет дополнено переданным текстом.

```csharp
public static T GetRequiredElementValue<T>(XmlNode node,
                                           string path,
                                           string name,
                                           object targetObject,
                                           string additionalMessage)
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

`additionalMessage` System.String\
Дополнительное сообщение, которое будет добавлено в текст исключения.

### **Возвращает** <a href="#returns" id="returns"></a>

T\
`defaultValue`, если элемент отсутствуют; в противном случае - значение нужного элемента.

### Исключения <a href="#exceptions" id="exceptions"></a>

[InvalidXmlException](/broken/pages/58gMUdOHK6kWSzV2vhR7)\
Если в описании узла `node` отсутствует элемент по пути `path` или его атрибут `attribute`.

### Примечания <a href="#remarks" id="remarks"></a>

Вы можете использовать этот метод, чтобы дополнить текст сообщения об ошибке информацией, раскрывающей суть обязательного элемента. Таким образом, сообщения об ошибках будут полными и заменять документацию.



### **Примеры** <a href="#examples" id="examples"></a>

В следующем примере извлекается значение тэга `<Text>`, вложенного в тэг `<SettingsSqlQuery>`:

```csharp
XmlParser.GetRequiredElementValue<string>(
    node, "SettingsSqlQuery/Text", Name, this,
    "В элементе SettingsSqlQuery указывается текст запроса на получение настроек.");
```

В метод [GetRequiredElementValue](get_required_element_value.md#getrequiredelementvalue-less-than-t-greater-than-xmlnode-string-string-object-string) последним параметром передается дополнительное сообщение, которое будет добавлено в текст исключения.

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
