# MyObject

## Шаблон кастомного объекта <a href="#custom-object-template" id="custom-object-template"></a>

```xml
<MyObject Name="CustomControl" Type="CustomControl" Assembly="Template">
  <!-- Элементы объекта -->
</MyObject>
```

В атрибуте `Type` указывается имя класса, описывающего кастомный объект, а в качестве значения атрибута `Assembly` указывается имя сборки, в которой реализован этот класс.&#x20;

Для удобства и разделения логики объекта, исходный код можно разбить на два класса:

* CustomControlSettings - класс загрузки данных из xml-файла;
* CustomControl - класс с бизнес-логикой.

### Парсинг xml

Полный код шаблона класса CustomControlSettings:

```csharp
using System.Xml;

namespace WorkflowForms.Controls
{
    public class CustomControlSettings : ControlSettings
    {
        /*
         * Набор свойств для хранения данных
         * public string PropertyName { get; private set; }
         */

        public CustomControlSettings(IDependable parent, XmlNode node, IWorkflowForm form, IDataBindingProvider dataBindingProvider)
            : base(node, form)
        {
            /*
             * Здесь должен быть парсинг xml-кода,
             * чтобы получить значения тэгов и их атрибутов.
             */
        }
    }
}
```

Конструктор класса принимает параметры:

* `parent` IDependable - ссылка на экземпляр класса CustomCommand, для которого создавался объект типа CustomCommandSettings;
* `form` IWorkflowForm - форма, на которой описана кастомная команда;
* `node` XmlNode - узел, который соответствует тэгу \<Command>, описанному в xml-файле формы.
* `dataBindingProvider` IDataBindingProvider -&#x20;

### Исполняемый код <a href="#executable-code" id="executable-code"></a>

Класс кастомного объекта наследуем от класса **Control**:

```csharp
using System;
using System.Collections.Generic;
using System.Xml;

namespace WorkflowForms.Controls
{
    public class CustomControl : Control
    {
        private const string SET_PROPERTY = "SetProperty";
        private const string ANY_PROPERTY = "AnyProperty";

        #region Base Properties
        /*
         * Кастомный объект может не иметь какого-то конкретного значения,
         * а необходимые данные можно получать через get-проперти
         */
        public override object Value
        {
            get => null;
            set => throw new NotImplementedException();
        }

        /*
         * Так как кастомный объект не имеет графического представления,
         * свойства Visible и Enabled не имеют реализации и возврящают false
         */
        public override bool Visible
        {
            get => false;
            set => throw new NotImplementedException();
        }

        public override bool Enabled
        {
            get => false;
            set => throw new NotImplementedException();
        }
        #endregion

        #region Custom Properties
        private string _anyProperty;
        private string AnyProperty
        {
            get
            {
                return _anyProperty;
            }
            set
            {
                _anyProperty = value;
                // Когда изменяется значение свойства объекта, необходимо сделать рассылку события всем подписчикам.
                OnPropertyChange(ANY_PROPERTY);
            }
        }
        #endregion

        protected override void UpdateValue()
        {
            /*
             * Метод обновления значения базового свойства Value.
             */
            throw new NotImplementedException();
        }

        public CustomControl(IWorkflowForm form, XmlNode node) : base(form, node)
        {
        }

        #region Инициализация объекта
        protected override void InternalInit(XmlNode node, IWorkflowForm form, IControl parentControl, System.Windows.Forms.Control parentWindowsControl)
        {
            base.InternalInit(node, form, parentControl, parentWindowsControl);

            LoadSettings(node, form);

            /*
             * На объекты формы и DataBinding можно повесить Handler,
             * которые будут автоматически срабатывать при изменении
             * объектов и DataBinding
             * 
             * Например:
             * _anyDatabaseTable.Change += RecountChangeHandler;
             * _somethingDataBinding.Change += RecountChangeHandler;
             * 
             * В зависимости от задачи, каждый объект и DataBinding
             * могут быть привязаны разные Handler
             */
        }

        private void LoadSettings(XmlNode node, IWorkflowForm form)
        {
            var settings = new CustomControlSettings(this, node, form, new DataBindingProvider());
            /*
             * Получение значений и сохранение в переменные
             */
        }
        #endregion

        private void RecountChangeHandler(object sender, EventArgs args)
        {
            Recount();
        }

        private async void Recount()
        {
            /*
             * Здесь должен быть код бизнес-логики
             */

            // Свойству кастомного объекта присваивается какое-то значение
            AnyProperty = "Result";
        }

        #region SetProperty
        public override void SetProperty(string propertyName, Dictionary<string, object> parameters)
        {
            if (propertyName.Equals(SET_PROPERTY, StringComparison.OrdinalIgnoreCase))
            {
                // Логика обработки
                Recount();
            }
            else
            {
                base.SetProperty(propertyName, parameters);
            }
        }
        #endregion

        #region GetProperty
        public override object GetProperty(string propertyName, Dictionary<string, object> parameters)
        {
            if (propertyName.Equals(ANY_PROPERTY, StringComparison.OrdinalIgnoreCase))
            {
                return AnyProperty;
            }
            else
            {
                return base.GetProperty(propertyName, parameters);
            }
        }
        #endregion
    }
}
```

