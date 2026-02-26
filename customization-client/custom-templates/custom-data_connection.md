# DataConnection

Необходимо подключение библиотеки DataConnection.dll.

## Шаблон кастомного DataConnection <a href="#custom-dataconnection-template" id="custom-dataconnection-template"></a>

```xml
<DataConnection Name="CustomGetDataConnection" Type="CustomGetDataConnection" Assembly="Template">
  <!-- Элементы DataConnection -->
</DataConnection>
```

В атрибуте `Type` указывается имя класса, описывающего кастомный DataConnection, а в качестве значения атрибута `Assembly` указывается имя сборки, в которой реализован этот класс.&#x20;

Для удобства и разделения логики, исходный код DataConnection можно разбить на два класса:

* CustomGetDataConnectionXmlSettings - класс загрузки данных из xml-файла;
* CustomGetDataConnection - класс с логикой формирования результирующей таблицы.

### Парсинг xml

Полный код шаблона:

```csharp
using System.Xml;

namespace WorkflowForms.DataConnections
{
    public class CustomGetDataConnectionXmlSettings : GetDataConnectionXmlSettings
    {
        /*
         * Набор свойств для хранения данных
         * public string PropertyName { get; private set; }
         */
         
        public CustomGetDataConnectionXmlSettings(IDependable parent, IWorkflowForm form, XmlNode node, XmlSqlQueryProvider queryProvider)
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

Полный код класса:

```csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Threading.Tasks;

namespace WorkflowForms.DataConnections
{
    public class CustomGetDataConnection : GetDataConnection
    {
        private const string SET_PROPERTY = "SetProperty";
        private const string ANY_PROPERTY = "AnyProperty";

        private DataTable _table;

        private IWorkflowForm _form;

        protected List<Item> list = new List<Item>();

        // Менеджер строковых ресурсов для поддержки языков в интерфейсе
        // private Template.StringManager _manager;

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

        public CustomGetDataConnection(IWorkflowForm form, System.Xml.XmlNode node)
            : base(form, node)
        {
        }

        #region Инициализация DC
        public override void InternalInit(System.Xml.XmlNode node, IWorkflowForm form)
        {
            base.InternalInit(node, form);
            _form = form;

            // _manager = new Template.StringManager(form);

            LoadSettings(node, form);

            // Создаем структуру результирующей таблицы
            CreateTable();
        }

        protected void LoadSettings(System.Xml.XmlNode node, IWorkflowForm form)
        {
            var settings = new CustomGetDataConnectionXmlSettings(this, form, node, new XmlSqlQueryProvider());

            /*
             * Получение значений и сохранение в переменные
             */
        }

        public override async Task PostInit()
        {
            await Refresh();
            await Task.CompletedTask;
        }
        #endregion

        #region Основная логика
        // Метод, через который элементы формы будут получать данные из DC
        public override DataTable GetDataTable()
        {
            return _table;
        }

        public override async Task Refresh(string queryName = null)
        {
            await RefreshDataAsync();
            FillTable();

            // Вызов метода для рассылки события подписчикам об изменении данных в DC
            OnChange();
        }

        private async Task RefreshDataAsync()
        {
            // Получение строкового ресурса по ключу
            // string str = _manager.GetString("TextId");
          

        }
        #endregion

