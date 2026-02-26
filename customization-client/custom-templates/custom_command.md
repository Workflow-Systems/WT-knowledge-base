# Command

## Шаблон кастомной команды <a href="#custom-command-template" id="custom-command-template"></a>

Шаблон описания кастомной команды на форме:

```xml
<Command Name="CustomCommand" Type="CustomCommand" Assembly="Template">
    <!-- Элементы команды -->
</Command>
```

В атрибуте `Type` указывается имя класса, описывающего кастомную команду, а в качестве значения атрибута `Assembly` указывается имя сборки, в которой реализован этот класс.&#x20;

Для удобства и разделения логики команды, исходный код кастомной команды можно разбить на два класса:

* CustomCommandSettings- класс загрузки данных из xml-файла;
* CustomCommand - класс с бизнес-логикой.

### Парсинг xml

Полный код шаблона:

```csharp
using System.Collections.Generic;
using System.Xml;

namespace WorkflowForms.Commands
{
    public class CustomCommandSettings : AbstractCommandXmlSettings
    {
        /*
         * Набор свойств для хранения данных
         * public string PropertyName { get; private set; }
         */

        public CustomCommandSettings(IDependable parent, IWorkflowForm form, XmlNode node, IDataBindingProvider dataBindingProvider)
            : base(parent, form, node)
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

Кастомную команду наследуем от класса **AsyncCommand**:

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Threading.Tasks;
using System.Xml;

namespace WorkflowForms.Commands
{
    public class CustomCommand : AsyncCommand
    {
        private IWorkflowForm _form;

        public override void InternalInit(XmlNode node, IWorkflowForm form)
        {
            base.InternalInit(node, form);
            _form = form;

            var settings = new CustomCommandSettings(this, form, node, new DataBindingProvider(this));
            LoadSettings(settings);
        }

        protected void LoadSettings(CustomCommandSettings settings)
        {
            base.LoadSettings(settings);
            /*
             * Получение значений и сохранение в переменные
             */
        }

        protected override async Task<Dictionary<string, object>> ExecuteAsyncCommand()
        {
            /*
             * Результатом выполнения любой команды должен быть словарь параметров.
             * Ключи словаря могут быть любыми.
             */
            var result = new Dictionary<string, object>() {
                { "Value", null },
                { "Error", false },
                { "ErrorMessage", null }
            };
            /*
             * Если словарь содержит параметр с именем Value, то значение параметра
             * можно получить, обратившись к команде по имени:
             * <Command Name="CustomCommand" />.
             * Иначе значение параметра получаем по имени через атрибут Parameter:
             * <Command Name="CustomCommand" Parameter="Error" />
             */

            try
            {
                /*
                 * Здесь должен быть код бизнес-логики: выполнение SQL-запросов и команд,
                 * а так же обработка их результатов
                 */
                result["Value"] = "Результат";
            }
            catch (Exception e)
            {
                Logger.Write(new LogEntry()
                {
                    Form = Form,
                    Message = $"Ошибка выполнения команды {Name} типа {GetType().Name}.\r\n{e}",
                    // Обычно используются типы событий: Error, Warning, Information
                    Severity = EventLogEntryType.Error
                });

                result["Error"] = true;
                result["ErrorMessage"] = "При выполнении команды возникли ошибки. Обратитесь к администратору.";
            }

            return result;
        }
    }
}
```

### Результат команды <a href="#command-result" id="command-result"></a>

Результатом выполнения любой команды должен быть словарь параметров.

```csharp
var result = new Dictionary<string, object>() {
    { "Value", null },
    { "Error", false },
    { "ErrorMessage", null }
};
```

Если словарь содержит параметр с именем Value, то значение этого параметра можно получить, обратившись к команде по имени:

```xml
<Command Name="CustomCommand" />
```

В противном случае значение параметра получаем по имени через атрибут `Parameter`:

```xml
<Command Name="CustomCommand" Parameter="Error" />
```

## Элементы команды <a href="#command-elements" id="command-elements"></a>

### Константы и переменные <a href="#constants-and-variables" id="constants-and-variables"></a>

Для задания констант используем значения атрибутов, а для задания переменных - значения тэгов. В примере рассмотрим xml-код команды с необязательным тэгом `<Flag>` и обязательным тэгом `<Something>`:

```xml
<Command Name="CustomCommand" Type="CustomCommand" Assembly="Template">
  <Flag Value="True" />
  
  <Something>
    <Object Name="MyVariable" />
  </Something>  
</Command>
```

#### Парсинг xml <a href="#const-and-var-parsing-xml" id="const-and-var-parsing-xml"></a>

