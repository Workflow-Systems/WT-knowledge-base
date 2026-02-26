# Patterns

## Подключение паттернов к проекту <a href="#add-patterns-to-project" id="add-patterns-to-project"></a>

Чтобы подключить паттерны к конкретному проекту, необходимо открыть свойства этого проекта пункт меню **Project -> Properties**:

<figure><img src="https://wfsys.gitbook.io/~gitbook/image?url=https%3A%2F%2F2982419670-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fw42gvnbAkwAz4lPHWZM0%252Fuploads%252F2BxW15KqSLUtDzUA80gD%252Fimage.png%3Falt%3Dmedia%26token%3D4cce6400-bc41-487b-8677-d9f4fc707ef9&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=3bb68298&#x26;sv=1" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Свойства проекта можно открыть через контекстное меню, кликнув правой кнопкой мыши по имени проекта в окне Project Explorer, и выбрав пункт Properties.
{% endhint %}

В открывшемся окне свойств проекта в левой части перейти к секции **Patterns**.

<figure><img src="https://wfsys.gitbook.io/~gitbook/image?url=https%3A%2F%2F3019442075-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-M_eBlWEU4C3o2GVEAAr%252Fuploads%252FnOSNwn9CJIvWTfwdiLdV%252Fimage.png%3Falt%3Dmedia%26token%3Ddfbd58a1-4bf5-44f4-8456-fae04de297eb&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=aab02a7d&#x26;sv=1" alt=""><figcaption></figcaption></figure>

В правой части окна в поле **Project patterns path** указать путь до папки с паттернами.

Чтобы Eclipse корректно подтянул изменения паттернов, необходимо перезапустить редактор или воспользоваться кнопкой **Reload patterns**, расположенной на панели инструментов:

<figure><img src="https://wfsys.gitbook.io/~gitbook/image?url=https%3A%2F%2F3019442075-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-M_eBlWEU4C3o2GVEAAr%252Fuploads%252FSkxbgkBDAHFGDLPVFzXV%252Fimage.png%3Falt%3Dmedia%26token%3D4007ba25-c1f6-4f3b-a5be-58e62604b0a9&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=b18a0d80&#x26;sv=1" alt=""><figcaption></figcaption></figure>

## Применение паттерна <a href="#apply-pattern" id="apply-pattern"></a>

Для выбора паттерна используется пункт меню **File** -> **New** -> **Apply Workflow Pattern**.

<figure><img src="https://wfsys.gitbook.io/~gitbook/image?url=https%3A%2F%2F3019442075-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-M_eBlWEU4C3o2GVEAAr%252Fuploads%252FQZwi8pN76rftw40j0G7k%252Fimage.png%3Falt%3Dmedia%26token%3D6e9bdf20-9cbe-432e-ae07-9bf09b82279e&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=d3c3b216&#x26;sv=1" alt=""><figcaption></figcaption></figure>

В открывшемся диалоговом окне Workflow Editor отображается список шаблонов форм:

<figure><img src="https://wfsys.gitbook.io/~gitbook/image?url=https%3A%2F%2F3019442075-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-M_eBlWEU4C3o2GVEAAr%252Fuploads%252Ft569YTmoIZaIHgD6sd9t%252Fimage.png%3Falt%3Dmedia%26token%3Dee17005c-44f7-4ed1-882b-c6fc236ecf55&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=78f0f8b2&#x26;sv=1" alt=""><figcaption></figcaption></figure>

При выборе нужного шаблона откроется окно настроек:

<figure><img src="https://wfsys.gitbook.io/~gitbook/image?url=https%3A%2F%2F3019442075-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-M_eBlWEU4C3o2GVEAAr%252Fuploads%252F7SlC0IfBYn0ukzGcU5iB%252Fimage.png%3Falt%3Dmedia%26token%3D7729f6f5-3b46-4ffd-b2a8-76a2c8f17cb2&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=ed54e11&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Например, настройки шаблона **Empty Form with Footer** содержат поля:

* **Имя формы**: это постфикс для имени файла. Например, если процесс имеет название Template, а в поле указано CityEdit, то имя файла будет TemplateCityEdit.xml;
* **Заголовок формы**: текст, который будет отображаться в хэдере формы;
* **Имя кнопки в футере**: имя объекта типа Button, которое будет использоваться на форме при обращении к объекту;
* **Текст кнопки в футере**: видимый текст на кнопке;
* **Иконка на кнопке в футере**: путь до файла с графическим содержанием, которое будет отображаться на кнопке. Путь задается  относительно папки \Forms\Images\24x24.

Применив настройки, кликнув по кнопке Finish, редактор создаст форму и сразу же ее откроет. В окне Project Explorer в ветке проекта появится созданная форма:

<figure><img src="https://wfsys.gitbook.io/~gitbook/image?url=https%3A%2F%2F3019442075-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252F-M_eBlWEU4C3o2GVEAAr%252Fuploads%252Fmq2poul5Bzc3zpI56Qkg%252Fimage.png%3Falt%3Dmedia%26token%3Dcdbc65b0-8e72-4a0b-ad6b-59ef34a043fd&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=6459826e&#x26;sv=1" alt=""><figcaption></figcaption></figure>

## Редактирование паттерна <a href="#pattern-editing" id="pattern-editing"></a>

Раздел в разработке =)
