# GetRequiredElementDataBinding

## GetRequiredElementDataBinding(IDependable, IWorkflowForm, XmlNode, IDataBindingProvider, String, String, Object) <a href="#get-required-element-data-binding" id="get-required-element-data-binding"></a>

Возвращает элемент привязки данных из XmlNode, содержащийся в элементе по указанному пути. Если элемент отсутствует, будет возвращено исключение.

```csharp
public static IDataBinding GetRequiredElementDataBinding(
    IDependable parent,
    IWorkflowForm form,
    XmlNode node,
    IDataBindingProvider dataBindingProvider,
    string path,
    string name,
    object targetObject)
```

### **Параметры** <a href="#parameters" id="parameters"></a>

`parent` IDependable\
Родительский объект.

`form` IWorkflowForm\
Форма.

`node` System.Xml.XmlNode\
Узел XmlNode, содержащий нужный элемент.

`dataBindingProvider` IDataBindingProvider\
Привязка элемента.

`path` System.String\
Путь до нужного элемента.

`name` System.String\
Имя объекта, в котором происходит получение значения.

`targetObject` System.Object\
Класс объекта, в котором происходит получение значения.

### **Возвращает** <a href="#returns" id="returns"></a>

IDataBinding\
Возвращает ссылку.

### Исключения <a href="#exceptions" id="exceptions"></a>

InvalidXmlException\
Если в описании узла `node` отсутствует элемент по пути `path`.



## GetRequiredElementDataBinding(IDependable, IWorkflowForm, XmlNode, IDataBindingProvider, String, String, Object, String) <a href="#get-required-element-data-binding-with-add-text" id="get-required-element-data-binding-with-add-text"></a>

Возвращает элемент привязки данных из XmlNode, содержащийся в элементе по указанному пути. Если элемент отсутствует, будет возвращено исключение, сообщение которого будет дополнено переданным текстом.

```csharp
public static IDataBinding GetRequiredElementDataBinding(
    IDependable parent,
    IWorkflowForm form,
    XmlNode node,
    IDataBindingProvider dataBindingProvider,
    string path,
    string name,
    object targetObject,
    string additionalMessage)
```

### **Параметры** <a href="#parameters" id="parameters"></a>

`parent` IDependable\
Родительский объект.

`form` IWorkflowForm\
Форма.

`node` System.Xml.XmlNode\
Узел XmlNode, содержащий нужный элемент.

`dataBindingProvider` IDataBindingProvider\
Привязка элемента.

`path` System.String\
Путь до нужного элемента.

`name` System.String\
Имя объекта, в котором происходит получение значения.

`targetObject` System.Object\
Класс объекта, в котором происходит получение значения.

`additionalMessage` System.String\
Дополнительное сообщение, которое будет добавлено в текст исключения.

### **Возвращает** <a href="#returns" id="returns"></a>

IDataBinding\
Возвращает ссылку.

### Исключения <a href="#exceptions" id="exceptions"></a>

InvalidXmlException\
Если в описании узла `node` отсутствует элемент по пути `path`.

### Примечания <a href="#remarks" id="remarks"></a>

Вы можете использовать этот метод, чтобы дополнить текст сообщения об ошибке информацией, раскрывающей суть обязательного элемента. Таким образом, сообщения об ошибках будут полными и заменять документацию.

## **Примеры** <a href="#examples" id="examples"></a>

В следующем примере извлекается значение тэга `<Text>`, вложенного в тэг `<SettingsSqlQuery>`:

```csharp
```

Описание команды типа MyCommand в xml-файле:

```xml
```