В конструкторе класса CustomCommandSettings необходимо прописать код вида:

```csharp
// public bool Flag { get; private set; }

Flag = XmlParser.GetAttributeValue(form, node, "Flag", "Value", false);
```

Так как тэг `<Flag>` является необязательным, то для получения его значения используется статический метод GetAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). Если тэг `<Flag>` или его атрибут `Value` будут отсутствовать в описании команды, то свойству Flag будет присвоено значение по умолчанию, переданное пятым параметром (T defaultValue).

```csharp
// using WorkflowForms.DataBindings;

// public IDataBinding SomethingDataBinding { get; private set; }

SomethingDataBinding = XmlParser.GetRequiredElementDataBinding(
    parent, form, node, dataBindingProvider, "Something",
    ((AbstractCommand)parent).Name, parent);
```

Для работы с переменным значением, указанным в тэге `<Something>`, используется механизм привязки данных, который позволяет в любой момент выполнения кастомной команды получать актуальное значение объекта с именем MyVariable.

Для получения привязки к источнику данных в тэге `<Something>` используем статический метод GetRequiredElementDataBinding.



#### Исполняемая часть <a href="#const-and-var-executable-part" id="const-and-var-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить нужные значения, обратившись к соответствующим публичным свойствам:&#x20;

```csharp
// public bool _flag;
// public IDataBinding _somethingDataBinding;

_flag = settings.Flag;
_somethingDataBinding = settings.SomethingDataBinding;
```

В методе ExecuteAsyncCommand работа с константными значениями простая:

```
if (_flag)
{
    // ...
}
```

А для получения значения из объекта типа IDataBinding необходимо использовать метод GetValue:

```
var value = _somethingDataBinding.GetValue();
```

Полный список методов по [ссылке](../platform-classes/data_binding.md).

### Условие <a href="#condition" id="condition"></a>

В примере рассмотрим xml-код команды с обязательным тэгом `<AnyCondition>` для передачи имени условия:

```xml
<Command Name="CustomCommand" Type="CustomCommand" Assembly="Template">
  <AnyCondition Name="MyCondition" />
</Command>
```

#### Парсинг xml <a href="#condition-parsing-xml" id="condition-parsing-xml"></a>

В конструкторе класса CustomCommandSettings необходимо прописать код вида:

```csharp
// using WorkflowForms.Conditions;

// public ICondition AnyCondition { get; private set; }
 
var name = XmlParser.GetRequiredAttributeValue<string>(
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
// public ICondition _anyCondition;

_anyCondition = settings.AnyCondition;
```

В методе ExecuteAsyncCommand можно получать значение условия, обращаясь к свойству Value:

```csharp
if (_anyCondition.Value)
{
    // ...
}
```

### Параметры <a href="#parameters" id="parameters"></a>

```xml
<Command Name="CustomCommand" Type="CustomCommand" Assembly="Template">
  <Parameters>
    <Parameter Name="First">First Value</Parameter>
    <Parameter Name="Second">Second Value</Parameter>
  </Parameters>
</Command>
```

#### Парсинг xml <a href="#parameters-parsing-xml" id="parameters-parsing-xml"></a>

В конструкторе класса CustomCommandSettings необходимо прописать код вида:

```csharp
// public Dictionary<string, IDataBinding> Parameters { get; set; }

var parametersXmlSettings = new ParametersXmlSettings(
    parent, form, node, dataBindingProvider, typeof(WorkflowForms.Parameter));
    
Parameters = parametersXmlSettings.GetParametersDataBindings();
```

#### Исполняемая часть <a href="#parameters-executable-part" id="parameters-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить словарь параметров:&#x20;

```csharp
// private Dictionary<string, IDataBinding> _parameters;

_parameters = settings.Parameters;
```

В методе ExecuteAsyncCommand можно получать значение параметров через метод GetValue, так как каждый параметр представлен объектом типа IDataBinding:

```csharp
foreach (string parameterName in _parameters.Keys)
{
    var value = _parameters[parameterName].GetValue();
}
```

### SQL-запросы <a href="#sql-queries" id="sql-queries"></a>

