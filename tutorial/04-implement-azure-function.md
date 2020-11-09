---
ms.openlocfilehash: 728138b5d298f9469a566defed6ee2e457896c0b
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822734"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="413ed-101">В этом упражнении вы завершите реализацию функции Azure `GetMyNewestMessage` и обновите тестовый клиент, чтобы вызвать функцию.</span><span class="sxs-lookup"><span data-stu-id="413ed-101">In this exercise you will finish implementing the Azure Function `GetMyNewestMessage` and update the test client to call the function.</span></span>

<span data-ttu-id="413ed-102">Функция Azure использует [потоки от имени](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)"от имени".</span><span class="sxs-lookup"><span data-stu-id="413ed-102">The Azure Function uses the [on-behalf-of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).</span></span> <span data-ttu-id="413ed-103">Ниже приведен базовый порядок событий этого процесса.</span><span class="sxs-lookup"><span data-stu-id="413ed-103">The basic order of events in this flow are:</span></span>

- <span data-ttu-id="413ed-104">В тестовом приложении используется интерактивный процесс проверки подлинности, позволяющий пользователю выполнить вход и предоставить согласие.</span><span class="sxs-lookup"><span data-stu-id="413ed-104">The test application uses an interactive auth flow to allow the user to sign in and grant consent.</span></span> <span data-ttu-id="413ed-105">Возвращается маркер, областью действия которого является функция Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-105">It gets back a token that is scoped to the Azure Function.</span></span> <span data-ttu-id="413ed-106">Маркер **не содержит областей** Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="413ed-106">The token does **NOT** contain any Microsoft Graph scopes.</span></span>
- <span data-ttu-id="413ed-107">Тестовое приложение вызывает функцию Azure, отправляя маркер доступа в `Authorization` заголовке.</span><span class="sxs-lookup"><span data-stu-id="413ed-107">The test application invokes the Azure Function, sending its access token in the `Authorization` header.</span></span>
- <span data-ttu-id="413ed-108">Функция Azure проверяет маркер, затем обменивается данными маркером для второго маркера доступа, содержащего области Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="413ed-108">The Azure Function validates the token, then exchanges that token for a second access token that contains Microsoft Graph scopes.</span></span>
- <span data-ttu-id="413ed-109">Функция Azure вызывает Microsoft Graph от имени пользователя с помощью второго маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="413ed-109">The Azure Function calls Microsoft Graph on the user's behalf using the second access token.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="413ed-110">Чтобы не хранить идентификатор и секрет приложения в источнике, вы будете использовать [Диспетчер секрета .NET](https://docs.microsoft.com/aspnet/core/security/app-secrets) для хранения этих значений.</span><span class="sxs-lookup"><span data-stu-id="413ed-110">To avoid storing the application ID and secret in source, you will use the [.NET Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) to store these values.</span></span> <span data-ttu-id="413ed-111">Диспетчер секретности предназначен только для целей разработки, рабочие приложения должны использовать доверенный диспетчер секретов для хранения секретов.</span><span class="sxs-lookup"><span data-stu-id="413ed-111">The Secret Manager is for development purposes only, production apps should use a trusted secret manager for storing secrets.</span></span>

## <a name="add-authentication-to-the-single-page-application"></a><span data-ttu-id="413ed-112">Добавление проверки подлинности для приложения с одной страницей</span><span class="sxs-lookup"><span data-stu-id="413ed-112">Add authentication to the single page application</span></span>

<span data-ttu-id="413ed-113">Для начала добавьте проверку подлинности в SPA.</span><span class="sxs-lookup"><span data-stu-id="413ed-113">Start by adding authentication to the SPA.</span></span> <span data-ttu-id="413ed-114">Это позволит приложению получить маркер доступа, предоставляя доступ для вызова функции Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-114">This will allow the application to get an access token granting access to call the Azure Function.</span></span> <span data-ttu-id="413ed-115">Так как это SPA, он будет использовать [код проверки подлинности для пкце](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).</span><span class="sxs-lookup"><span data-stu-id="413ed-115">Because this is a SPA, it will use the [authorization code flow with PKCE](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).</span></span>

