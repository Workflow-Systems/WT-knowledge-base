# Condition

Для создания кастомных условий необходимо подключение штатных библиотек Conditions.dll и Common.dll.

## Условия проверки одного значения

### Описание условия

Самый простой вариант условия - условие для проверки скалярного значения:

```xml
<Condition Name="CustomCheckCondition" Type="CustomCheckCondition" Assembly="Template">
  <Items>
    <Item>Значение</Item>
  </Items>
</Condition>
```

В атрибуте `Type` указывается имя класса, описывающего кастомный Condition, а в качестве значения атрибута `Assembly` указывается имя сборки, в которой реализован этот класс.&#x20;

### Исходный код

Класс кастомного условия наследует класс **CheckCondition**:

```csharp
using System.Xml;

namespace WorkflowForms.Conditions
{
    public class CustomCheckCondition : CheckCondition
    {
        public CustomCheckCondition(IWorkflowForm form, XmlNode node)
            : base(form, node)
        {
        }

        // Метод Check будет вызываться столько раз, какова длина массива в <Item>.
        // И будет проверяться только первый элемент каждой строки.
        protected override bool Check(object value)
        {
            // Для корректного получения значения из праметра value используется статический класс ValueConverter.
            // Класс описан в Common.dll и предоставляет широкий набор методов поддерживающих различные типы данных.
            var str = ValueConverter.GetStringValue(value);

            // Здесь необходимо реализовать логику проверки переданного значения

            return true;
        }
    }
}
```

Такие условия могут обрабатывать линейные массивы:

```xml
<Condition Name="CustomAbstractMultiCondition" Type="CustomAbstractMultiCondition" Assembly="Template">
  <Items>
    <Item>
      <DataConnection SourceDataConnection="PrimaryGetDataConnection">
        <Fields>
          <Field Name="Field1" />
        </Fields>
      </DataConnection>
    </Item>
  </Items>
</Condition>
```

Также условия могут использовать вложенный тэг [`<Satisfy>`](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/conditions/comparison_condition#satisfy).

Если в тэге `<Item>` передается массив, то метод Check будет вызываться для каждого элемента отдельно.

## Условия проверки нескольких значений

### Описание условия

Вариант условия для сравнения нескольких значений:

```xml
<Condition Name="CustomComparisonCondition" Type="CustomComparisonCondition" Assembly="Template">
  <Items>
    <Item>
      <Object Name="FirstTextBox" />
    </Item>
    <Item>
      <Object Name="SecondTextBox" />
    </Item>
    <Item>Значение</Item>
  </Items>
</Condition>
```

### Исходный код

В этом случае наследуется класс **ComparisonCondition**:

```csharp
using System.Xml;

namespace WorkflowForms.Conditions
{
    public class CustomComparisonCondition : ComparisonCondition
    {
        public CustomComparisonCondition(IWorkflowForm form, XmlNode node)
            : base(form, node)
        {
        }

        public override void InternalInit(XmlNode node, IWorkflowForm form)
        {
            base.InternalInit(node, form);
        }

        // Метод CheckValue будет вызываться столько раз, какова длина массива в <Item>.
        // И будет проверяться только первый элемент каждой строки.
        protected override bool CheckValue(object[] values)
        {
            if (values.Length > 1)
            {
                // Здесь необходимо реализовать логику проверки переданного значения
            }

            return false;
        }
    }
}
```

Параметр values будет содержать массив значений, описанных в тэгах `<Item>`.

Если в тэгах `<Item>` передаются массивы, то метод CheckValue будет вызываться по количеству элементов в коротком массиве.

## Дополнительные элементы

### Описание условия

Кастомные условия могут иметь тэги для задания дополнительных данных, необходимых для проверки значений:

```xml
<Condition Name="CustomCheckCondition" Type="CustomCheckCondition" Assembly="Template">
  <Items>
    <Item>Значение</Item>
  </Items>
  <Something>123</Something>
</Condition>
```

### Исходный код

Для получения значения тэга `<Something>` используется механизм привязки данных. Такой подход позволяет получать актуальные данные в момент проверки условия, а не фиксировать значение при загрузки формы.

Исходный код класса:

```csharp
using System.Xml;
using WorkflowForms.DataBindings;

namespace WorkflowForms.Conditions
{
    public class CustomCheckCondition : CheckCondition
    {
        private const string SOMETHING_ELEMENT = "Something";

        private IDataBinding somethingDataBinding;

        public CustomCheckCondition(IWorkflowForm form, XmlNode node)
            : base(form, node)
        {
            /*
             * Для получения значения тэга <Something> используем
             * механизм привязки данных
             */
            somethingDataBinding = XmlParser.GetRequiredElementDataBinding(
                this, form, node,
                new DataBindingProvider(),
                SOMETHING_ELEMENT,
                Name, GetType());
        }

        // Метод Check будет вызываться столько раз, какова длина массива в <Item>.
        // И будет проверяться только первый элемент каждой строки.
        protected override bool Check(object value)
        {
            // Для корректного получения значения из праметра value используется статический класс ValueConverter.
            // Класс описан в Common.dll и предоставляет широкий набор методов поддерживающих различные типы данных.
            var str1 = ValueConverter.GetStringValue(value);

            // Динамически получаем актуальное значение в момент проверки условия
            var str2 = somethingDataBinding.GetStringValue();

            // Здесь необходимо реализовать логику проверки переданного значения

            return true;
        }
    }
}
```

Для получения привязки к источнику данных в тэге `<Something>` используем статический метод GetRequiredElementDataBinding().

