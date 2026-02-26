# Создание и настройка проекта

## Настройка Visual Studio <a href="#setting-up-visual-studio" id="setting-up-visual-studio"></a>

Запустите Visual Studio Installer.&#x20;

<figure><img src="../.gitbook/assets/01.webp" alt=""><figcaption></figcaption></figure>

По кнопке "Изменить" откроется окно установки компонент среды разработки:

<figure><img src="../.gitbook/assets/02 (3).png" alt=""><figcaption></figcaption></figure>

На вкладке "Рабочие нагрузки" поставьте галочку на компоненте **ASP.NET и разработка веб-приложений** и нажмите кнопку "Изменить".

## Создание проекта <a href="#create-new-project" id="create-new-project"></a>

В папке **\Template\Projects\1. Template** создадим папку **Engine**, в которой будут храниться исходники кастомных объектов для серверной части.

Запустим Visual Studio 2022.

На начальной странице выберем пункт **Create a new project** (Создать проект):

<figure><img src="../.gitbook/assets/03.png" alt=""><figcaption></figcaption></figure>

Если среда разработки Visual Studio уже открыта, то проект можно создать, выбрав пункт **File -> New -> Project...** в строке меню. А также нажав кнопку **New Project** на панели инструментов, или нажав комбинацию клавиш **Ctrl+Shift+N**.

![](../.gitbook/assets/00.png)

На странице **Create a new project** введите в поле поиска **library**.  Так как платформа написана на платформе **.NET Core**, то нам необходимо выбрать соответствующий тип приложения:

<figure><img src="../.gitbook/assets/05 (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Если вы не видите шаблоны .NET, вероятно, у вас не установлена требуемая рабочая нагрузка. В сообщении **Not finding what you're looking for?** (Не удается найти то, что ищете?) выберите ссылку **Install more tools and features** (Установка других средств и компонентов). Откроется  Visual Studio Installer. Убедитесь, что у вас установлена рабочая нагрузка **ASP.NET и разработка веб-приложений**.
{% endhint %}

В диалоговом окне **Configure your new project** (Настройка нового проекта) доступны параметры, позволяющие присвоить имя проекту (и решению), выбрать расположение на диск&#x435;**:**

<figure><img src="../.gitbook/assets/03 (1).png" alt=""><figcaption></figcaption></figure>

В поле **Project name** укажем имя нашего проекта - **Template**. В поле **Location** - ранее созданную папку Objects.

Галочку **Place solution and project in the same directory** можно снять.

В диалоговом окне **Additional information** (Дополнительные сведения) содержится параметр для выбора версии платформы:

<figure><img src="../.gitbook/assets/07 (1).png" alt=""><figcaption></figcaption></figure>

Оставим здесь **.NET Core 3.1 (Out of support)** и нажмем на кнопку Create.

## Сборка проекта <a href="#building-project" id="building-project"></a>

Откроем свойства проекта, вызвав контекстное меню и выбрав пункт **Properties**. Или нажав комбинацию клавиш **Alt+Enter**.

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

На вкладке **Build->Events** необходимо прописать команду, которая будет выполняться после сборки решения:

<figure><img src="../.gitbook/assets/04.webp" alt=""><figcaption></figcaption></figure>

В поле **Post-build event command line** пропишем команду копирования собранного dll-файла из папки проекта в папку, в которую установили серверную часть:

```
copy "$(ProjectDir)$(OutDir)$(TargetName).dll" "D:\WorkflowEngine\Template"
```

По умолчанию Visual Studio собирает проект в папку:

\Template\Projects\1. Template\Engine\TemplateEngine\TemplateEngine\bin\Debug\netcoreapp3.1

Давайте пересоберем проект, для этого в контекстном меню выберем пункт **Build** или **Rebuild**:

<figure><img src="../.gitbook/assets/05.png" alt=""><figcaption></figcaption></figure>

Проверьте, что файл TemplateEngine.dll скопировался в папку с установленной серверной частью.

## Отладка проекта <a href="#debug" id="debug"></a>

Чтобы запускать приложение напрямую из Visual Studio, сделаем настройки режима отладки. Для этого откроем свойства проекта и перейдем на вкладку **Debug**:

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

Кликнем по тексту **Open debug launch profiles UI**. В открывшемся окне профилей запуска кликнем по кнопке **Create a new profile**. В меню выберем пункт **Executable**:

<figure><img src="../.gitbook/assets/13.webp" alt=""><figcaption></figcaption></figure>

Новый профиль сразу переименуем, кликнув по кнопке **Rename selected profile**:

<figure><img src="../.gitbook/assets/14.webp" alt=""><figcaption></figcaption></figure>

В профиле укажем следующие настройки:

<figure><img src="../.gitbook/assets/06.png" alt=""><figcaption></figcaption></figure>

В поле **Executable** прописываем путь до exe-файла самого web-приложения, размещенного в папке развернутого сервера. В поле **Command line argument** прописываем `--console` аналогично аргументу из файла \_start.bat, который используем для запуска серверной части. А в поле **Working directory** укажем путь до папки, в которой развернут сервер учебного проекта.

Теперь мы можем запустить приложение, нажав клавишу **F5** или кнопку на панели инструментов:

![](../.gitbook/assets/07.png)

Перед запуском проекта не забудьте остановить серверную часть приложения, если она у вас запущена.

В проекте появился новый файл **launchSettings.json**, в котором будут храниться настройки профилей запуска приложения:

<figure><img src="../.gitbook/assets/08.png" alt=""><figcaption></figcaption></figure>
