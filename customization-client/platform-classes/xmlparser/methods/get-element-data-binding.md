# GetElementDataBinding

## GetElementDataBinding(IDependable, IWorkflowForm, XmlNode, IDataBindingProvider, String, String, Object) <a href="#get-element-data-binding" id="get-element-data-binding"></a>

Возвращает элемент привязки данных из XmlNode, содержащийся в элементе по указанному пути. Если элемент отсутствует, будет возвращено значение null.

```csharp
public static IDataBinding GetElementDataBinding(
    IDependable parent,
    IWorkflowForm form,
    XmlNode node,
    IDataBindingProvider dataBindingProvider,
    string path,
    string name,
    object targetObject = null)
```

### **Параметры** <a href="#parameters" id="parameters"></a>

`parent` IDependable\
Родительский объект.

`form` IWorkflowForm\
Форма, на которой описана элемент.

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

### **Примеры** <a href="#examples" id="examples"></a>

В следующем примере извлекается значение тэга `<Text>`, вложенного в тэг `<SettingsSqlQuery>`:

```csharp
```

Описание команды типа MyCommand в xml-файле:

```xml
```