При наследовании класса Control необходимо реализовать свойства Value, Visible и Enabled. Кастомный объект не имеет значения, поэтому свойство Value возвращает null. Свойства Visible и Enabled возвращают false, так как объект не имеет графического интерфейса.

### Get/Set-проперти

Для удобного взаимодействия с объектом и полного контроля за его работой следует реализовывать Get/Set-проперти, с помощью которых можно отдавать команды на проведение вычислений или взаимодействия со сторонними сервисами. Реализация get и set-проперти описывается в методах GetProperty и SetProperty соответственно.

Обращение к set-проперти происходит через команду [ValueSetCommand](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/commands/value_set_command):

```xml
<Command Name="CustomControlValueSetCommand" Type="ValueSetCommand" Assembly="Commands">
  <Object Name="CustomControl">
    <Property Name="AnySetProperty">
      <Parameters>
        <Parameter Name="FirstParameter">value</Parameter>
      </Parameters>
    </Property>
  </Object>
</Command>
```

В классе необходимо переопределить метод SetProperty:

```csharp
public override void SetProperty(string propertyName, Dictionary<string, object> parameters)
{
    if (propertyName.Equals("AnySetProperty", StringComparison.OrdinalIgnoreCase))
    {
        // Логика обработки
        // Recount();
    }
    else
    {
        base.SetProperty(propertyName, parameters);
    }
}
```

В параметре parameters метода SetProperty хранится словарь всех параметров, описанных в команде ValueSetCommand в тэге `<Parameters>`.

Аналогичным образом отработает get-проперти:

```xml
<Object Name="CustomControl">
  <Property Name="AnyGetProperty"/>
</Object>
```

В классе необходимо переопределить метод GetProperty:

```csharp
public override object GetProperty(string propertyName, Dictionary<string, object> parameters)
{
    if (propertyName.Equals("AnyGetProperty", StringComparison.OrdinalIgnoreCase))
    {
        // Логика обработки
        // return AnyProperty;
    }
    else
    {
        return base.GetProperty(propertyName, parameters);
    }
}
```

В параметре parameters метода GetProperty хранится словарь всех параметров, описанных в тэге `<Parameters>` при обращении к get-проперти.

## Элементы объекта <a href="#object-elements" id="object-elements"></a>

### Константы и переменные <a href="#constants-and-variables" id="constants-and-variables"></a>

Для задания констант используем значения атрибутов, а для задания переменных - значения тэгов. В примере рассмотрим xml-код объекта с необязательным тэгом `<Flag>` и обязательным тэгом `<Something>`:

```xml
<MyObject Name="CustomControl" Type="CustomControl" Assembly="Template">
  <Flag Value="True" />
  
  <Something>
    <Object Name="MyVariable" />
  </Something>  
</MyObject>
```

#### Парсинг xml <a href="#const-and-var-parsing-xml" id="const-and-var-parsing-xml"></a>

В конструкторе класса CustomControlSettings необходимо прописать код вида:

```csharp
// public bool Flag { get; private set; }

Flag = XmlParser.GetAttributeValue(form, node, "Flag", "Value", false);
```

Так как тэг `<Flag>` является необязательным, то для получения его значения используется статический метод GetAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). Если тэг `<Flag>` или его атрибут `Value` будут отсутствовать в описании объекта, то свойству Flag будет присвоено значение по умолчанию, переданное пятым параметром (T defaultValue).

```csharp
// using WorkflowForms.DataBindings;

// public IDataBinding SomethingDataBinding { get; private set; }

SomethingDataBinding = XmlParser.GetRequiredElementDataBinding(
    parent, form, node, dataBindingProvider,
    "Something", ((Control)parent).Name, parent);
```

