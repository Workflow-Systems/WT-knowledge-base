# Языки в кастомках

В папке \Template\Projects\1. Template\Forms\Language можно найти файлы строковых ресурсов:

* ru.wxlf - для работы форм со строковыми ресурсами на русском языке;
* object-ru.wxlf - для работы кастомок со строковыми ресурсами на русском языке;
* en.wxlf - для работы форм со строковыми ресурсами на английском языке;
* object-en.wxlf - для работы кастомок со строковыми ресурсами на английском языке.

Файлы object-ru.wxlf и object-en.wxlf используются для задания строковых ресурсов для кастомных элементов (команд, объектов и т.д.).&#x20;



По договоренности внутри компании: файлы ru.wxlf и en.wxlf используем для задания строковых ресурсов для форм, а файлы object-ru.wxlf и object-en.wxlf - для кастомных элементов (команд, объектов и т.д.), которые рассмотрим в одном из следующих уроков. На самом деле платформа считывает из папки Forms\Language все файлы одной локали и записывает их в один словарь. И файлов по одной локали может быть сколько угодно - отбор происходит по имени файла, оно должно оканчиваться именем локали (ru/en).



```xml
<?xml version="1.0"?>
<Wxliff xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Context>
    <Text Id="TextId">
      <Source>Текст</Source>
      <Target>Text</Target>
    </Text>
  </Context>
</Wxliff>
```

Чтобы получать значения из этих файлов, необходимо обращаться к методу GetLocalizedText(string) формы, который по ключу возвращает нужную строку из файла object-ru.wxlf или object-en.wxlf относительно текущей локали приложения.

Создадим вспомогательный класс, который будет хранить контекст формы для обращения к методу получения строковых ресурсов:

{% code title="StringManager.cs" %}
```csharp
using WorkflowForms;

namespace Template
{
    public class StringManager
    {
        private readonly IWorkflowForm _form;

        public StringManager(IWorkflowForm form)
        {
            _form = form;
        }

        public string GetString(string id)
        {
            return _form.GetLocalizedText(id);
        }
    }
}
```
{% endcode %}

В методе InternalInit(XmlNode, IWorkflowForm) кастомных классов необходимо создавать экземпляр класса StringManager и сохранять в глобальную переменную:

```csharp
_manager = new Template.StringManager(form);
```

Затем через метод GetString(string) по ключу получать нужную строку:

```csharp
string str = _manager.GetString("TextId");
```
