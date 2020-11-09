---
ms.openlocfilehash: 003d7f382a0c950d31bb84176feb1e83fbd5cf19
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822698"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0e289-101">В этом руководстве показано, как создать простую функцию Azure, которая реализует функции триггера HTTP, вызывающие Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0e289-101">In this tutorial, you will create a simple Azure Function that implements HTTP trigger functions that call Microsoft Graph.</span></span> <span data-ttu-id="0e289-102">Эти функции будут охватывать следующие сценарии:</span><span class="sxs-lookup"><span data-stu-id="0e289-102">These functions will cover the following scenarios:</span></span>

- <span data-ttu-id="0e289-103">Реализует API для доступа к папке "Входящие" пользователя с помощью проверки подлинности " [от имени](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) ".</span><span class="sxs-lookup"><span data-stu-id="0e289-103">Implements an API to access a user's inbox using [on-behalf-of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) authentication.</span></span>
- <span data-ttu-id="0e289-104">Реализует API для подписки и отмены подписки на уведомления в папке "Входящие" пользователя, используя [учетные данные клиента, предоставляют](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) проверку подлинности с помощью клиента.</span><span class="sxs-lookup"><span data-stu-id="0e289-104">Implements an API to subscribe and unsubscribe for notifications on a user's inbox, using using [client credentials grant flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) authentication.</span></span>
- <span data-ttu-id="0e289-105">Реализует веб-перехватчик для получения [уведомлений об изменениях](https://docs.microsoft.com/graph/webhooks) от Microsoft Graph и доступа к данным с помощью потоки предоставления учетных данных клиента.</span><span class="sxs-lookup"><span data-stu-id="0e289-105">Implements a webhook to receive [change notifications](https://docs.microsoft.com/graph/webhooks) from Microsoft Graph and access data using client credentials grant flow.</span></span>

<span data-ttu-id="0e289-106">Кроме того, вы создадите простое одностраничное приложение JavaScript (SPA), чтобы вызвать API, реализованные в функции Azure.</span><span class="sxs-lookup"><span data-stu-id="0e289-106">You will also create a simple JavaScript single-page application (SPA) to call the APIs implemented in the Azure Function.</span></span>

## <a name="create-azure-functions-project"></a><span data-ttu-id="0e289-107">Создание проекта функций Azure</span><span class="sxs-lookup"><span data-stu-id="0e289-107">Create Azure Functions project</span></span>

1. <span data-ttu-id="0e289-108">Откройте интерфейс командной строки (CLI) в каталоге, в котором нужно создать проект.</span><span class="sxs-lookup"><span data-stu-id="0e289-108">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="0e289-109">Выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="0e289-109">Run the following command.</span></span>

    ```Shell
    func init GraphTutorial --dotnet
    ```

1. <span data-ttu-id="0e289-110">Измените текущий каталог в CLI на каталог **графтуториал** и выполните следующие команды, чтобы создать три функции в проекте.</span><span class="sxs-lookup"><span data-stu-id="0e289-110">Change the current directory in your CLI to the **GraphTutorial** directory and run the following commands to create three functions in the project.</span></span>

    ```Shell
    func new --name GetMyNewestMessage --template "HTTP trigger" --language C#
    func new --name SetSubscription --template "HTTP trigger" --language C#
    func new --name Notify --template "HTTP trigger" --language C#
    ```

1. <span data-ttu-id="0e289-111">Откройте **local.settings.js** и добавьте в файл следующую команду, чтобы разрешить CORS с `http://localhost:8080` URL-адреса для тестового приложения.</span><span class="sxs-lookup"><span data-stu-id="0e289-111">Open **local.settings.json** and add the following to the file to allow CORS from `http://localhost:8080`, the URL for the test application.</span></span>

    ```json
    "Host": {
      "CORS": "http://localhost:8080"
    }
    ```

1. <span data-ttu-id="0e289-112">Выполните следующую команду, чтобы запустить проект локально.</span><span class="sxs-lookup"><span data-stu-id="0e289-112">Run the following command to run the project locally.</span></span>

    ```Shell
    func start
    ```

1. <span data-ttu-id="0e289-113">Если все работает, вы увидите следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="0e289-113">If everything is working, you will see the following output:</span></span>

    ```Shell
    Http Functions:

        GetMyNewestMessage: [GET,POST] http://localhost:7071/api/GetMyNewestMessage

        Notify: [GET,POST] http://localhost:7071/api/Notify

        SetSubscription: [GET,POST] http://localhost:7071/api/SetSubscription
    ```

1. <span data-ttu-id="0e289-114">Убедитесь, что функции работают правильно, открыв браузер и просмотрев URL-адреса функций, показанные в выходных данных.</span><span class="sxs-lookup"><span data-stu-id="0e289-114">Verify that the functions are working correctly by opening your browser and browsing to the function URLs shown in the output.</span></span> <span data-ttu-id="0e289-115">В браузере должно появиться следующее сообщение: `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.` .</span><span class="sxs-lookup"><span data-stu-id="0e289-115">You should see the following message in your browser: `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.`.</span></span>

## <a name="create-single-page-application"></a><span data-ttu-id="0e289-116">Создание одностраничного приложения</span><span class="sxs-lookup"><span data-stu-id="0e289-116">Create single-page application</span></span>

1. <span data-ttu-id="0e289-117">Откройте подсистему CLI в каталоге, в котором необходимо создать проект.</span><span class="sxs-lookup"><span data-stu-id="0e289-117">Open your CLI in a directory where you want to create the project.</span></span> <span data-ttu-id="0e289-118">Создайте каталог с именем **тестклиент** для хранения файлов HTML и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0e289-118">Create a directory named **TestClient** to hold your HTML and JavaScript files.</span></span>

1. <span data-ttu-id="0e289-119">Создайте новый файл с именем **index.html** в каталоге **тестклиент** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="0e289-119">Create a new file named **index.html** in the **TestClient** directory and add the following code.</span></span>

    :::code language="html" source="../demo/TestClient/index.html" id="indexSnippet":::

    <span data-ttu-id="0e289-120">Этот параметр определяет базовую структуру приложения, в том числе панель навигации.</span><span class="sxs-lookup"><span data-stu-id="0e289-120">This defines the basic layout of the app, including a navigation bar.</span></span> <span data-ttu-id="0e289-121">Кроме того, добавляются следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="0e289-121">It also adds the following:</span></span>

    - <span data-ttu-id="0e289-122">[Начальная](https://getbootstrap.com/) Загрузка и поддерживающая JavaScript</span><span class="sxs-lookup"><span data-stu-id="0e289-122">[Bootstrap](https://getbootstrap.com/) and its supporting JavaScript</span></span>
    - [<span data-ttu-id="0e289-123">фонтавесоме</span><span class="sxs-lookup"><span data-stu-id="0e289-123">FontAwesome</span></span>](https://fontawesome.com/)
    - [<span data-ttu-id="0e289-124">Библиотека проверки подлинности (Майкрософт) для JavaScript (MSAL.js) 2,0</span><span class="sxs-lookup"><span data-stu-id="0e289-124">Microsoft Authentication Library for JavaScript (MSAL.js) 2.0</span></span>](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

    > [!TIP]
    > <span data-ttu-id="0e289-125">Страница содержит фавикон ( `<link rel="shortcut icon" href="g-raph.png">` ).</span><span class="sxs-lookup"><span data-stu-id="0e289-125">The page includes a favicon, (`<link rel="shortcut icon" href="g-raph.png">`).</span></span> <span data-ttu-id="0e289-126">Вы можете удалить эту строку или скачать файл **g-raph.png** из [GitHub](https://github.com/microsoftgraph/g-raph).</span><span class="sxs-lookup"><span data-stu-id="0e289-126">You can remove this line, or you can download the **g-raph.png** file from [GitHub](https://github.com/microsoftgraph/g-raph).</span></span>

1. <span data-ttu-id="0e289-127">Создайте новый файл с именем **Style. CSS** в каталоге **тестклиент** и добавьте приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="0e289-127">Create a new file named **style.css** in the **TestClient** directory and add the following code.</span></span>

    :::code language="css" source="../demo/TestClient/style.css":::

1. <span data-ttu-id="0e289-128">Создайте новый файл с именем **ui.js** в каталоге **тестклиент** и добавьте следующий код.</span><span class="sxs-lookup"><span data-stu-id="0e289-128">Create a new file named **ui.js** in the **TestClient** directory and add the following code.</span></span>

    :::code language="javascript" source="../demo/TestClient/ui.js" id="uiJsSnippet":::

    <span data-ttu-id="0e289-129">Этот код использует JavaScript для отображения текущей страницы на основе выбранного представления.</span><span class="sxs-lookup"><span data-stu-id="0e289-129">This code uses JavaScript to render the current page based on the selected view.</span></span>

### <a name="test-the-single-page-application"></a><span data-ttu-id="0e289-130">Тестирование одностраничного приложения</span><span class="sxs-lookup"><span data-stu-id="0e289-130">Test the single-page application</span></span>

> [!NOTE]
> <span data-ttu-id="0e289-131">В этом разделе приведены инструкции по использованию [DotNet-обслуживает](https://github.com/natemcmaster/dotnet-serve) для запуска простого тестового HTTP-сервера на компьютере для разработки.</span><span class="sxs-lookup"><span data-stu-id="0e289-131">This section includes instructions for using [dotnet-serve](https://github.com/natemcmaster/dotnet-serve) to run a simple testing HTTP server on your development machine.</span></span> <span data-ttu-id="0e289-132">Использование этого конкретного средства не является обязательным.</span><span class="sxs-lookup"><span data-stu-id="0e289-132">Using this specific tool is not required.</span></span> <span data-ttu-id="0e289-133">Вы можете использовать любой тестовый сервер, который вы предпочитаете обслуживать каталог **тестклиент** .</span><span class="sxs-lookup"><span data-stu-id="0e289-133">You can use any testing server you prefer to serve the **TestClient** directory.</span></span>

1. <span data-ttu-id="0e289-134">Выполните следующую команду в командной панели управления для установки **DotNet — обслуживает**.</span><span class="sxs-lookup"><span data-stu-id="0e289-134">Run the following command in your CLI to install **dotnet-serve**.</span></span>

    ```Shell
    dotnet tool install --global dotnet-serve
    ```

1. <span data-ttu-id="0e289-135">Измените текущий каталог в CLI на каталог **тестклиент** и выполните следующую команду, чтобы запустить HTTP-сервер.</span><span class="sxs-lookup"><span data-stu-id="0e289-135">Change the current directory in your CLI to the **TestClient** directory and run the following command to start an HTTP server.</span></span>

    ```Shell
    dotnet serve -h "Cache-Control: no-cache, no-store, must-revalidate"
    ```

1. <span data-ttu-id="0e289-136">Откройте браузер и перейдите по адресу `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="0e289-136">Open your browser and navigate to `http://localhost:8080`.</span></span> <span data-ttu-id="0e289-137">Страница должна отображаться, но ни одна из кнопок в настоящее время не работает.</span><span class="sxs-lookup"><span data-stu-id="0e289-137">The page should render, but none of the buttons currently work.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="0e289-138">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="0e289-138">Add NuGet packages</span></span>

<span data-ttu-id="0e289-139">Прежде чем переходить, установите некоторые дополнительные пакеты NuGet, которые будут использоваться позже.</span><span class="sxs-lookup"><span data-stu-id="0e289-139">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="0e289-140">[Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions) для включения внедрения зависимостей в проект функций Azure.</span><span class="sxs-lookup"><span data-stu-id="0e289-140">[Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions) to enable dependency injection in the Azure Functions project.</span></span>
- <span data-ttu-id="0e289-141">[Microsoft.Extensions.Configуратион. Усерсекретс](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) чтение конфигурации приложения из [хранилища секретов для разработки .NET](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="0e289-141">[Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="0e289-142">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) для совершения звонков в Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0e289-142">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="0e289-143">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) для проверки подлинности и управления маркерами.</span><span class="sxs-lookup"><span data-stu-id="0e289-143">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) for authenticating and managing tokens.</span></span>
- <span data-ttu-id="0e289-144">[Microsoft. IdentityModel. Protocols. опенидконнект](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocols.OpenIdConnect) для получения конфигурации OpenID для проверки маркера.</span><span class="sxs-lookup"><span data-stu-id="0e289-144">[Microsoft.IdentityModel.Protocols.OpenIdConnect](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocols.OpenIdConnect) for retrieving OpenID configuration for token validation.</span></span>
- <span data-ttu-id="0e289-145">[System. IdentityModel. tokens. JWT](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) для проверки маркеров, отправляемых в веб-API.</span><span class="sxs-lookup"><span data-stu-id="0e289-145">[System.IdentityModel.Tokens.Jwt](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) for validating tokens sent to the web API.</span></span>

1. <span data-ttu-id="0e289-146">Измените текущий каталог в CLI на каталог **графтуториал** и выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="0e289-146">Change the current directory in your CLI to the **GraphTutorial** directory and run the following commands.</span></span>

    ```Shell
    dotnet add package Microsoft.Azure.Functions.Extensions --version 1.0.0
    dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.5
    dotnet add package Microsoft.Graph --version 3.8.0
    dotnet add package Microsoft.Identity.Client --version 4.15.0
    dotnet add package Microsoft.IdentityModel.Protocols.OpenIdConnect --version 6.7.1
    dotnet add package System.IdentityModel.Tokens.Jwt --version 6.7.1
    ```