Для работы с переменным значением, указанным в тэге `<Something>`, используется механизм привязки данных, который позволяет в любой момент выполнения логики объекта получать актуальное значение объекта с именем MyVariable.

Для получения привязки к источнику данных в тэге `<Something>` используем статический метод GetRequiredElementDataBinding.

#### Исполняемая часть <a href="#const-and-var-executable-part" id="const-and-var-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить нужные значения, обратившись к соответствующим публичным свойствам:&#x20;

```csharp
// using WorkflowForms.DataBindings;

// public bool _flag;
// public IDataBinding _somethingDataBinding;

_flag = settings.Flag;
_somethingDataBinding = settings.SomethingDataBinding;
```

Если используется привязка данных, то на изменение привязанного объекта можно повесить Handler, который будет автоматически выполняться:

```csharp
_somethingDataBinding.Change += RecountChangeHandler;
```

Где RecountChangeHandler представлен методом вида:

```csharp
private void RecountChangeHandler(object sender, EventArgs args)
{
    Recount();
}
```

В рассматриваемом примере при изменении значения объекта с именем MyVariable, будет выполняться метод Recount.

При реализации бизнес-логики объекта работа с константными значениями простая:

```csharp
if (_flag)
{
    // ...
}
```

А для получения значения из объекта типа IDataBinding необходимо использовать метод GetValue:

```csharp
var value = _somethingDataBinding.GetValue();
```

Полный список методов по [ссылке](../platform-classes/data_binding.md).

### Условие <a href="#condition" id="condition"></a>

В примере рассмотрим xml-код объекта с обязательным тэгом `<AnyCondition>` для передачи имени условия:

```xml
<MyObject Name="CustomControl" Type="CustomControl" Assembly="Template">
  <AnyCondition Name="MyCondition" />
</MyObject>
```

#### Парсинг xml <a href="#condition-parsing-xml" id="condition-parsing-xml"></a>

В конструкторе класса CustomControlSettings необходимо прописать код вида:

```csharp
// using WorkflowForms.Conditions;

// public ICondition AnyCondition { get; private set; }
 
string name = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "AnyCondition", NAME_ATTRIBUTE);
    
AnyCondition = form.GetCondition(name);
```

Так как имя условия указывается в качестве значения атрибута обязательного тэга, то для его получения используется статический метод GetRequiredAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). В данном случае используется константа `NAME_ATTRIBUTE`, описанная в базовом классе. Если элемент или его атрибут отсутствует, будет возвращено исключение типа InvalidXmlException.

{% hint style="info" %}
Если необходимо получить значение необязательного атрибута, то следует использовать метод GetAttributeValue. Этот метод в случае отсутствия элемента или его атрибута будет возвращать значение по умолчанию, переданное в параметрах метода.
{% endhint %}

Имея имя условия, объект типа ICondition можно получить через переменную form типа IWorkflowForm, используя метод GetCondition.

#### Исполняемая часть <a href="#condition-executable-part" id="condition-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить объект типа ICondition, обратившись к публичному свойству AnyCondition:&#x20;

```csharp
// using WorkflowForms.Conditions;

// public ICondition _anyCondition;

_anyCondition = settings.AnyCondition;
```

При реализации бизнес-логики объекта можно получать значение условия, обращаясь к свойству Value:

```csharp
if (_anyCondition.Value)
{
    // ...
}
```

### Команды <a href="#commands" id="commands"></a>

В примере рассмотрим xml-код объекта с обязательным тэгом `<AnyCommand>` для передачи имени команды, которую должен вызвать объект:

```xml
<MyObject Name="CustomControl" Type="CustomControl" Assembly="Template">
  <AnyCommand Name="MyCommand" />
</MyObject>
```

#### Парсинг xml <a href="#command-parsing-xml" id="command-parsing-xml"></a>

В конструкторе класса CustomControlSettings необходимо прописать код вида:

```csharp
// public ICommand AnyCommand { get; set; }

string name = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "AnyCommand", NAME_ATTRIBUTE);
    
AnyCommand = form.GetCommand(name);
```

Для получения имени команды используется статический метод GetRequiredAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). В данном случае используется константа `NAME_ATTRIBUTE`, описанная в базовом классе. Если элемент или его атрибут отсутствует, будет возвращено исключение типа InvalidXmlException.

{% hint style="info" %}
Если необходимо получить значение необязательного атрибута, то следует использовать метод GetAttributeValue. Этот метод в случае отсутствия элемента или его атрибута будет возвращать значение по умолчанию, переданное в параметрах метода.
{% endhint %}

