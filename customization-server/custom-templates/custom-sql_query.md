# SqlQuery

Кастомный SqlQuery полезен в тех случаях, когда необходима предварительная обработка данных, которую проще реализовать на C#, и не нужно передавать клиенту лишние данные, используемые в обработке.

Для создания кастомных SqlQuery достаточно подключения основной библиотеки WorkflowEngine.dll.

### Описание запроса <a href="#request-description" id="request-description"></a>

Пример синтаксиса кастомного запроса

```xml
<SqlQuery Name="CustomSqlQuery" Type="CustomSqlQuery" Assembly="TemplateEngine">
  <Text>
    SELECT
      null AS "Field0",
      null AS "Field1",
      null AS "Field2"
    LIMIT 0;
  </Text>
  <MySqlQuery>
    <Text>
      SELECT
        city.city_id AS "CityId",
        city.title AS "Title",
        city.archive AS "Archive"
      FROM
        template.city
      ORDER BY city.title;
    </Text>
  </MySqlQuery>
</SqlQuery>
```

В тэге `<Text>` можно указать запрос, который будет описывать поля ответа, доступные на форме, и не будет содержать записей. Такая договоренность позволяет понять структуру ответа без необходимости открывать исходный код элемента.

Тэг `<MySqlQuery>` является вспомогательным для получения данных, которые необходимо предварительно обработать. Таких тэгов может быть несколько - все зависит от задачи.

### Обращение к запросу <a href="#request-sqlquery" id="request-sqlquery"></a>

Обращение к кастомному SqlQuery на форме ничем не отличается от обращения к платформенным SqlQuery:

```xml
<DataConnection Name="CustomPrimaryGetDataConnection" Type="PrimaryGetDataConnection" Assembly="DataConnections">
  <SqlQuery Name="CustomSqlQuery" Type="Select">
    <Workflow Name="Template" />
    <Fields>
      <Field Name="Field0" />
      <Field Name="Field1" />
      <Field Name="Field2" />
    </Fields>
  </SqlQuery>
</DataConnection>
```

Так же указываем поля таблицы, которую сервер возвращает клиентской части.&#x20;

### Исходный код

{% code title="CustomSqlQuery.cs" %}
```csharp
using System;
using System.Data;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Common;

namespace WorkflowEngine
{
    public class CustomSqlQuery : AbstractSqlQuery
    {
        private const string TEXT_ELEMENT = "Text";
        private const string MY_SQL_QUERY = "MySqlQuery";

        private string _mySqlQueryText;

        private readonly ISqlQueryHelper _sqlQueryHelper;

        public CustomSqlQuery(IServiceProvider serviceProvider) : base(serviceProvider)
        {
            // Объект для замены переменных в тексте запроса
            _sqlQueryHelper = serviceProvider.GetService<ISqlQueryHelper>();
        }

        public override void Init(System.Xml.XmlNode node)
        {
            // В методе базового класса разбирается тэг <Text>, значение которого можно получить через переменную sqlQueryText
            base.Init(node);

            // Получаем значение из тэга <Text>, вложенного в тэг <MySqlQuery>
            _mySqlQueryText = XmlParser.GetRequiredElementValue<string>(
                node, $"{MY_SQL_QUERY}/{TEXT_ELEMENT}", Name, this);
        }

        public async override Task<DataTable> Execute(IDatabaseConnection connection, Dictionary<string, object> parameters)
        {
            // Выполнение текста запроса из sqlQueryText, предварительно заменив переменный в тексте на значения параметров
            var result = await connection.ExecuteQueryAsync(GetQueryText(parameters)).ConfigureAwait(false);

            // Выполнение текста дополнительного запрос, предварительно заменив переменный в тексте на значения параметров
            var myTable = await connection.ExecuteQueryAsync(_sqlQueryHelper.GetQueryText(_mySqlQueryText, parameters));

            // Здесь будет код по обработке данных из запросов и формированию итоговой таблицы
            /*
              foreach (DataRow dataRow in myTable.Rows) {
                var newRow = result.NewRow();

                newRow["Field0"] = ...;
                newRow["Field1"] = ...;
                newRow["Field2"] = ...;

                result.Rows.Add(newRow);
            }
            */

            return result;
        }
    }
}
```
{% endcode %}

В конструкторе CustomSqlQuery используется метод GetService объекта типа ServiceProvider для  получения объекта типа ISqlQueryHelper, который помогает построить текст запроса, заменив в нем переменные в круглых скобках `{}` на нужные значения:

```csharp
_sqlQueryHelper = serviceProvider.GetService<ISqlQueryHelper>();
```

В методе Init описывается парсинг xml-кода, для получение данных из серверного xml-файла.  Статический метод [GetRequiredElementValue](https://wfsys.gitbook.io/workflow-engine-doc/xmlparser/methods/get_required_element_value#get_required_element_value) класса [XmlParser](https://wfsys.gitbook.io/workflow-engine-doc/xmlparser) используется для получения значения обязательного тэга, путь до которого указывается в параметре string path. В качестве результата метод вернет строку. Если элемент отсутствует, будет возвращено исключение типа InvalidXmlException. Если необходимо получить значение необязательного элемента, то следует использовать метод [GetElementValue](https://wfsys.gitbook.io/workflow-engine-doc/xmlparser/methods/get_element_value). Этот метод в случае отсутствия элемента будет возвращать значение по умолчанию, переданное в параметрах метода.

В методе Execute описывается логика кастомного запроса, прописываются команды на выполнение запросов к базе данных и формируется объект типа DataTable, который будет отправляться в качестве ответа на клиентскую часть.

Для выполнения SQL-запросов используется метод ExecuteQueryAsync у объекта типа IDatabaseConnection, результатом которого будет объект типа DataTable.