        #region SetProperty
        public override void SetProperty(string propertyName, Dictionary<string, object> parameters)
        {
            if (propertyName.Equals(SET_PROPERTY, StringComparison.OrdinalIgnoreCase))
            {
                // Логика обработки
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

        #region Заполнение таблицы
        private void FillTable()
        {
            _table.Clear();

            list.ForEach(item =>
            {
                DataRow row = _table.NewRow();
                row["FirstColumn"] = item.FirstField;
                row["SecondColumn"] = item.SecondField;
                row["ThirdColumn"] = item.ThirdField;
                _table.Rows.Add(row);
            });
        }
        #endregion

        #region Создание таблицы
        private void CreateTable()
        {
            // Пример создания колонок результирующей таблицы
            _table = new DataTable("myTableName");
            _table.Columns.Add(CreateColumn("FirstColumn", typeof(int)));
            _table.Columns.Add(CreateColumn("SecondColumn", typeof(string)));
            _table.Columns.Add(CreateColumn("ThirdColumn", typeof(bool)));

        }

        private DataColumn CreateColumn(string columnName, Type dataType, object defaultValue = null)
        {
            var column = new DataColumn
            {
                ColumnName = columnName,
                DataType = dataType
            };

            if (defaultValue != null)
            {
                column.DefaultValue = defaultValue;
            }

            return column;
        }
        #endregion
    }

    #region Вложенные типы
    public class Item
    {
        public int FirstField { get; set; }

        public string SecondField { get; set; }

        public bool ThirdField { get; set; }
    }
    #endregion
}
```

### Get/Set-проперти

Кастомный GetDataConnection может иметь свои get и set-проперти, реализация которых описывается в методах GetProperty и SetProperty соответственно.

Обращение к set-проперти происходит через команду [ValueSetCommand](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/commands/value_set_command):

```xml
<Command Name="ValueSetCommand" Type="ValueSetCommand" Assembly="Commands">
  <DataConnection Name="CustomGetDataConnection">
    <Property Name="SetProperty">
      <Parameters>
        <Parameter Name="FirstParameter">value</Parameter>
      </Parameters>
    </Property>
  </DataConnection>
</Command>
```

В классе необходимо переопределить метод SetProperty:

```csharp
private const string SET_PROPERTY = "SetProperty";

public override void SetProperty(string propertyName, Dictionary<string, object> parameters)
{
    if (propertyName.Equals(SET_PROPERTY, StringComparison.OrdinalIgnoreCase))
    {
        // Логика обработки
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
<DataConnection SourceDataConnection="CustomGetDataConnection">
  <Property Name="AnyProperty">
    <Parameters>
      <Parameter Name="FirstParameter">value</Parameter>
    </Parameters>
  </Property>
</DataConnection>
```

В классе необходимо переопределить метод GetProperty:

```csharp
private const string ANY_PROPERTY = "AnyProperty";

public override object GetProperty(string propertyName, Dictionary<string, object> parameters)
{
    if (propertyName.Equals(GET_PROPERTY, StringComparison.OrdinalIgnoreCase))
    {
        return AnyProperty;
    }
    else
    {
        return base.GetProperty(propertyName, parameters);
    }
}
```

В параметре parameters метода GetProperty хранится словарь всех параметров, описанных в тэге `<Parameters>` при обращении к get-проперти.

## Элементы DataConnection <a href="#dataconnection-elements" id="dataconnection-elements"></a>

### Условие <a href="#condition" id="condition"></a>

В примере рассмотрим xml-код соединения с данными с обязательным тэгом `<AnyCondition>` для передачи имени условия:

```xml
<DataConnection Name="CustomGetDataConnection" Type="CustomGetDataConnection" Assembly="Template">
  <AnyCondition Name="MyCondition" />
</DataConnection>
```

#### Парсинг xml <a href="#condition-parsing-xml" id="condition-parsing-xml"></a>

В конструкторе класса CustomGetDataConnectionXmlSettings необходимо прописать код вида:

```csharp
// using WorkflowForms.Conditions;

// public ICondition AnyCondition { get; private set; }
 
string name = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "AnyCondition", "Name");
    
AnyCondition = form.GetCondition(name);
```

Так как имя условия указывается в качестве значения атрибута обязательного тэга, то для его получения используется статический метод GetRequiredAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). Если элемент или его атрибут отсутствует, будет возвращено исключение типа InvalidXmlException.

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

В методе ExecuteAsyncCommand можно получать значение условия, обращаясь к свойству Value:

```csharp
if (_anyCondition.Value)
{
    // ...
}
```

### SQL-запросы <a href="#sql-queries" id="sql-queries"></a>

Если соединение с данными должно отправлять на сервер запрос на выполнение SqlQuery, то всегда должно указываться имя процесса (WorkflowName), в рамках которого описан SQL-запрос. Аналогичный тэг указывается в описании [PrimaryGetDataConnection](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/dataconnections/primary_dc/sql_query#sql_query_workflow).

```xml
<DataConnection Name="CustomGetDataConnection" Type="CustomGetDataConnection" Assembly="Template">
  <Workflow Name="Template" />
  <AnySqlQuery Name="LanguageSelectSqlQuery" />
</DataConnection>
```

#### Парсинг xml <a href="#sql-parsing-xml" id="sql-parsing-xml"></a>

В конструкторе класса CustomGetDataConnectionXmlSettings необходимо прописать код вида:

```csharp
// public string WorkflowName { get; private set; }
// public string AnySqlQueryName { get; private set; }

WorkflowName = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "Workflow", "Name");
    
AnySqlQueryName = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "AnySqlQuery", "Name");
```

Так как имя процесса и запрос являются обязательными для описания в команде, то для получения их значений используется статический метод GetRequiredAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). Если элемент или его атрибут отсутствует, будет возвращено исключение типа InvalidXmlException.

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

### Объекты формы <a href="#form-objects" id="form-objects"></a>

```xml
<DataConnection Name="CustomGetDataConnection" Type="CustomGetDataConnection" Assembly="Template">
  <AnyControl Name="MyVariable" />
</DataConnection>
```

#### Парсинг xml <a href="#form-objects-parsing-xml" id="form-objects-parsing-xml"></a>

В конструкторе класса CustomGetDataConnectionXmlSettings необходимо прописать код вида:

```csharp
// using WorkflowForms.Controls;

// public IControl AnyConrol { get; private set; }

string name = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "AnyControl", "Name");
    
