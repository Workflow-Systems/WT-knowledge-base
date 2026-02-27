# Export to PDF

Для выгрузки в PDF используются команды [ExportToPdfCommand](https://wfsys.gitbook.io/workflow-engine-syntax/workflow_engine/commands/export_to_pdf_command) на сервере и [ExportToPdfCommand](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/commands/export_to_pdf_command) на клиентской части. Обе команды работают только с docx-файлами.

Для формирования pdf-файла на клиенте используется одно из приложений Microsoft Office Word или Libre Office, которые должны быть установлены на компьютере, или библиотека Open XML (не требует дополнительных пакетов или библиотек). На стороне сервера - приложение Libre Office или библиотека Open XML.

{% hint style="info" %}
Для Microsoft Office Word должен стоять плагин Save As PDF, если приложение по умолчанию не поддерживает сохранение в pdf.
{% endhint %}

Для определения способа выгрузки используется тэг `<ExportBy>`.

На клиентской части тэг [`<ExportBy>`](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/commands/export_to_pdf_command#export_by) имеет вид:

```xml
<ExportBy>
  <Type></Type>
  <LibreOfficePath></LibreOfficePath>
</ExportBy>
```

На сервере тэг [`<ExportBy>`](https://wfsys.gitbook.io/workflow-engine-syntax/workflow_engine/commands/export_to_pdf_command#export_by) имеет вид:

```xml
<ExportBy Type="">
  <LibreOfficePathSqlQuery>
    <Text>SQL-query</Text>
  </LibreOfficePathSqlQuery>
</ExportBy>
```

При формировании pdf-файла через Microsoft Office Word или Libre Office не требуется специально подготавливать файл шаблона. А для выгрузки через OpenXml необходимо придерживаться некоторых правил. Это связано с ограничениями в доступе для OpenXml к стилям и настройкам вордовского файла, и дефолтным значениям, а так же с логикой формирования pdf-файла, которая заложена в библиотеку QuestPDF.

{% hint style="warning" %}
Если есть возможность, то лучше использовать Microsoft Office Word или Libre Office, чтобы формировать качественные и полноценные pdf-документы. Вариант с OpenXml использовать в исключительных случаях.
{% endhint %}

Если для экспорта используется вариант OpenXml, то исходный шаблон должен быть максимально простым. Так как в основе этого способа лежит бесплатная библиотека QuestPDF, которая рассчитана на создание простых PDF-файлов, и в ней нет возможностей поддерживать сложные стили и конструкции.

Основной момент: _все содержимое файла (в колонтитулах и в теле страницы) должно быть разбито по ячейкам таблиц_ без отрисовки границ ячеек. Это ограничение исходит из невозможности работать с колонками на странице, т.к. Word создает скрытый раздел, в котором параграфы динамически разбиваются по колонкам. Такое формирование колонок не позволяет знать, в какой конкретно колонке находиться абзац. В то время как используемая для PDF библиотека QuestPDF для  формирования колонок использует строгую структуру строк и колонок.

Увы, картинки пока не поддерживаются в шаблоне.
