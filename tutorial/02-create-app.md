---
ms.openlocfilehash: 003d7f382a0c950d31bb84176feb1e83fbd5cf19
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822698"
---
<!-- markdownlint-disable MD002 MD041 -->

В этом руководстве показано, как создать простую функцию Azure, которая реализует функции триггера HTTP, вызывающие Microsoft Graph. Эти функции будут охватывать следующие сценарии:

- Реализует API для доступа к папке "Входящие" пользователя с помощью проверки подлинности " [от имени](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) ".
- Реализует API для подписки и отмены подписки на уведомления в папке "Входящие" пользователя, используя [учетные данные клиента, предоставляют](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) проверку подлинности с помощью клиента.
- Реализует веб-перехватчик для получения [уведомлений об изменениях](https://docs.microsoft.com/graph/webhooks) от Microsoft Graph и доступа к данным с помощью потоки предоставления учетных данных клиента.

Кроме того, вы создадите простое одностраничное приложение JavaScript (SPA), чтобы вызвать API, реализованные в функции Azure.

## <a name="create-azure-functions-project"></a>Создание проекта функций Azure

1. Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект. Выполните следующую команду.

    ```Shell
    func init GraphTutorial --dotnet
    ```

1. Измените текущий каталог в CLI на каталог **графтуториал** и выполните следующие команды, чтобы создать три функции в проекте.

    ```Shell
    func new --name GetMyNewestMessage --template "HTTP trigger" --language C#
    func new --name SetSubscription --template "HTTP trigger" --language C#
    func new --name Notify --template "HTTP trigger" --language C#
    ```

1. Откройте **local.settings.js** и добавьте в файл следующую команду, чтобы разрешить CORS с `http://localhost:8080` URL-адреса для тестового приложения.

    ```json
    "Host": {
      "CORS": "http://localhost:8080"
    }
    ```

1. Выполните следующую команду, чтобы запустить проект локально.

    ```Shell
    func start
    ```

1. Если все работает, вы увидите следующие выходные данные:

    ```Shell
    Http Functions:

        GetMyNewestMessage: [GET,POST] http://localhost:7071/api/GetMyNewestMessage

        Notify: [GET,POST] http://localhost:7071/api/Notify

        SetSubscription: [GET,POST] http://localhost:7071/api/SetSubscription
    ```

1. Убедитесь, что функции работают правильно, открыв браузер и просмотрев URL-адреса функций, показанные в выходных данных. В браузере должно появиться следующее сообщение: `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.` .

## <a name="create-single-page-application"></a>Создание одностраничного приложения

1. Откройте подсистему CLI в каталоге, в котором необходимо создать проект. Создайте каталог с именем **тестклиент** для хранения файлов HTML и JavaScript.

1. Создайте новый файл с именем **index.html** в каталоге **тестклиент** и добавьте следующий код.

    :::code language="html" source="../demo/TestClient/index.html" id="indexSnippet":::

    Этот параметр определяет базовую структуру приложения, в том числе панель навигации. Кроме того, добавляются следующие компоненты:

    - [Начальная](https://getbootstrap.com/) Загрузка и поддерживающая JavaScript
    - [фонтавесоме](https://fontawesome.com/)
    - [Библиотека проверки подлинности (Майкрософт) для JavaScript (MSAL.js) 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

    > [!TIP]
    > Страница содержит фавикон ( `<link rel="shortcut icon" href="g-raph.png">` ). Вы можете удалить эту строку или скачать файл **g-raph.png** из [GitHub](https://github.com/microsoftgraph/g-raph).

1. Создайте новый файл с именем **Style. CSS** в каталоге **тестклиент** и добавьте приведенный ниже код.

    :::code language="css" source="../demo/TestClient/style.css":::

1. Создайте новый файл с именем **ui.js** в каталоге **тестклиент** и добавьте следующий код.

    :::code language="javascript" source="../demo/TestClient/ui.js" id="uiJsSnippet":::

    Этот код использует JavaScript для отображения текущей страницы на основе выбранного представления.

### <a name="test-the-single-page-application"></a>Тестирование одностраничного приложения

> [!NOTE]
> В этом разделе приведены инструкции по использованию [DotNet-обслуживает](https://github.com/natemcmaster/dotnet-serve) для запуска простого тестового HTTP-сервера на компьютере для разработки. Использование этого конкретного средства не является обязательным. Вы можете использовать любой тестовый сервер, который вы предпочитаете обслуживать каталог **тестклиент** .

1. Выполните следующую команду в командной панели управления для установки **DotNet — обслуживает**.

    ```Shell
    dotnet tool install --global dotnet-serve
    ```

1. Измените текущий каталог в CLI на каталог **тестклиент** и выполните следующую команду, чтобы запустить HTTP-сервер.

    ```Shell
    dotnet serve -h "Cache-Control: no-cache, no-store, must-revalidate"
    ```

1. Откройте браузер и перейдите по адресу `http://localhost:8080`. Страница должна отображаться, но ни одна из кнопок в настоящее время не работает.

## <a name="add-nuget-packages"></a>Добавление пакетов NuGet

Прежде чем переходить, установите некоторые дополнительные пакеты NuGet, которые будут использоваться позже.

- [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions) для включения внедрения зависимостей в проект функций Azure.
- [Microsoft.Extensions.Configуратион. Усерсекретс](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) чтение конфигурации приложения из [хранилища секретов для разработки .NET](https://docs.microsoft.com/aspnet/core/security/app-secrets).
- [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) для совершения звонков в Microsoft Graph.
- [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) для проверки подлинности и управления маркерами.
- [Microsoft. IdentityModel. Protocols. опенидконнект](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocols.OpenIdConnect) для получения конфигурации OpenID для проверки маркера.
- [System. IdentityModel. tokens. JWT](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) для проверки маркеров, отправляемых в веб-API.

1. Измените текущий каталог в CLI на каталог **графтуториал** и выполните следующие команды.

    ```Shell
    dotnet add package Microsoft.Azure.Functions.Extensions --version 1.0.0
    dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.5
    dotnet add package Microsoft.Graph --version 3.8.0
    dotnet add package Microsoft.Identity.Client --version 4.15.0
    dotnet add package Microsoft.IdentityModel.Protocols.OpenIdConnect --version 6.7.1
    dotnet add package System.IdentityModel.Tokens.Jwt --version 6.7.1
    ```
