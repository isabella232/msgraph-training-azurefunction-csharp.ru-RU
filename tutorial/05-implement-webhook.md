---
ms.openlocfilehash: f55eecd4d157c945d0e95870d4e481653b26c9ec
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822659"
---
<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы завершите реализацию функций Azure `SetSubscription` и `Notify` Обновите тестовое приложение, чтобы подписаться на изменения в папке "Входящие" и отказаться от них.

- `SetSubscription`Функция будет действовать как API, позволяя тестовому приложению создавать или удалять [подписку](https://docs.microsoft.com/graph/webhooks) на изменения в папке "Входящие" пользователя.
- `Notify`Функция будет действовать в качестве веб-перехватчика, который получает уведомления об изменениях, создаваемые подпиской.

Обе функции будут использовать [потоки предоставления учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) для получения маркера, доступного только для приложений, для вызова Microsoft Graph. Так как администратор предоставил согласие администратора на требуемые области разрешений, для получения маркера не потребуется вмешательство пользователя.

## <a name="add-client-credentials-authentication-to-the-azure-functions-project"></a>Добавление учетных данных клиента проверка подлинности в проект функций Azure

В этом разделе показано, как реализовать потоки учетных данных клиента в проекте функций Azure, чтобы получить маркер доступа, совместимый с Microsoft Graph.

1. Откройте подсистему CLI в каталоге, содержащем **графтуториал. csproj**.

1. Добавьте идентификатор и секрет приложения веб-перехватчика в Секретное хранилище с помощью следующих команд. Замените `YOUR_WEBHOOK_APP_ID_HERE` идентификатором приложения для веб- **перехватчика функции Graph Azure**. Замените на `YOUR_WEBHOOK_APP_SECRET_HERE` секрет приложения, созданный на портале Azure, для **веб-перехватчика функции Graph Azure**.

    ```Shell
    dotnet user-secrets set webHookId "YOUR_WEBHOOK_APP_ID_HERE"
    dotnet user-secrets set webHookSecret "YOUR_WEBHOOK_APP_SECRET_HERE"
    ```

### <a name="create-a-client-credentials-authentication-provider"></a>Создание поставщика проверки подлинности для учетных данных клиента

1. Создайте новый файл в каталоге **./графтуториал/аусентикатион** с именем **ClientCredentialsAuthProvider.CS** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/ClientCredentialsAuthProvider.cs" id="AuthProviderSnippet":::

Уделите несколько минут, чтобы определить, что делает код в **ClientCredentialsAuthProvider.CS** .

- В конструкторе он инициализирует **конфидентиалклиентаппликатион** из `Microsoft.Identity.Client` пакета. Она использует `WithAuthority(AadAuthorityAudience.AzureAdMyOrg, true)` функции и, `.WithTenantId(tenantId)` чтобы ограничить аудиторию входа только указанной организацией Microsoft 365.
- В `GetAccessToken` функции функция вызывается `AcquireTokenForClient` для получения маркера для приложения. Потоки маркеров учетных данных клиента всегда являются неинтерактивными.
- Он реализует `Microsoft.Graph.IAuthenticationProvider` интерфейс, позволяя передавать этот класс в конструктор `GraphServiceClient` для проверки подлинности исходящих запросов.

## <a name="update-graphclientservice"></a>Обновление Графклиентсервице

1. Откройте **GraphClientService.CS** и добавьте в класс следующее свойство.

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="AppGraphClientMembers":::

1. Замените имеющуюся функцию `GetAppGraphClient` указанным ниже кодом.

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="AppGraphClientMembers":::

## <a name="implement-notify-function"></a>Реализация функции notify

В этом разделе описывается реализация `Notify` функции, которая будет использоваться в качестве URL-адреса уведомления для уведомлений об изменениях.

1. Создайте новый каталог в каталоге **графтуториалс** с именем **Models**.

1. Создайте новый файл в каталоге **Models** с именем **ResourceData.CS** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Models/ResourceData.cs" id="ResourceDataSnippet":::

1. Создайте новый файл в каталоге **Models** с именем **ChangeNotification.CS** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Models/ChangeNotification.cs" id="ChangeNotificationSnippet":::

1. Создайте новый файл в каталоге **Models** с именем **NotificationList.CS** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Models/NotificationList.cs" id="NotificationListSnippet":::

1. Откройте **/графтуториал/нотифи.КС** и замените все содержимое следующим.

    :::code language="csharp" source="../demo/GraphTutorial/Notify.cs" id="NotifySnippet":::

Уделите несколько минут, чтобы определить, что делает код в **Notify.CS** .

- `Run`Функция проверяет наличие `validationToken` параметра запроса. Если этот параметр присутствует, он обрабатывает запрос как [запрос на проверку](https://docs.microsoft.com/graph/webhooks#notification-endpoint-validation)и отвечает соответствующим образом.
- Если запрос не является запросом проверки, полезная нагрузка JSON десериализуется в объект `NotificationList` .
- Каждое уведомление в списке проверяется на наличие ожидаемого значения состояния клиента и обрабатывается.
- Сообщение, вызвавшее уведомление, будет извлечено в Microsoft Graph.

## <a name="implement-setsubscription-function"></a>Реализация функции Сетсубскриптион

В этом разделе описывается реализация функции Сетсубскриптион. Эта функция будет использоваться в качестве API, который вызывается тестовым приложением для создания или удаления подписки в папке "Входящие" пользователя.

1. Создайте новый файл в каталоге **Models** с именем **SetSubscriptionPayload.CS** и добавьте следующий код.

    :::code language="csharp" source="../demo/GraphTutorial/Models/SetSubscriptionPayload.cs" id="SetSubscriptionPayloadSnippet":::

1. Откройте **/графтуториал/сетсубскриптион.КС** и замените все содержимое следующим.

    :::code language="csharp" source="../demo/GraphTutorial/SetSubscription.cs" id="SetSubscriptionSnippet":::

Уделите несколько минут, чтобы определить, что делает код в **SetSubscription.CS** .

- `Run`Функция считывает полезные данные JSON, отправленные в запросе POST, чтобы определить тип запроса (подписаться или отписаться), идентификатор пользователя для подписки и идентификатор подписки для отказа от подписки.
- Если запрос является запросом на подписку, он использует пакет SDK Microsoft Graph, чтобы создать новую подписку в папке "Входящие" указанного пользователя. Подписка будет уведомлять о создании или обновлении сообщений. Новая подписка возвращается в полезных данных JSON ответа.
- Если запрос является запросом на отмену подписки, он использует пакет SDK Microsoft Graph, чтобы удалить указанную подписку.

## <a name="call-setsubscription-from-the-test-app"></a>Вызов Сетсубскриптион из тестового приложения

В этом разделе мы реализуем функции для создания и удаления подписок в тестовом приложении.

1. Откройте **/тестклиент/azurefunctions.js** и добавьте указанную ниже функцию.

    :::code language="javascript" source="../demo/TestClient/azurefunctions.js" id="createSubscriptionSnippet":::

    Этот код вызывает `SetSubscription` функцию Azure для подписки и добавляет новую подписку в массив подписок в сеансе.

1. Добавьте указанную ниже функцию в **azurefunctions.js**.

    :::code language="javascript" source="../demo/TestClient/azurefunctions.js" id="deleteSubscriptionSnippet":::

    Этот код вызывает `SetSubscription` функцию Azure для отмены и удаления подписки из массива подписок в сеансе.

1. Если ngrok не запущены, запустите ngrok ( `ngrok http 7071` ) и скопируйте URL-адрес пересылки HTTPS.

1. Добавьте URL-адрес ngrok в пользовательское хранилище секреты, выполнив следующую команду.

    ```Shell
    dotnet user-secrets set ngrokUrl "YOUR_NGROK_URL_HERE"
    ```

    > [!IMPORTANT]
    > Если перезапустить ngrok, вам потребуется повторить эту команду, чтобы обновить URL-адрес ngrok.

1. Измените текущий каталог в командной системе CLI на каталог **./графтуториал** и выполните следующую команду, чтобы запустить функцию Azure локально.

    ```Shell
    func start
    ```

1. Обновите SPA и выберите элемент навигации по **подпискам** . Введите идентификатор пользователя для пользователя в организации Microsoft 365 с почтовым ящиком Exchange Online. Это может быть либо пользователь `id` (из Microsoft Graph), либо пользователь `userPrincipalName` . Нажмите кнопку **подписаться**.

1. На странице отображается новая подписка в таблице.

1. Отправьте пользователю сообщение электронной почты. После краткого времени `Notify` необходимо вызвать функцию. Это можно проверить в веб-интерфейсе ngrok ( `http://localhost:4040` ) или в выходных данных отладки проекта функции Azure.

    ```Shell
    ...
    [7/8/2020 7:33:57 PM] The following message was created:
    [7/8/2020 7:33:57 PM] Subject: Hi Megan!, ID: AAMkAGUyN2I4N2RlLTEzMTAtNDBmYy1hODdlLTY2NTQwODE2MGEwZgBGAAAAAAA2J9QH-DvMRK3pBt_8rA6nBwCuPIFjbMEkToHcVnQirM5qAAAAAAEMAACuPIFjbMEkToHcVnQirM5qAACHmpAsAAA=
    [7/8/2020 7:33:57 PM] Executed 'Notify' (Succeeded, Id=9c40af0b-e082-4418-aa3a-aee624f30e7a)
    ...
    ```

1. В тестовом приложении нажмите кнопку **Delete (удалить** ) в строке таблицы для подписки. Страница обновится, и подписка перестанет быть в таблице.
