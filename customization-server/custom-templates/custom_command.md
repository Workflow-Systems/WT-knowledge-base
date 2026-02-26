# Command

## Шаблон кастомной команды <a href="#custom-command-template" id="custom-command-template"></a>

Шаблон описания кастомной команды в серверном xml-файле:

```xml
<Command Name="MyCustomCommand" Type="CustomCommand" Assembly="TemplateEngine">
  <!-- Элементы команды -->
</Command>
```

В качестве значения атрибута `Assembly` указывается имя кастомной сборки, в которой реализована команда. А в атрибуте `Type` указывается имя класса, описывающего команду.

На сервере команды разделены на две части: инициируемая часть и исполняемая часть. Инициируемая часть представляется классом AbstractCommand, а исполняемая часть - AbstractCommandExecutor. Инициируемая часть используется для парсинга xml-кода, а исполняемая часть - для выполнения бизнес-логики.

### Инициируемая часть <a href="#init-part" id="init-part"></a>

Шаблон кода инициируемой части:

{% code title="CustomCommand.cs" %}
```csharp
using Common;
using System.Xml;

namespace WorkflowEngine
{
    public class CustomCommand : AbstractCommand
    {
        /*
         * Набор свойств для хранения данных
         * public string PropertyName { get; private set; }
         */
         
        public override void Init(XmlNode node, IWorkflowType workflowType)
        {
            base.Init(node, workflowType);
            
            /*
             * Здесь должен быть парсинг xml-кода,
             * чтобы получить значения тэгов и их атрибутов.
             */
        }

        public override ICommandExecutor CreateExecutor()
        {
            return new CustomCommandExecutor(this);
        }
    }
}
```
{% endcode %}

Метод Init() принимает параметр XmlNode node, который соответствует тэгу `<Command>`, описанному в xml-файле формы. Из объекта node можем получить информацию обо всех вложенных тэгах и их атрибутах. Значения, полученные из xml, сохраняются в публичные свойства, доступ к которым будет у исполняемой части через прямую ссылку на экземпляр класса CustomCommand, переданную в конструктор класса CustomCommandExecutor.

### Исполняемая часть <a href="#executable-part" id="executable-part"></a>

Пример кода исполняемой части:

{% code title="CustomCommandExecutor.cs" %}
```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using System;
using System.Data;
using System.Threading.Tasks;

namespace WorkflowEngine
{
    public class CustomCommandExecutor : AbstractCommandExecutor
    {
        private readonly CustomCommand _command;

        public CustomCommandExecutor(CustomCommand command) : base(command)
        {
            _command = command;

            /*
             * Через класс ServiceProvider можно получить дополнительные сервисы.
             * Например, объект типа ILogger для добавления записей в журнал событий
             * или объект типа IDatabaseConnection для выполнения SQL-запросов.
             */
        }

        public override async Task Execute(IParameterized parameters)
        {
            /*
             * Получение словаря из контекста параметров.
             * Этот словарь можно использовать для подстановки значений в SQL-запросы.
             */
            var parameterDictionary = parameters.ToDictionary();
                
            /*
             * Здесь должен быть код бизнес-логики: выполнение SQL-запросов и команд,
             * а так же обработка их результатов
             */
             
             /*
              * Результат выполнения команды храниться в свойстве Value.
              */
             Value = null;
        }
    }
}
```
{% endcode %}

Метод Execute принимает параметр  parameters типа IParameterized, хранящий контекст параметров, общий для всех команд. Из контекста можно получить словарь параметров, который можно использовать для подстановки значений в SQL-запросы вместо переменных.

### Как это работает <a href="#how-it-works" id="how-it-works"></a>

При старте сервер разбирает серверный xml-файл и создает экземпляр класса CustomCommand для каждого тэга `<Command>` типа CustomCommand. Когда происходит вызов команды, первым делом выполняется метод CreateExecutor из класса CustomCommand, который создает экземпляр класса CustomCommandExecutor.

## Элементы команды <a href="#command-elements" id="command-elements"></a>

### SQL-запросы <a href="#sql-queries" id="sql-queries"></a>

