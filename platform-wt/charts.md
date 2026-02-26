# Диаграммы

## CartesianChart <a href="#cartesian_chart" id="cartesian_chart"></a>

Особенность использования [CartesianChart](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/objects/cartesian_chart) заключается в том, что объект этого типа должен быть обернут в объект типа [Panel](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/objects/panel), в котором _не должно быть_ других графических объектов. При этом панель должна иметь свойство [AutoScroll](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/objects/panel#auto_scroll) со значением True. Причина этого в том, что по высоте диаграмма будет автоматически растягиваться на всю высоту контейнера, если значение из тэга `<Height>` будет меньше или равно высоте контейнера. Если заданная высота диаграммы будет больше высоты контейнера, то диаграмма не будет сжиматься, а в панели появится вертикальная полоса прокрутки.&#x20;

Отсюда следует вторая особенность использования CartesianChart на форме: к размерам и координатам таких объектов _нельзя привязывать_ координаты и размеры других объектов.

График CartesianChart может иметь вид линейной, столбчатой или полосовой диаграммы. За это отвечает атрибут `Type` тэга  [`<SeriesCollection>`](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/objects/cartesian_chart#series_collection).  В зависимости от вида отображаемой диаграммы, будет меняться подход к заданию высоты и ширины объекта CartesianChart.

### Линейная и столбчатая диаграммы <a href="#line-and-column-charts" id="line-and-column-charts"></a>

Если создаем линейный график или столбчатый, то значения тэгов `<Height>` и `<Width>` объекта CartesianChart можно привязать к соответствующим get-проперти контейнера:

```xml
<MyObject Name="CartesianChartPanel" Type="Panel" Assembly="BaseControls">
  <Top>5</Top>
  <Left>5</Left>
  <Height>
    <Calculate>
      <Expression>{0} - {1}*2</Expression>
      <Items>
        <Item>
          <Object Name="ContentPanel">
            <Property Name="Height" />
          </Object>
        </Item>
        <Item>
          <Object Name="CartesianChartPanel">
            <Property Name="Top" />
          </Object>
        </Item>
      </Items>
    </Calculate>
  </Height>
  <Width>
    <Calculate>
      <Expression>{0} - {1}*2</Expression>
      <Items>
        <Item>
          <Object Name="ContentPanel">
            <Property Name="Width" />
          </Object>
        </Item>
        <Item>
          <Object Name="CartesianChartPanel">
            <Property Name="Left" />
          </Object>
        </Item>
      </Items>
    </Calculate>
  </Width>

  <MyObject Name="CartesianChart" Type="CartesianChart" Assembly="BaseControls">
    <Top>0</Top>
    <Left>0</Left>
    <Height>
      <Object Name="CartesianChartPanel">
        <Property Name="Height" />
      </Object>
    </Height>
    <Width>
      <Object Name="CartesianChartPanel">
        <Property Name="Width" />
      </Object>
    </Width>
    <LegendPosition>Left</LegendPosition>
    <LegendTextSize>11</LegendTextSize>
    <TooltipTextSize>13</TooltipTextSize>
    <StrokeThickness>2</StrokeThickness>
    <AxisSettings SecondaryAxisType="Text" />
    <SeriesCollection Type="Line">
      <Series Name="Income" Color="DarkCyan">
        <Title>Доходы</Title>
        <Points>
          <Object Name="IncomeArrayVariable" />
        </Points>
      </Series>
      <Series Name="Expense" Color="SoftBlue">
        <Title>Расходы</Title>
        <Points>
          <Object Name="ExpenseArrayVariable" />
        </Points>
      </Series>
    </SeriesCollection>
  </MyObject>
</MyObject>
```

<details>

<summary>IncomeArrayVariable и ExpenseArrayVariable</summary>

Объект IncomeArrayVariable является [структурой типа Table](https://wfsys.gitbook.io/workflow-forms-syntax/workflow_forms/values/structure#table), строящейся следующим образом:

```xml
<MyObject Name="IncomeArrayVariable" Type="Variable" Assembly="SimpleControls">
  <Value>
    <Structure Type="Table">
      <Row>
        <Item>Январь</Item>
        <Item>
          <DataConnection SourceDataConnection="IncomeSecondaryGetDataConnection">
            <Fields>
              <Field Name="JanSumm" />
            </Fields>
          </DataConnection>
        </Item>
      </Row>
      <Row>
        <Item>Февраль</Item>
        <Item>
          <DataConnection SourceDataConnection="IncomeSecondaryGetDataConnection">
            <Fields>
              <Field Name="FebSumm" />
            </Fields>
          </DataConnection>
        </Item>
      </Row>
      ...
    </Structure>
  </Value>
</MyObject>
```

Вторичное соединение с данными IncomeSecondaryGetDataConnection фильтрует исходный набор данных из ReportPrimaryGetDataConnectionпо полю Income:

```xml
<DataConnection Name="ReportPrimaryGetDataConnection" Type="PrimaryGetDataConnection" Assembly="DataConnections">
  <SqlQuery Name="ReportSqlQuery" Type="Select">
    <Workflow Name="Template" />
    <Fields>
      <Field Name="RowNumber" />
      <Field Name="OperationId" />
      <Field Name="OperationTitle" />
      <Field Name="Income" />
      <Field Name="JanSumm" />
      <Field Name="FebSumm" />
      <Field Name="MarSumm" />
      <Field Name="AprSumm" />
      <Field Name="MaySumm" />
      <Field Name="JunSumm" />
      <Field Name="JulSumm" />
      <Field Name="AugSumm" />
      <Field Name="SepSumm" />
      <Field Name="OctSumm" />
      <Field Name="NovSumm" />
      <Field Name="DecSumm" />
    </Fields>
    <Parameters>
      <Parameter NativeName="YearFilter">
        <Value>
          <Object Name="YearFilterVariable" />
        </Value>
      </Parameter>
    </Parameters>
  </SqlQuery>
</DataConnection>
```

Аналогичным образом строится объект ExpenseArrayVariable для второй серии данных на диаграмме.

</details>

Таким образом, диаграмма будет полностью занимать пространство родительского объекта:

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Для таких диаграмм можно _не использовать_ свойство панели AutoScroll со значением True.

Пример отображения столбчатой диаграммы:

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### Полосовая диаграмма <a href="#row-charts" id="row-charts"></a>

Если в примере выше в атрибуте  `Type` тэга  `<SeriesCollection>` просто поменять значение на  Row без корректировки высоты графика, то при открытии формы некоторые значения на оси Y будут отсутствовать, включая и те, по которым отображаются данные. На скриншоте видно, что Май и Июнь не отображаются:

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Это связано с тем, что для отображения диаграммы не хватает высоты. Если увеличим высоту окна, то диаграмма дорисует недостающие значения:

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Чтобы по основной оси (оси Y) корректно отображались все данные без изменения размера окна, для расчета высоты объекта CartesianChart нужно учитывать количество элементов в сериях, и это количество умножать на коэффициент, например, 50.

Так как обе серии Income и Expense получают данные из однотипных источников, то достаточно проверять количество элементов в одном из них:

```xml
<MyObject Name="CartesianChart" Type="CartesianChart" Assembly="BaseControls">
  <Top>0</Top>
  <Left>0</Left>
  <Height>
    <Calculate>
      <Expression>{0} * 50</Expression>
      <Items>
        <Item>
          <Array>
            <Source>
              <Object Name="IncomeArrayVariable" />
            </Source>
            <Count />
          </Array>
        </Item>
      </Items>
    </Calculate>
  </Height>
  <Width>
    <Calculate>
      <Expression>{0} - 20</Expression>
      <Items>
        <Item>
          <Object Name="CartesianChartPanel">
            <Property Name="Width" />
          </Object>
        </Item>
      </Items>
    </Calculate>
  </Width>
  <LegendPosition>Left</LegendPosition>
  <LegendTextSize>11</LegendTextSize>
  <TooltipTextSize>13</TooltipTextSize>
  <StrokeThickness>2</StrokeThickness>
  <AxisSettings SecondaryAxisType="Text" />
  <SeriesCollection Type="Row">
    <Series Name="Income" Color="DarkCyan">
      <Title>Доходы</Title>
      <Points>
        <Object Name="IncomeArrayVariable" />
      </Points>
    </Series>
    <Series Name="Expense" Color="SoftBlue">
      <Title>Расходы</Title>
      <Points>
        <Object Name="ExpenseArrayVariable" />
      </Points>
    </Series>
  </SeriesCollection>
</MyObject>
```

Если бы серии содержали разное количество элементов, то выбирали бы источник с наибольшим количеством.

Таким образом, объект CartesianChart по высоте может выходить за пределы панели, и пользователь не увидит нижнюю часть диаграммы. Поэтому панель должна иметь свойство AutoScroll со значением True, чтобы появлялась вертикальная полоса прокрутки.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Так как полоса прокрутки отображается внутри панели, то при расчете ширины графика стоит учитывать этот момент и вычитать из ширины панели некоторую константу:

```xml
<Width>
  <Calculate>
    <Expression>{0} - 20</Expression>
    <Items>
      <Item>
        <Object Name="CartesianChartPanel">
          <Property Name="Width" />
        </Object>
      </Item>
    </Items>
  </Calculate>
</Width>
```

## PieChart <a href="#pie_chart" id="pie_chart"></a>

С построением круговой диаграммы не должно возникать особых трудностей.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Код, описывающий объект PieChart в XML-файле формы, будет иметь вид:

```xml
<MyObject Name="ReportExpensePieChart" Type="PieChart" Assembly="BaseControls">
  <Top>
    <Object Name="ChartViewRadioButtonBlock">
      <Property Name="Bottom" />
    </Object>
  </Top>
  <Left>
    <Object Name="ChartViewRadioButtonBlock">
      <Property Name="Left" />
    </Object>
  </Left>
  <Height>
    <Calculate>
      <Expression>{0} - {1}</Expression>
      <Items>
        <Item>
          <Object Name="ReportDatabaseTable">
            <Property Name="Height" />
          </Object>
        </Item>
        <Item>
          <Object Name="ReportIncomePieChart">
            <Property Name="Top" />
          </Object>
        </Item>
      </Items>
    </Calculate>
  </Height>
  <Width>300</Width>
  <LegendPosition>Bottom</LegendPosition>
  <LegendTextSize>13</LegendTextSize>
  <TooltipTextSize>13</TooltipTextSize>
  <StrokeThickness>1</StrokeThickness>
  <StrokeColor>White</StrokeColor>
  <InnerRadius>50</InnerRadius>
  <Series>
    <Object Name="ExpenseVariable" />
  </Series>
  <Visible>
    <Condition Name="ChartViewEqualExpenseCondition" />
  </Visible>
</MyObject>
```

Где объект ExpenseVariable хранит матрицу данных с необходимыми колонками:

```xml
<MyObject Name="ExpenseVariable" Type="Variable" Assembly="SimpleControls">
  <Value>
    <Array>
      <Source>
        <DataConnection SourceDataConnection="ReportBudgetIsExpenseSecondaryGetDataConnection">
          <Fields>
            <Field Name="TitleForPieChart" />
            <Field Name="Summ" />
            <Field Name="Color" />
          </Fields>
        </DataConnection>
      </Source>
      <Filter Type="Greater">
        <Index Value="1" />
        <Value>0</Value>
      </Filter>
      <Select>
        <Items>
          <Item Type="Index">0</Item>
          <Item Type="Index">1</Item>
          <Item Type="Index">2</Item>
          <Item Type="Action">
            <Index>1</Index>
            <Actions>
              <DataTypeFormat Type="DecimalDataType" Format="N2" />
            </Actions>
          </Item>
        </Items>
      </Select>
      <Select>
        <Items>
          <Item Type="Index">0</Item><!-- MainAxisTextValue -->
          <Item Type="Index">1</Item><!-- MainAxisValue -->
          <Item Type="Format">{0} {3}</Item><!-- Hint -->
          <Item Type="Index">2</Item><!-- Color -->
        </Items>
      </Select>
    </Array>
  </Value>
</MyObject>
```

