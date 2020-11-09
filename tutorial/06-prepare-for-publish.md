---
ms.openlocfilehash: fbd43b4708cd1aeaad93b70fcf151869bd9e543e
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822710"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="feb5e-101">В этом упражнении вы узнаете, какие изменения необходимы для примера функции Azure, чтобы подготовиться к [публикации в приложении функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local#publish).</span><span class="sxs-lookup"><span data-stu-id="feb5e-101">In this exercise you'll learn about what changes are needed to the sample Azure Function to prepare for [publishing to an Azure Functions app](https://docs.microsoft.com/azure/azure-functions/functions-run-local#publish).</span></span>

## <a name="update-code"></a><span data-ttu-id="feb5e-102">Обновление кода</span><span class="sxs-lookup"><span data-stu-id="feb5e-102">Update code</span></span>

<span data-ttu-id="feb5e-103">Конфигурация считывается из хранилища секрета пользователя, которое применяется только к компьютеру, на котором выполняется разработка.</span><span class="sxs-lookup"><span data-stu-id="feb5e-103">Configuration is read from the user secret store, which only applies to your development machine.</span></span> <span data-ttu-id="feb5e-104">Перед публикацией в Azure необходимо изменить место хранения конфигурации и соответствующим образом обновить код в **Startup.CS** .</span><span class="sxs-lookup"><span data-stu-id="feb5e-104">Before you publish to Azure, you'll need to change where you store your configuration, and update the code in **Startup.cs** accordingly.</span></span>

<span data-ttu-id="feb5e-105">Секреты приложений должны храниться в безопасном хранилище, таком как [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview).</span><span class="sxs-lookup"><span data-stu-id="feb5e-105">Application secrets should be stored in secure storage, such as [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview).</span></span>

## <a name="update-cors-setting-for-azure-function"></a><span data-ttu-id="feb5e-106">Обновление параметра CORS для функции Azure</span><span class="sxs-lookup"><span data-stu-id="feb5e-106">Update CORS setting for Azure Function</span></span>

<span data-ttu-id="feb5e-107">В этом примере мы настроили CORS в **local.settings.json** для того, чтобы тестовое приложение вызывало функцию.</span><span class="sxs-lookup"><span data-stu-id="feb5e-107">In this sample we configured CORS in **local.settings.json** to allow the test application to call the function.</span></span> <span data-ttu-id="feb5e-108">Вам потребуется настроить опубликованную функцию, чтобы разрешить все приложения SPA, которые будут вызывать ее.</span><span class="sxs-lookup"><span data-stu-id="feb5e-108">You'll need to configure your published function to allow any SPA apps that will call it.</span></span>

## <a name="update-app-registrations"></a><span data-ttu-id="feb5e-109">Обновление регистраций приложений</span><span class="sxs-lookup"><span data-stu-id="feb5e-109">Update app registrations</span></span>

<span data-ttu-id="feb5e-110">`knownClientApplications`Свойство в манифесте для регистрации приложения **функции Graph Azure** необходимо обновить с идентификаторами всех приложений, которые будут вызывать функцию Azure.</span><span class="sxs-lookup"><span data-stu-id="feb5e-110">The  `knownClientApplications` property in the manifest for the **Graph Azure Function** app registration will need to be updated with the application IDs of any apps that will be calling the Azure Function.</span></span>

## <a name="recreate-existing-subscriptions"></a><span data-ttu-id="feb5e-111">Повторное создание существующих подписок</span><span class="sxs-lookup"><span data-stu-id="feb5e-111">Recreate existing subscriptions</span></span>

<span data-ttu-id="feb5e-112">Все подписки, созданные с помощью URL-адреса веб-перехватчика на локальном компьютере или ngrok, необходимо повторно создать с помощью рабочего URL-адреса `Notify` функции Azure.</span><span class="sxs-lookup"><span data-stu-id="feb5e-112">Any subscriptions created using the webhook URL on your local machine or ngrok should be recreated using the production URL of the `Notify` Azure Function.</span></span>