1. <span data-ttu-id="413ed-116">Создайте новый файл в каталоге **тестклиент** с именем **config.js** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-116">Create a new file in the **TestClient** directory named **config.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/TestClient/config.example.js" id="msalConfigSnippet":::

    <span data-ttu-id="413ed-117">Замените на `YOUR_TEST_APP_APP_ID_HERE` идентификатор приложения, созданный на портале Azure для **тестового приложения функции Graph Azure**.</span><span class="sxs-lookup"><span data-stu-id="413ed-117">Replace `YOUR_TEST_APP_APP_ID_HERE` with the application ID you created in the Azure portal for the **Graph Azure Function Test App**.</span></span> <span data-ttu-id="413ed-118">Замените `YOUR_TENANT_ID_HERE` ЗНАЧЕНИЕМ **идентификатора Directory (клиент)** , скопированным с портала Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-118">Replace `YOUR_TENANT_ID_HERE` with the **Directory (tenant) ID** value you copied from the Azure portal.</span></span> <span data-ttu-id="413ed-119">Замените `YOUR_AZURE_FUNCTION_APP_ID_HERE` идентификатором приложения для **функции Graph Graph**.</span><span class="sxs-lookup"><span data-stu-id="413ed-119">Replace `YOUR_AZURE_FUNCTION_APP_ID_HERE` with the application ID for the **Graph Azure Function**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="413ed-120">Если вы используете систему управления версиями (например, Git), то в дальнейшем будет полезно исключить файл **config.js** из системы управления версиями, чтобы избежать непреднамеренного утечки идентификаторов и идентификатора клиента.</span><span class="sxs-lookup"><span data-stu-id="413ed-120">If you're using source control such as git, now would be a good time to exclude the **config.js** file from source control to avoid inadvertently leaking your app IDs and tenant ID.</span></span>

1. <span data-ttu-id="413ed-121">Создайте новый файл в каталоге **тестклиент** с именем **auth.js** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-121">Create a new file in the **TestClient** directory named **auth.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/TestClient/auth.js" id="signInSignOutSnippet":::

    <span data-ttu-id="413ed-122">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="413ed-122">Consider what this code does.</span></span>

    - <span data-ttu-id="413ed-123">Он инициализирует объект `PublicClientApplication` с использованием значений, хранящихся в **config.js**.</span><span class="sxs-lookup"><span data-stu-id="413ed-123">It initializes a `PublicClientApplication` using the values stored in **config.js**.</span></span>
    - <span data-ttu-id="413ed-124">Используется `loginPopup` для подписания пользователя в, используя область разрешений для функции Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-124">It uses `loginPopup` to sign the user in, using the permission scope for the Azure Function.</span></span>
    - <span data-ttu-id="413ed-125">Он хранит имя пользователя пользователя в сеансе.</span><span class="sxs-lookup"><span data-stu-id="413ed-125">It stores the user's username in the session.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="413ed-126">Так как приложение использует приложение `loginPopup` , может потребоваться изменить блокирование всплывающих окон в браузере, чтобы разрешить всплывающие окна `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="413ed-126">Since the app uses `loginPopup`, you may need to change your browser's pop-up blocker to allow pop-ups from `http://localhost:8080`.</span></span>

1. <span data-ttu-id="413ed-127">Обновите страницу и войдите в нее.</span><span class="sxs-lookup"><span data-stu-id="413ed-127">Refresh the page and sign in.</span></span> <span data-ttu-id="413ed-128">Страница должна обновляться с именем пользователя, что означает, что вход выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="413ed-128">The page should update with the user name, indicating that the sign in was successful.</span></span>