Пример команды с указанием текста SQL-запроса:

```xml
<Command Name="MyCustomCommand" Type="CustomCommand" Assembly="TemplateEngine">
  <SqlQuery>
    <Text>
      SELECT
        'qwerty' AS "Field0",
        123 AS "Field1",
        false AS "Field2";
    </Text>
  </SqlQuery>
</Command>
```

#### Инициируемая часть <a href="#sql-init-part" id="sql-init-part"></a>

Для получения значения обязательного тэга в методе Init необходимо использовать статический метод GetRequiredElementValue класса XmlParser:

```csharp
// public string SqlQueryText { get; private set; }

SqlQueryText = XmlParser.GetRequiredElementValue<string>(
    node, "SqlQuery/Text", Name, this);
```

Вторым параметром (string path) указывается полный путь до тэга, значение которого нужно получить. В третьем параметре (string name) указывается имя объекта, в котором происходит получение значения, в четвертом параметре (object targetObject) - класс объекта, в котором происходит получение значения. Имя объекта и его класс используются в формировании текста сообщения об ошибке. В качестве результата метод возвращает строку. Если элемент отсутствует, будет возвращено исключение типа InvalidXmlException.

Если необходимо получить значение необязательного элемента, то следует использовать метод GetElementValue. Этот метод в случае отсутствия элемента будет возвращать значение по умолчанию, переданное в параметрах метода.

#### Исполняемая часть <a href="#sql-executable-part" id="sql-executable-part"></a>

Если команда должна работать с SQL-запросами, то в конструкторе CustomCommandExecutor необходимо получить ссылки на сервисы обеспечивающие работу с базой данных. Для этого используется статический метод GetRequiredService класса ServiceProvider.

Первый сервис - объект типа IDatabaseConnection, который устанавливает соединение с базой данных и выполняет SQL-запрос:

```csharp
// private readonly IDatabaseConnection _connection;

_connection = ServiceProvider.GetRequiredService<IDatabaseConnection>();
```

Второй сервис - объект типа ISqlQueryHelper, который помогает построить текст запроса, заменив в нем переменные в круглых скобках `{}` на нужные значения из словаря параметров:

```csharp
// private readonly ISqlQueryHelper _sqlQueryHelper;

_sqlQueryHelper = ServiceProvider.GetRequiredService<ISqlQueryHelper>();
```

Для выполнения SQL-запросов в методе Execute используем метод ExecuteQueryAsync() у объекта типа IDatabaseConnection, результатом которого будет объект типа DataTable:

```csharp
var data = await _connection.ExecuteQueryAsync(
    _command.SqlQueryText, parameterDictionary
);
```

В метод передаются текст SQL-запроса и словарь параметров parameterDictionary. Словарь должен содержать значения исходного словаря parameters.ToDictionary() и дополнительные, необходимы для выполнения запроса.