AnyConrol = form.GetControl(name);
```

Так как имя объекта формы указывается в качестве значения атрибута обязательного тэга, то для его получения используется статический метод GetRequiredAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). Если элемент или его атрибут отсутствует, будет возвращено исключение типа InvalidXmlException.

{% hint style="info" %}
Если необходимо получить значение необязательного атрибута, то следует использовать метод GetAttributeValue. Этот метод в случае отсутствия элемента или его атрибута будет возвращать значение по умолчанию, переданное в параметрах метода.
{% endhint %}

Имея имя объекта формы, объект типа IControl можно получить через переменную form типа IWorkflowForm, используя метод GetControl.

#### Исполняемая часть <a href="#condition-executable-part" id="condition-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить ссылку на объект формы:

```csharp
// using WorkflowForms.Controls;

// private IControl _anyConrol;

_anyConrol = settings.AnyConrol;
```

Для получения значения из объекта формы необходимо обращаться к свойству Value:

```
var value = _anyConrol.Value;
```

### DataConnection

```xml
<DataConnection Name="CustomGetDataConnection" Type="CustomGetDataConnection" Assembly="Template">
  <AnyDataConnection Name="MyPrimaryGetDataConnection" />
</DataConnection>
```

#### Парсинг xml <a href="#dataconnection-parsing-xml" id="dataconnection-parsing-xml"></a>

В конструкторе класса CustomGetDataConnectionXmlSettings необходимо прописать код вида:

```csharp
// public IGetDataConnection AnyDataConnection { get; private set; }

string name = XmlParser.GetRequiredAttributeValue<string>(
    form, node, "AnyDataConnection", "Name");
    
AnyDataConnection = form.GetDataConnection(name);
```

Так как имя DataConnection указывается в качестве значения атрибута обязательного тэга, то для его получения используется статический метод GetRequiredAttributeValue класса XmlParser. В третьем параметре (string path) указываем полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в четвертом параметре (string attribute). Если элемент или его атрибут отсутствует, будет возвращено исключение типа InvalidXmlException.

{% hint style="info" %}
Если необходимо получить значение необязательного атрибута, то следует использовать метод GetAttributeValue. Этот метод в случае отсутствия элемента или его атрибута будет возвращать значение по умолчанию, переданное в параметрах метода.
{% endhint %}

Имея имя соединения с данными, объект типа IGetDataConnection можно получить через переменную form типа IWorkflowForm, используя метод GetDataConnection.

#### Исполняемая часть <a href="#condition-executable-part" id="condition-executable-part"></a>

В методе LoadSettings из объекта settings, переданного в метод в качестве параметра, можно получить ссылку на DataConnection:

```csharp
// using WorkflowForms.DataBindings;

// private IGetDataConnection _anyDataConnection;

_anyDataConnection = settings.AnyDataConnection;
```

Если кастомный DataConnection использует данные другого DataConnection, то необходимо установить зависимость первого от второго, чтобы гарантировать порядок загрузки при старте формы. Для этого в метод InternalInit класса CustomGetDataConnection необходимо добавить строки:

```csharp
SetDependencies(new HashSet<IDependable>()
{
    new DataConnectionDataBinding(
        form, _anyDataConnection, new List<IDataBindingQuery>()),
});
```

Имея ссылку на DataConnection, можно получить его таблицу данных вызвав метод GetDataTable:

```csharp
var dataTable = _anyDataConnection.GetDataTable();            

foreach (DataRow row in dataTable.Rows)
{
    // Convert.ToInt32(row["FirstField"]),
    // Convert.ToString(row["SecondField"]),
    // Convert.ToBoolean(row["ThirdField"])
}
```