> [!TIP]
> <span data-ttu-id="413ed-129">Вы можете проанализировать маркер доступа [https://jwt.ms](https://jwt.ms) и подтвердить, что `aud` Заявка является идентификатором приложения для функции Azure, и что `scp` утверждение содержит область разрешений функции Azure, а не Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="413ed-129">You can parse the access token at [https://jwt.ms](https://jwt.ms) and confirm that the `aud` claim is the app ID for the Azure Function, and that the `scp` claim contains the Azure Function's permission scope, not Microsoft Graph.</span></span>

## <a name="add-authentication-to-the-azure-function"></a><span data-ttu-id="413ed-130">Добавление проверки подлинности в функцию Azure</span><span class="sxs-lookup"><span data-stu-id="413ed-130">Add authentication to the Azure Function</span></span>

<span data-ttu-id="413ed-131">В этом разделе для `GetMyNewestMessage` получения маркера доступа, совместимого с Microsoft Graph, вы настроите потоки от имени пользователя в функции Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-131">In this section you'll implement the on-behalf-of flow in the `GetMyNewestMessage` Azure Function to get an access token compatible with Microsoft Graph.</span></span>

1. <span data-ttu-id="413ed-132">Инициализируйте хранилище секрета .NET Development, открыв его в каталоге, содержащем **графтуториал. csproj** , и выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="413ed-132">Initialize the .NET development secret store by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="413ed-133">Добавьте идентификатор приложения, секретный код и идентификатор клиента в Секретное хранилище с помощью следующих команд.</span><span class="sxs-lookup"><span data-stu-id="413ed-133">Add your application ID, secret, and tenant ID to the secret store using the following commands.</span></span> <span data-ttu-id="413ed-134">Замените `YOUR_API_FUNCTION_APP_ID_HERE` идентификатором приложения для **функции Graph Graph**.</span><span class="sxs-lookup"><span data-stu-id="413ed-134">Replace `YOUR_API_FUNCTION_APP_ID_HERE` with the application ID for the **Graph Azure Function**.</span></span> <span data-ttu-id="413ed-135">Замените на `YOUR_API_FUNCTION_APP_SECRET_HERE` секрет приложения, созданный на портале Azure, для **функции Graph**.</span><span class="sxs-lookup"><span data-stu-id="413ed-135">Replace `YOUR_API_FUNCTION_APP_SECRET_HERE` with the application secret you created in the Azure portal for the **Graph Azure Function**.</span></span> <span data-ttu-id="413ed-136">Замените `YOUR_TENANT_ID_HERE` ЗНАЧЕНИЕМ **идентификатора Directory (клиент)** , скопированным с портала Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-136">Replace `YOUR_TENANT_ID_HERE` with the **Directory (tenant) ID** value you copied from the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set apiFunctionId "YOUR_API_FUNCTION_APP_ID_HERE"
    dotnet user-secrets set apiFunctionSecret "YOUR_API_FUNCTION_APP_SECRET_HERE"
    dotnet user-secrets set tenantId "YOUR_TENANT_ID_HERE"
    ```

### <a name="process-the-incoming-bearer-token"></a><span data-ttu-id="413ed-137">Обработка токена входящего носителя</span><span class="sxs-lookup"><span data-stu-id="413ed-137">Process the incoming bearer token</span></span>

<span data-ttu-id="413ed-138">В этом разделе описывается, как реализовать класс для проверки и обработки токена носителя, отправленного из SPA в функцию Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-138">In this section you'll implement a class to validate and process the bearer token sent from the SPA to the Azure Function.</span></span>

1. <span data-ttu-id="413ed-139">Создайте новый каталог в каталоге **графтуториал** с именем **authentication**.</span><span class="sxs-lookup"><span data-stu-id="413ed-139">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>

1. <span data-ttu-id="413ed-140">Создайте новый файл с именем **TokenValidationResult.CS** в папке **./графтуториал/аусентикатион** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-140">Create a new file named **TokenValidationResult.cs** in the **./GraphTutorial/Authentication** folder, and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/TokenValidationResult.cs" id="TokenValidationResultSnippet":::

1. <span data-ttu-id="413ed-141">Создайте новый файл с именем **TokenValidation.CS** в папке **./графтуториал/аусентикатион** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-141">Create a new file named **TokenValidation.cs** in the **./GraphTutorial/Authentication** folder, and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/TokenValidation.cs" id="TokenValidationSnippet":::

<span data-ttu-id="413ed-142">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="413ed-142">Consider what this code does.</span></span>

- <span data-ttu-id="413ed-143">Это гарантирует, что в `Authorization` заголовке присутствует токен носителя.</span><span class="sxs-lookup"><span data-stu-id="413ed-143">It ensure there is a bearer token in the `Authorization` header.</span></span>
- <span data-ttu-id="413ed-144">Он проверяет подпись и поставщика из конфигурации опубликованной OpenID в Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-144">It verifies the signature and issuer from Azure's published OpenID configuration.</span></span>
- <span data-ttu-id="413ed-145">Она проверяет соответствие аудитории ( `aud` утверждения) идентификатору приложения функции Azure.</span><span class="sxs-lookup"><span data-stu-id="413ed-145">It verifies that the audience (`aud` claim) matches the Azure Function's application ID.</span></span>
- <span data-ttu-id="413ed-146">Он выполняет синтаксический анализ маркера и создает идентификатор учетной записи MSAL, который будет необходим для использования кэширования маркеров.</span><span class="sxs-lookup"><span data-stu-id="413ed-146">It parses the token and generates an MSAL account ID, which will be needed to take advantage of token caching.</span></span>

### <a name="create-an-on-behalf-of-authentication-provider"></a><span data-ttu-id="413ed-147">Создание поставщика проверки подлинности "от имени"</span><span class="sxs-lookup"><span data-stu-id="413ed-147">Create an on-behalf-of authentication provider</span></span>

1. <span data-ttu-id="413ed-148">Создайте новый файл в каталоге **проверки подлинности** с именем **OnBehalfOfAuthProvider.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-148">Create a new file in the **Authentication** directory named **OnBehalfOfAuthProvider.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/OnBehalfOfAuthProvider.cs" id="AuthProviderSnippet":::

<span data-ttu-id="413ed-149">Уделите несколько минут, чтобы определить, что делает код в **OnBehalfOfAuthProvider.CS** .</span><span class="sxs-lookup"><span data-stu-id="413ed-149">Take a moment to consider what the code in **OnBehalfOfAuthProvider.cs** does.</span></span>

- <span data-ttu-id="413ed-150">В `GetAccessToken` функции она сначала пытается получить маркер пользователя из кэша маркеров с помощью метода `AcquireTokenSilent` .</span><span class="sxs-lookup"><span data-stu-id="413ed-150">In the `GetAccessToken` function, it first attempts to get a user token from the token cache using `AcquireTokenSilent`.</span></span> <span data-ttu-id="413ed-151">В противном случае он использует маркер носителя, отправленный тестовым приложением, в функцию Azure для создания утверждения пользователя.</span><span class="sxs-lookup"><span data-stu-id="413ed-151">If this fails, it uses the bearer token sent by the test app to the Azure Function to generate a user assertion.</span></span> <span data-ttu-id="413ed-152">Затем он использует утверждение пользователя, чтобы получить маркер, совместимый с графиком, с помощью `AcquireTokenOnBehalfOf` .</span><span class="sxs-lookup"><span data-stu-id="413ed-152">It then uses that user assertion to get a Graph-compatible token using `AcquireTokenOnBehalfOf`.</span></span>
- <span data-ttu-id="413ed-153">Он реализует `Microsoft.Graph.IAuthenticationProvider` интерфейс, позволяя передавать этот класс в конструктор `GraphServiceClient` для проверки подлинности исходящих запросов.</span><span class="sxs-lookup"><span data-stu-id="413ed-153">It implements the `Microsoft.Graph.IAuthenticationProvider` interface, allowing this class to be passed in the constructor of the `GraphServiceClient` to authenticate outgoing requests.</span></span>

### <a name="implement-a-graph-client-service"></a><span data-ttu-id="413ed-154">Реализация клиентской службы Graph</span><span class="sxs-lookup"><span data-stu-id="413ed-154">Implement a Graph client service</span></span>

<span data-ttu-id="413ed-155">В этом разделе описывается реализация службы, которая может быть зарегистрирована для [внедрения зависимостей](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="413ed-155">In this section you'll implement a service that can be registered for [dependency injection](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection).</span></span> <span data-ttu-id="413ed-156">Служба будет использоваться для получения клиента Graph, прошедшего проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="413ed-156">The service will be used to get an authenticated Graph client.</span></span>

1. <span data-ttu-id="413ed-157">Создайте новый каталог в каталоге **графтуториал** с именем **Services**.</span><span class="sxs-lookup"><span data-stu-id="413ed-157">Create a new directory in the **GraphTutorial** directory named **Services**.</span></span>

1. <span data-ttu-id="413ed-158">Создайте новый файл в каталоге **Services** с именем **IGraphClientService.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-158">Create a new file in the **Services** directory named **IGraphClientService.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Services/IGraphClientService.cs" id="IGraphClientServiceSnippet":::

1. <span data-ttu-id="413ed-159">Создайте новый файл в каталоге **Services** с именем **GraphClientService.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-159">Create a new file in the **Services** directory named **GraphClientService.cs** and add the following code to that file.</span></span>

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

1. <span data-ttu-id="413ed-160">Добавьте в класс указанные ниже свойства `GraphClientService` .</span><span class="sxs-lookup"><span data-stu-id="413ed-160">Add the following properties to the `GraphClientService` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="UserGraphClientMembers":::

1. <span data-ttu-id="413ed-161">Добавьте в класс следующие функции `GraphClientService` .</span><span class="sxs-lookup"><span data-stu-id="413ed-161">Add the following functions to the `GraphClientService` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Services/GraphClientService.cs" id="UserGraphClientFunctions":::

1. <span data-ttu-id="413ed-162">Добавьте реализацию заполнителя для `GetAppGraphClient` функции.</span><span class="sxs-lookup"><span data-stu-id="413ed-162">Add a placeholder implementation for the `GetAppGraphClient` function.</span></span> <span data-ttu-id="413ed-163">Это будет реализовано в последующих разделах.</span><span class="sxs-lookup"><span data-stu-id="413ed-163">You will implement that in later sections.</span></span>

    ```csharp
    public GraphServiceClient GetAppGraphClient()
    {
        throw new System.NotImplementedException();
    }
    ```

    <span data-ttu-id="413ed-164">`GetUserGraphClient`Функция выполняет результаты проверки маркера и создает для пользователя проверку подлинности `GraphServiceClient` .</span><span class="sxs-lookup"><span data-stu-id="413ed-164">The `GetUserGraphClient` function takes the results of token validation and builds an authenticated `GraphServiceClient` for the user.</span></span>

1. <span data-ttu-id="413ed-165">Создайте новый файл в каталоге **графтуториал** с именем **Startup.CS** и добавьте в этот файл следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-165">Create a new file in the **GraphTutorial** directory named **Startup.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Startup.cs" id="StartupSnippet":::

    <span data-ttu-id="413ed-166">Этот код включает [внедрение зависимостей](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) в функции Azure, предоставляя `IConfiguration` объект и `GraphClientService` службу.</span><span class="sxs-lookup"><span data-stu-id="413ed-166">This code will enable [dependency injection](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection) in your Azure Functions, exposing the `IConfiguration` object and the `GraphClientService` service.</span></span>

### <a name="implement-getmynewestmessage-function"></a><span data-ttu-id="413ed-167">Реализация функции Жетминевестмессаже</span><span class="sxs-lookup"><span data-stu-id="413ed-167">Implement GetMyNewestMessage function</span></span>

1. <span data-ttu-id="413ed-168">Откройте **/графтуториал/жетминевестмессаже.КС** и замените все содержимое следующим.</span><span class="sxs-lookup"><span data-stu-id="413ed-168">Open **./GraphTutorial/GetMyNewestMessage.cs** and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GetMyNewestMessage.cs" id="GetMyNewestMessageSnippet":::

#### <a name="review-the-code-in-getmynewestmessagecs"></a><span data-ttu-id="413ed-169">Просмотр кода в GetMyNewestMessage.cs</span><span class="sxs-lookup"><span data-stu-id="413ed-169">Review the code in GetMyNewestMessage.cs</span></span>

<span data-ttu-id="413ed-170">Уделите несколько минут, чтобы определить, что делает код в **GetMyNewestMessage.CS** .</span><span class="sxs-lookup"><span data-stu-id="413ed-170">Take a moment to consider what the code in **GetMyNewestMessage.cs** does.</span></span>

- <span data-ttu-id="413ed-171">В конструкторе он сохраняет объект, `IConfiguration` переданный с помощью внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="413ed-171">In the constructor, it saves the `IConfiguration` object passed in via dependency injection.</span></span>
- <span data-ttu-id="413ed-172">В `Run` функции она выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="413ed-172">In the `Run` function, it does the following:</span></span>
  - <span data-ttu-id="413ed-173">Проверяет наличие обязательных значений конфигурации в `IConfiguration` объекте.</span><span class="sxs-lookup"><span data-stu-id="413ed-173">Validates the required configuration values are present in the `IConfiguration` object.</span></span>
  - <span data-ttu-id="413ed-174">Проверяет маркер носителя и возвращает `401` код состояния, если маркер является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="413ed-174">Validates the bearer token and returns a `401` status code if the token is invalid.</span></span>
  - <span data-ttu-id="413ed-175">Возвращает клиент графа `GraphClientService` для пользователя, который внес этот запрос.</span><span class="sxs-lookup"><span data-stu-id="413ed-175">Gets a Graph client from the `GraphClientService` for the user that made this request.</span></span>
  - <span data-ttu-id="413ed-176">Использует пакет SDK Microsoft Graph, чтобы получить Последнее сообщение из папки "Входящие" пользователя и возвратить его в ответе в теле JSON.</span><span class="sxs-lookup"><span data-stu-id="413ed-176">Uses the Microsoft Graph SDK to get the newest message from the user's inbox and returns it as a JSON body in the response.</span></span>

## <a name="call-the-azure-function-from-the-test-app"></a><span data-ttu-id="413ed-177">Вызов функции Azure из тестового приложения</span><span class="sxs-lookup"><span data-stu-id="413ed-177">Call the Azure Function from the test app</span></span>

1. <span data-ttu-id="413ed-178">Откройте **auth.js** и добавьте указанную ниже функцию, чтобы получить маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="413ed-178">Open **auth.js** and add the following function to get an access token.</span></span>

    :::code language="javascript" source="../demo/TestClient/auth.js" id="getTokenSnippet":::

    <span data-ttu-id="413ed-179">Рассмотрите, что делает этот код.</span><span class="sxs-lookup"><span data-stu-id="413ed-179">Consider what this code does.</span></span>

    - <span data-ttu-id="413ed-180">Сначала он пытается получить маркер доступа без вмешательства пользователя.</span><span class="sxs-lookup"><span data-stu-id="413ed-180">It first attempts to get an access token silently, without user interaction.</span></span> <span data-ttu-id="413ed-181">Так как пользователь уже должен войти в систему, MSAL должен иметь маркеры для пользователя в его кэше.</span><span class="sxs-lookup"><span data-stu-id="413ed-181">Since the user should already be signed in, MSAL should have tokens for the user in its cache.</span></span>
    - <span data-ttu-id="413ed-182">Если произойдет сбой с ошибкой, указывающей на то, что пользователь должен взаимодействовать, он пытается интерактивно получить маркер.</span><span class="sxs-lookup"><span data-stu-id="413ed-182">If that fails with an error that indicates the user needs to interact, it attempts to get a token interactively.</span></span>

1. <span data-ttu-id="413ed-183">Создайте новый файл в каталоге **тестклиент** с именем **azurefunctions.js** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="413ed-183">Create a new file in the **TestClient** directory named **azurefunctions.js** and add the following code.</span></span>

    :::code language="javascript" source="../demo/TestClient/azurefunctions.js" id="getLatestMessageSnippet":::

1. <span data-ttu-id="413ed-184">Измените текущий каталог в командной системе CLI на каталог **./графтуториал** и выполните следующую команду, чтобы запустить функцию Azure локально.</span><span class="sxs-lookup"><span data-stu-id="413ed-184">Change the current directory in your CLI to the **./GraphTutorial** directory and run the following command to start the Azure Function locally.</span></span>

    ```Shell
    func start
    ```

1. <span data-ttu-id="413ed-185">Если вы еще не обслуживаете SPA, откройте второе окно CLI и измените текущий каталог на каталог **./тестклиент** .</span><span class="sxs-lookup"><span data-stu-id="413ed-185">If not already serving the SPA, open a second CLI window and change the current directory to the **./TestClient** directory.</span></span> <span data-ttu-id="413ed-186">Выполните следующую команду, чтобы запустить тестовое приложение.</span><span class="sxs-lookup"><span data-stu-id="413ed-186">Run the following command to run the test application.</span></span>

    ```Shell
    dotnet serve -h "Cache-Control: no-cache, no-store, must-revalidate"
    ```

1. <span data-ttu-id="413ed-187">Откройте браузер и перейдите по адресу `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="413ed-187">Open your browser and navigate to `http://localhost:8080`.</span></span> <span data-ttu-id="413ed-188">Выполните вход и выберите последний элемент навигации по **сообщению** .</span><span class="sxs-lookup"><span data-stu-id="413ed-188">Sign in and select the **Latest Message** navigation item.</span></span> <span data-ttu-id="413ed-189">Приложение отображает сведения о последних сообщениях в папке "Входящие" пользователя.</span><span class="sxs-lookup"><span data-stu-id="413ed-189">The app displays information about the newest message in the user's inbox.</span></span>
