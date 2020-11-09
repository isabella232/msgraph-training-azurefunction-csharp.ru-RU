---
ms.openlocfilehash: 728138b5d298f9469a566defed6ee2e457896c0b
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822734"
---
<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы завершите реализацию функции Azure `GetMyNewestMessage` и обновите тестовый клиент, чтобы вызвать функцию.

Функция Azure использует [потоки от имени](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)"от имени". Ниже приведен базовый порядок событий этого процесса.

- В тестовом приложении используется интерактивный процесс проверки подлинности, позволяющий пользователю выполнить вход и предоставить согласие. Возвращается маркер, областью действия которого является функция Azure. Маркер **не содержит областей** Microsoft Graph.
- Тестовое приложение вызывает функцию Azure, отправляя маркер доступа в `Authorization` заголовке.
- Функция Azure проверяет маркер, затем обменивается данными маркером для второго маркера доступа, содержащего области Microsoft Graph.
- Функция Azure вызывает Microsoft Graph от имени пользователя с помощью второго маркера доступа.

> [!IMPORTANT]
> Чтобы не хранить идентификатор и секрет приложения в источнике, вы будете использовать [Диспетчер секрета .NET](https://docs.microsoft.com/aspnet/core/security/app-secrets) для хранения этих значений. Диспетчер секретности предназначен только для целей разработки, рабочие приложения должны использовать доверенный диспетчер секретов для хранения секретов.

## <a name="add-authentication-to-the-single-page-application"></a>Добавление проверки подлинности для приложения с одной страницей

Для начала добавьте проверку подлинности в SPA. Это позволит приложению получить маркер доступа, предоставляя доступ для вызова функции Azure. Так как это SPA, он будет использовать [код проверки подлинности для пкце](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

1. Создайте новый файл в каталоге **тестклиент** с именем **config.js** и добавьте следующий код.

    :::code language="javascript" source="../demo/TestClient/config.example.js" id="msalConfigSnippet":::

    Замените на `YOUR_TEST_APP_APP_ID_HERE` идентификатор приложения, созданный на портале Azure для **тестового приложения функции Graph Azure**. Замените `YOUR_TENANT_ID_HERE` ЗНАЧЕНИЕМ **идентификатора Directory (клиент)** , скопированным с портала Azure. Замените `YOUR_AZURE_FUNCTION_APP_ID_HERE` идентификатором приложения для **функции Graph Graph**.

    > [!IMPORTANT]
    > Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить файл **config.js** из системы управления версиями, чтобы избежать непреднамеренного утечки идентификаторов и идентификатора клиента.

1. Создайте новый файл в каталоге **тестклиент** с именем **auth.js** и добавьте следующий код.

    :::code language="javascript" source="../demo/TestClient/auth.js" id="signInSignOutSnippet":::

    Рассмотрите, что делает этот код.

    - Он инициализирует объект `PublicClientApplication` с использованием значений, хранящихся в **config.js**.
    - Используется `loginPopup` для подписания пользователя в, используя область разрешений для функции Azure.
    - Он хранит имя пользователя пользователя в сеансе.

    > [!IMPORTANT]
    > Так как приложение использует приложение `loginPopup` , может потребоваться изменить блокирование всплывающих окон в браузере, чтобы разрешить всплывающие окна `http://localhost:8080` .

1. Обновите страницу и войдите в нее. Страница должна обновляться с именем пользователя, что означает, что вход выполнен успешно.

> [!TIP]
> Вы можете проанализировать маркер доступа [https://jwt.ms](https://jwt.ms) и подтвердить, что `aud` Заявка является идентификатором приложения для функции Azure, и что `scp` утверждение содержит область разрешений функции Azure, а не Microsoft Graph.

## <a name="add-authentication-to-the-azure-function"></a>Добавление проверки подлинности в функцию Azure

В этом разделе для `GetMyNewestMessage` получения маркера доступа, совместимого с Microsoft Graph, вы настроите потоки от имени пользователя в функции Azure.

1. Инициализируйте хранилище секрета .NET Development, открыв его в каталоге, содержащем **графтуториал. csproj** , и выполнив следующую команду.

    ```Shell
    dotnet user-secrets init
    ```

1. Добавьте идентификатор приложения, секретный код и идентификатор клиента в Секретное хранилище с помощью следующих команд. Замените `YOUR_API_FUNCTION_APP_ID_HERE` идентификатором приложения для **функции Graph Graph**. Замените на `YOUR_API_FUNCTION_APP_SECRET_HERE` секрет приложения, созданный на портале Azure, для **функции Graph**. Замените `YOUR_TENANT_ID_HERE` ЗНАЧЕНИЕМ **идентификатора Directory (клиент)** , скопированным с портала Azure.

    ```Shell
    dotnet user-secrets set apiFunctionId "YOUR_API_FUNCTION_APP_ID_HERE"
    dotnet user-secrets set apiFunctionSecret "YOUR_API_FUNCTION_APP_SECRET_HERE"
    dotnet user-secrets set tenantId "YOUR_TENANT_ID_HERE"
    ```

### <a name="process-the-incoming-bearer-token"></a>Обработка токена входящего носителя

В этом разделе описывается, как реализовать класс для проверки и обработки токена носителя, отправленного из SPA в функцию Azure.

1. Создайте новый каталог в каталоге **графтуториал** с именем **authentication**.

1. Создайте новый файл с именем **TokenValidationResult.CS** в папке **./графтуториал/аусентикатион** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/TokenValidationResult.cs" id="TokenValidationResultSnippet":::

1. Создайте новый файл с именем **TokenValidation.CS** в папке **./графтуториал/аусентикатион** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/TokenValidation.cs" id="TokenValidationSnippet":::

Рассмотрите, что делает этот код.

- Это гарантирует, что в `Authorization` заголовке присутствует токен носителя.
- Он проверяет подпись и поставщика из конфигурации опубликованной OpenID в Azure.
- Она проверяет соответствие аудитории ( `aud` утверждения) идентификатору приложения функции Azure.
- Он выполняет синтаксический анализ маркера и создает идентификатор учетной записи MSAL, который будет необходим для использования кэширования маркеров.

### <a name="create-an-on-behalf-of-authentication-provider"></a>Создание поставщика проверки подлинности "от имени"

1. Создайте новый файл в каталоге **проверки подлинности** с именем **OnBehalfOfAuthProvider.CS** и добавьте в этот файл следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/OnBehalfOfAuthProvider.cs" id="AuthProviderSnippet":::

Уделите несколько минут, чтобы определить, что делает код в **OnBehalfOfAuthProvider.CS** .

- В `GetAccessToken` функции она сначала пытается получить маркер пользователя из кэша маркеров с помощью метода `AcquireTokenSilent` . В противном случае он использует маркер носителя, отправленный тестовым приложением, в функцию Azure для создания утверждения пользователя. Затем он использует утверждение пользователя, чтобы получить маркер, совместимый с графиком, с помощью `AcquireTokenOnBehalfOf` .
- Он реализует `Microsoft.Graph.IAuthenticationProvider` интерфейс, позволяя передавать этот класс в конструктор `GraphServiceClient` для проверки подлинности исходящих запросов.

### <a name="implement-a-graph-client-service"></a>Реализация клиентской службы Graph

В этом разделе описывается реализация службы, которая может быть зарегистрирована для [внедрения зависимостей](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection). Служба будет использоваться для получения клиента Graph, прошедшего проверку подлинности.

1. Создайте новый каталог в каталоге **графтуториал** с именем **Services**.

1. Создайте новый файл в каталоге **Services** с именем **IGraphClientService.CS** и добавьте в этот файл следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Services/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. Создайте новый файл в каталоге **Services** с именем **GraphClientService.CS** и добавьте в этот файл следующий код.

    ```csharp
    using GraphTutorial.Authentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.Identity.Client;
    using Microsoft.Graph;

    namespace GraphTutorial.Services
    {
        // Service added via dependency injection
        // Used to get an authenticated Graph client
        public class GraphClientService : IGraphClientService
        {
        }
    }
    ```

1. Добавьте в класс указанные ниже свойства `GraphClientService` .

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="UserGraphClientMembers":::

1. Добавьте в класс следующие функции `GraphClientService` .

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="UserGraphClientFunctions":::

1. Добавьте реализацию заполнителя для `GetAppGraphClient` функции. Это будет реализовано в последующих разделах.

    ```csharp
    public GraphServiceClient GetAppGraphClient()
    {
        throw new System.NotImplementedException();
    }
    ```

    `GetUserGraphClient`Функция выполняет результаты проверки маркера и создает для пользователя проверку подлинности `GraphServiceClient` .

1. Создайте новый файл в каталоге **графтуториал** с именем **Startup.CS** и добавьте в этот файл следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="StartupSnippet":::

    Этот код включает [внедрение зависимостей](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) в функции Azure, предоставляя `IConfiguration` объект и `GraphClientService` службу.

### <a name="implement-getmynewestmessage-function"></a>Реализация функции Жетминевестмессаже

1. Откройте **/графтуториал/жетминевестмессаже.КС** и замените все содержимое следующим.

    :::code language="csharp" source="../demo/GraphTutorial/GetMyNewestMessage.cs" id="GetMyNewestMessageSnippet":::

#### <a name="review-the-code-in-getmynewestmessagecs"></a>Просмотр кода в GetMyNewestMessage.cs

Уделите несколько минут, чтобы определить, что делает код в **GetMyNewestMessage.CS** .

- В конструкторе он сохраняет объект, `IConfiguration` переданный с помощью внедрения зависимостей.
- В `Run` функции она выполняет следующие действия:
  - Проверяет наличие обязательных значений конфигурации в `IConfiguration` объекте.
  - Проверяет маркер носителя и возвращает `401` код состояния, если маркер является недопустимым.
  - Возвращает клиент графа `GraphClientService` для пользователя, который внес этот запрос.
  - Использует пакет SDK Microsoft Graph, чтобы получить Последнее сообщение из папки "Входящие" пользователя и возвратить его в ответе в теле JSON.

## <a name="call-the-azure-function-from-the-test-app"></a>Вызов функции Azure из тестового приложения

1. Откройте **auth.js** и добавьте указанную ниже функцию, чтобы получить маркер доступа.

    :::code language="javascript" source="../demo/TestClient/auth.js" id="getTokenSnippet":::

    Рассмотрите, что делает этот код.

    - Сначала он пытается получить маркер доступа без вмешательства пользователя. Так как пользователь уже должен войти в систему, MSAL должен иметь маркеры для пользователя в его кэше.
    - Если произойдет сбой с ошибкой, указывающей на то, что пользователь должен взаимодействовать, он пытается интерактивно получить маркер.

1. Создайте новый файл в каталоге **тестклиент** с именем **azurefunctions.js** и добавьте следующий код.

    :::code language="javascript" source="../demo/TestClient/azurefunctions.js" id="getLatestMessageSnippet":::

1. Измените текущий каталог в командной системе CLI на каталог **./графтуториал** и выполните следующую команду, чтобы запустить функцию Azure локально.

    ```Shell
    func start
    ```

1. Если вы еще не обслуживаете SPA, откройте второе окно CLI и измените текущий каталог на каталог **./тестклиент** . Выполните следующую команду, чтобы запустить тестовое приложение.

    ```Shell
    dotnet serve -h "Cache-Control: no-cache, no-store, must-revalidate"
    ```

1. Откройте браузер и перейдите по адресу `http://localhost:8080`. Выполните вход и выберите последний элемент навигации по **сообщению** . Приложение отображает сведения о последних сообщениях в папке "Входящие" пользователя.