Имея имя команды, объект типа ICommand можно получить через переменную form типа IWorkflowForm, используя метод GetCommand.

#### Исполняемая часть <a href="#command-executable-part" id="command-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить объект типа ICommand, обратившись к публичному свойству AnyCommand:&#x20;

```csharp
// private ICommand _anyCommand;

_anyCommand = settings.AnyCommand;
```

В методе ExecuteAsyncCommand у объекта типа ICommand можно вызвать метод Execute, который запустит процесс выполнения команды:

```csharp
// var parameters = new Dictionary<string, object>() {  };
await _anyCommand.Execute(parameters);
```

В метод Execute можно передать словарь параметров, если команда использует входные параметры.

Получить результат выполнения команды:

```csharp
var commandResult = _anyCommand.GetParameter("Value").ToString();
```

### Таблица <a href="#databasetable" id="databasetable"></a>

В примере рассмотрим xml-код объекта с обязательным тэгом `<AnyCommand>` для передачи имени команды, которую должен вызвать объект:

```xml
<MyObject Name="CustomControl" Type="CustomControl" Assembly="Template">
  <AnyDatabaseTable Name="MyDatabaseTable">
    <AnyColumn Name="SecondColumn" />
  </AnyDatabaseTable>
</MyObject>
```

#### Парсинг xml <a href="#databasetable-parsing-xml" id="databasetable-parsing-xml"></a>

В конструкторе класса CustomControlSettings необходимо прописать код вида:

```csharp
// using WorkflowForms.Controls;
// using WorkflowForms.Exceptions;

// public DatabaseTable AnyDatabaseTable { get; private set; }
// public string AnyColumnName { get; private set; }
        
if (node["AnyDatabaseTable"] != null)
{
    string name = XmlParser.GetRequiredAttributeValue<string>(
        form, node, "AnyDatabaseTable", NAME_ATTRIBUTE);
    AnyDatabaseTable = (DatabaseTable)form.GetControl(name);

    AnyColumnName = XmlParser.GetRequiredAttributeValue<string>(
        form, node, "AnyDatabaseTable/AnyColumn", NAME_ATTRIBUTE);
}
else
{
    throw new InvalidXmlException($"Не задано имя таблицы в поле AnyDatabaseTable объекта \"{Name}\" типа {GetType().Name}.");
}
```

#### Исполняемая часть <a href="#command-executable-part" id="command-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить объект типа DatabaseTable, обратившись к публичному свойству AnyDatabaseTable:&#x20;

```csharp
// public DatabaseTable _anyDatabaseTable;
// public string _anyColumnName;

_anyDatabaseTable = settings.AnyDatabaseTable;
_anyColumnName = settings.AnyColumnName;
```

При реализации бизнес-логики у любого объекта можно вызывать метод GetProperty для обращения к get-проперти, реализованному у объекта. Например, можно обратиться к get-проперти [Column](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/objects/databasetable#get_column) у объекта DatabaseTable:

```csharp
var arr = (object[])_anyDatabaseTable.GetProperty(
    "Column", new Dictionary<string, object>() { ["ColumnName"] = _anyColumnName });
```

Первым аргументом идет имя get-проперти, а затем словарь параметров, которые принимает проперти объекта.

Так же можно вызывать метод SetProperty, чтобы обращаться к set-проперти объекта:

```
_anyDatabaseTable.SetProperty("UpdateRow", new Dictionary<string, object>()
{
    ["RowIndex"] = 0,
    ["ColumnNames"] = new object[] { "ThirdColumn" },
    ["Values"] = new object[] { false }
});
```

### Логирование <a href="#logging" id="logging"></a>

Для добавления записей в журнал событий Windows используется класс Logger со статическим методом Write.

Пример добавления сообщения уровня Error в методе ExecuteAsyncCommand:

```csharp
Logger.Write(new LogEntry()
{
    Form = Form,
    Message = $"Ошибка выполнения команды \"{Name}\" типа \"{GetType().Name}\".\r\n{e}",
    Severity = EventLogEntryType.Error
});
```

Обычно используются уровни событий: Error, Warning, Information.

Пример добавления сообщения уровня Information в конструкторе CustomCommandSettings при парсинге xml-кода команды:

```csharp
Logger.Write(new LogEntry()
{
    Form = Form,
    Message = $"Парсинг команды \"{((AbstractCommand)parent).Name}\" типа \"{parent.GetType().Name}\".",
    Severity = EventLogEntryType.Information
});
```