Внутри метод ExecuteQueryAsync() первым делом заменит все переменные в тексте запроса, а затем, с помощью параметров из parameters.ToDictionary(), построит тексты системных запросов с установкой [параметров конфигурации](https://wfsys.gitbook.io/wt-knowledge-base/platform-wt/time_zone#configuration-parameters).

При необходимости можно в своем коде сделать замену переменных в тексте запроса, используя сервис ISqlQueryHelper и его метод GetQueryText():

```
_sqlQueryHelper.GetQueryText(_command.SqlQueryText, parameterDictionary)
```

### Команды <a href="#commands" id="commands"></a>

Пример описания кастомной команды, которая принимает имя другой команды, чтобы использовать ее результат выполнения или вызвать ее для выполнения, передав в нее свои значения:

```xml
<Command Name="MyCustomCommand" Type="CustomCommand" Assembly="TemplateEngine">
  <AnyCommand Name="AnySqlQueryCommand" />
</Command>
```

В примере рассматривается команда AnySqlQueryCommand типа SqlQueryCommand, но это может быть команда любого типа, в том числе и кастомного. Вызов команды AnySqlQueryCommand можно реализовать в xml-файле перед вызовом команды MyCustomCommand, либо перенести в исходный код кастомки.

#### Инициируемая часть <a href="#command-init-part" id="command-init-part"></a>

Для получения значения обязательного атрибута тэга в методе Init необходимо использовать статический метод GetRequiredAttributeValue класса XmlParser:

```csharp
// public string AnyCommandName { get; private set; }

AnyCommandName = XmlParser.GetRequiredAttributeValue<string>(
    node, "AnyCommand", NAME_ATTRIBUTE);
```

Вторым параметром (string path) указывается полный путь до тэга, значение атрибута которого нужно получить. Имя атрибута указывается в третьем параметре (string attribute). В данном случае используется константа `NAME_ATTRIBUTE`, описанная в базовом классе. Если элемент или его атрибут отсутствует, будет возвращено исключение типа InvalidXmlException.

Если необходимо получить значение необязательного атрибута, то следует использовать метод GetAttributeValue. Этот метод в случае отсутствия элемента или его атрибута будет возвращать значение по умолчанию, переданное в параметрах метода.

#### Исполняемая часть <a href="#command-executable-part" id="command-executable-part"></a>

При работе с командой первым делом нужно получить ее Executor:

```csharp
var executor = _command.WorkflowType.GetCommandExecutor(_command.AnyCommandName);
```

Через свойство WorkflowType нашей команды получаем текущий процесс, у которого с помощью метода GetCommandExecutor получаем Executor нужной команды по ее имени.

Результат выполнения команды храниться в свойстве Value:

```csharp
var result = (DataTable)executor.Value;
```

Так как в примере команда AnyCommandName имеет тип SqlQueryCommand, то ее результат  можно привести к типу DataTable, что облегчит его обработку.

Но, прежде чем использовать результат выполнения команды, проверяем значение свойства Value. И если там пусто, то выполняем команду, вызвав метод Execute и передав в него параметры:

```csharp
if (executor.Value == null)
{
    await executor.Execute(parameters).ConfigureAwait(false);
}
```

### Результат выполнения <a href="#result-execution" id="result-execution"></a>

Результат выполнения команды должен записываться в свойство Value, определенное в наследуемом классе AbstractCommandExecutor:

```csharp
public object Value { get; protected set; }
```

Результатом команды может быть как скалярное значение, так и список значений.

Скалярное значение напрямую пишется в свойство Value:

```csharp
Value = "Result of command execution";
```

&#x20;И доступно по умолчанию при обращении к команде по имени:

```xml
<Command Name="MyCustomCommand"/>
```

Если необходимо, чтобы команда возвращала несколько значений, то используется Dictionary для хранения пар ключ-значение:

```csharp
Value = new Dictionary<string, object>(){
    { "FirstKey", "text" },
    { "SecondKey", true }
};
```

Если обращаться к команде только по имени, то будет возвращаться весь словарь. Чтобы получить конкретное значение из словаря, используется атрибут `Parameter`, в котором указывается имя ключа:

```xml
<Command Name="MyCustomCommand" Parameter="FirstKey"/>
```

### Логирование <a href="#logging" id="logging"></a>

Для возможности добавления записей в журнал событий Windows необходимо получить сервис ILogger, для этого в конструкторе CustomCommandExecutor используем статический метод GetService класса ServiceProvider.

```csharp
// private readonly ILogger<CustomCommand> _logger;

_logger = ServiceProvider.GetService<ILogger<CustomCommand>>();
```

Чтобы записать в журнал событий сообщение об ошибке, необходимо вызвать метод LogError:

```
_logger.LogError("Текст сообщения об ошибки.");
```

Есть разные уровни сообщений для записи в журнал событий:

* LogCritical() - форматирует и записывает критическое сообщение журнала;
* LogDebug() - форматирует и записывает в журнал сообщение отладки;
* LogError() - форматирует и записывает в журнал сообщение об ошибке;
* LogInformation() - форматирует и записывает в журнал информационное сообщение;
* LogTrace() - форматирует и записывает в журнал сообщение трассировки;
* LogWarning() - форматирует и записывает в журнал сообщение с предупреждением.
