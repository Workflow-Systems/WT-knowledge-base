# DataBinding

РАЗДЕЛ НАХОДИТСЯЫ В РАЗРАБОТКЕ





В классе DataBinding реализованы перегрузки методов для получения скалярных значений:

* object GetScalarValue();
* string GetStringValue(), string GetStringValue(string ifNullValue) и др.;
* long GetIntValue(), long GetIntValue(long ifNullValue) и др.;
* double GetDoubleValue(), double GetDoubleValue(double ifNullValue) и др.;
* decimal GetDecimalValue(), decimal GetDecimalValue(decimal ifNullValue) и др.;
* bool GetBoolValue(), bool GetBoolValue(bool ifNullValue) и др.;
* DateTime GetDateTimeValue(), DateTime GetDateTimeValue(DateTime ifNullValue) и др.

А также методы для получения массивов:

* object\[] GetArrayValue();
* string\[] GetStringArrayValue();
* int\[] GetIntArrayValue();
* double\[] GetDoubleArrayValue();
* decimal\[] GetDecimalArrayValue();
* bool\[] GetBoolArrayValue();
* DateTime\[] GetDateTimeArrayValue().

И получения матрицы объектов:

* object\[]\[] GetMatrixValue();