Если команда должна отправлять на сервер запрос на выполнение SqlQuery, то всегда должно указываться имя процесса (WorkflowName), в рамках которого описан SQL-запрос. Аналогичный тэг указывается в описании [PrimaryGetDataConnection](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/dataconnections/primary_dc/sql_query#sql_query_workflow).

```xml
<Command Name="CustomCommand" Type="CustomCommand" Assembly="Template">
  <Workflow Name="Template" />
  <AnySqlQuery Name="MySelectSqlQuery" />
</Command>
```

#### Парсинг xml <a href="#sql-parsing-xml" id="sql-parsing-xml"></a>

В конструкторе класса CustomCommandSettings необходимо прописать код вида:

```csharp
// public string WorkflowName { get; private set; }
// public string AnySqlQueryName { get; private set; }

WorkflowName = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "Workflow", NAME_ATTRIBUTE);
    
AnySqlQueryName = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "AnySqlQuery", NAME_ATTRIBUTE);
```

Так как имя процесса и запрос являются обязательными для описания в команде, то для получения их значений используется статический метод GetRequiredAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). В данном случае используется константа `NAME_ATTRIBUTE`, описанная в базовом классе. Если элемент или его атрибут отсутствует, будет возвращено исключение типа InvalidXmlException.

#### Исполняемая часть <a href="#sql-executable-part" id="sql-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить имя процесса и имя SQL-запроса, который нужно выполнять:&#x20;

```csharp
// public string _workflowName;
// public string _anySqlQueryName;

_workflowName = settings.WorkflowName;
_anySqlQueryName = settings.AnySqlQueryName;
```

В методе ExecuteAsyncCommand для выполнения запроса к серверной части, необходимо из объекта формы получить клиента серверной части (WorkflowEngineClient). В метод ExecuteQuery клиента необходимо передать имя процесса, имя SQL-запроса (описанного в серверном xml-файле) и словарь параметров, значения которых должны подставляться в текст запроса:

```csharp
/*
 * Формирование словаря параметров, значения которых будут
 * подставляться в текст запроса вместо переменных
 */
var parameters = new Dictionary<string, object>() {
    // ...
};

DataTable table = await _form.WorkflowEngineClient.ExecuteQuery(
    _workflowName, _anySqlQueryName, parameters
).ConfigureAwait(false);

// var arr = table.AsEnumerable().ToList().Select(row => row["title"]);
```

Результатом выполнения метода ExecuteQuery  будет таблица типа DataTable, дальнейшая работа с которой может быть с помощью [LINQ](https://learn.microsoft.com/ru-ru/dotnet/csharp/linq/), либо путем перебора всех строк:

```csharp
foreach (var row in table.Rows)
{
    // ...
}
```

### Команды <a href="#commands" id="commands"></a>

```xml
<Command Name="CustomCommand" Type="CustomCommand" Assembly="Template">
  <AnyCommand Name="MyCommand" />
</Command>
```

#### Парсинг xml <a href="#command-parsing-xml" id="command-parsing-xml"></a>

В конструкторе класса CustomCommandSettings необходимо прописать код вида:

```csharp
// public ICommand AnyCommand { get; set; }

var name = XmlParser.GetRequiredAttributeValue<string>(
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

```xml
<Command Name="CustomCommand" Type="CustomCommand" Assembly="Template">
  <AnyDatabaseTable Name="MyDatabaseTable">
    <AnyColumn Name="SecondColumn" />
  </AnyDatabaseTable>
</Command>
```

#### Парсинг xml <a href="#databasetable-parsing-xml" id="databasetable-parsing-xml"></a>

В конструкторе класса CustomCommandSettings необходимо прописать код вида:

```csharp
// using WorkflowForms.Controls;
// using WorkflowForms.Exceptions;

// public DatabaseTable AnyDatabaseTable { get; private set; }
// public string AnyColumnName { get; private set; }

if (node["AnyDatabaseTable"] != null)
{
    name = XmlParser.GetRequiredAttributeValue<string>(form, node, "AnyDatabaseTable", NAME_ATTRIBUTE);
    AnyDatabaseTable = (DatabaseTable)form.GetControl(name);

    AnyColumnName = XmlParser.GetRequiredAttributeValue<string>(form, node, "AnyDatabaseTable/AnyColumn", NAME_ATTRIBUTE);
}
else
{
    throw new InvalidXmlException($"Не задано имя таблицы в поле AnyDatabaseTable команды \"{Name}\" типа \"{GetType().Name}\".");
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

В методе ExecuteAsyncCommand у любого объекта можно вызывать метод GetProperty для обращения к get-проперти, реализованному у объекта. Например, можно обратиться к get-проперти [Column](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/objects/databasetable#get_column) у объекта DatabaseTable:

```csharp
// Пример обработки строк таблицы и обращения к ее get-проперти
var arr = (object[])_anyDatabaseTable.GetProperty(
    "Column", new Dictionary<string, object>() { ["ColumnName"] = _anyColumnName });
```

Первым аргументом идет имя get-проперти, а затем словарь параметров, которые принимает проперти объекта.

Так же можно вызывать метод SetProperty, чтобы обращаться к set-проперти объекта.

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
