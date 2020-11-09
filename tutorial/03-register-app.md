---
ms.openlocfilehash: 0c9c6703a54e50f576a171a4a161305c9b9ae6ff
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822728"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ce191-101">В этом упражнении вы создадите три новых приложения Azure AD с помощью центра администрирования Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="ce191-101">In this exercise you will create three new Azure AD applications using the Azure Active Directory admin center:</span></span>

- <span data-ttu-id="ce191-102">Регистрация приложения для одностраничного приложения, чтобы она могла выполнять вход в систему и получать маркеры, позволяющие приложению вызывать функцию Azure.</span><span class="sxs-lookup"><span data-stu-id="ce191-102">An app registration for the single-page application so that it can sign in users and get tokens allowing the application to call the Azure Function.</span></span>
- <span data-ttu-id="ce191-103">Регистрация приложения для функции Azure, позволяющая использовать потоки "от [имени](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) " для обмена маркером, отправляемым SPA для маркера, который позволит ему вызвать Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ce191-103">An app registration for the Azure Function that allows it to use the [on-behalf-of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow) to exchange the token sent by the SPA for a token that will allow it to call Microsoft Graph.</span></span>
- <span data-ttu-id="ce191-104">Регистрация приложения для веб-перехватчика функции Azure, которая позволяет ему использовать [потоки учетных данных клиента](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) для вызова Microsoft Graph без пользователя.</span><span class="sxs-lookup"><span data-stu-id="ce191-104">An app registration for the Azure Function webhook that allows it to use the [client credential flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) to call Microsoft Graph without a user.</span></span>

> [!NOTE]
> <span data-ttu-id="ce191-105">В этом примере требуется три регистрации приложений, так как он реализует как потоки "от имени", так и потоки учетных данных клиента.</span><span class="sxs-lookup"><span data-stu-id="ce191-105">This example requires three app registrations because it is implementing both the on-behalf-of flow and the client credential flow.</span></span> <span data-ttu-id="ce191-106">Если функция Azure использует только один из этих потоков, необходимо создать только регистрации приложений, соответствующие этому потоку.</span><span class="sxs-lookup"><span data-stu-id="ce191-106">If your Azure Function only uses one of these flows, you would only need to create the app registrations that correspond to that flow.</span></span>

1. <span data-ttu-id="ce191-107">Откройте браузер и перейдите в [центр администрирования Azure Active Directory](https://aad.portal.azure.com) и войдите в систему с помощью администратора организации клиента Microsoft 365.</span><span class="sxs-lookup"><span data-stu-id="ce191-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using an Microsoft 365 tenant organization admin.</span></span>

1. <span data-ttu-id="ce191-108">Выберите **Azure Active Directory** на панели навигации слева, затем выберите **Регистрация приложений** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="ce191-108">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="ce191-109">Снимок экрана с регистрациями приложений</span><span class="sxs-lookup"><span data-stu-id="ce191-109">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

## <a name="register-an-app-for-the-single-page-application"></a><span data-ttu-id="ce191-110">Регистрация приложения для одностраничного приложения</span><span class="sxs-lookup"><span data-stu-id="ce191-110">Register an app for the single-page application</span></span>

1. <span data-ttu-id="ce191-111">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="ce191-111">Select **New registration**.</span></span> <span data-ttu-id="ce191-112">На странице **Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="ce191-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="ce191-113">Введите **имя** `Graph Azure Function Test App`.</span><span class="sxs-lookup"><span data-stu-id="ce191-113">Set **Name** to `Graph Azure Function Test App`.</span></span>
    - <span data-ttu-id="ce191-114">Установите **Поддерживаемые типы учетных** записей **только для учетных записей в данном организационном каталоге**.</span><span class="sxs-lookup"><span data-stu-id="ce191-114">Set **Supported account types** to **Accounts in this organizational directory only**.</span></span>
    - <span data-ttu-id="ce191-115">В разделе **URI перенаправления** измените раскрывающийся список на **одностраничное приложение (SPA)** и задайте для него значение `http://localhost:8080` .</span><span class="sxs-lookup"><span data-stu-id="ce191-115">Under **Redirect URI** , change the dropdown to **Single-page application (SPA)** and set the value to `http://localhost:8080`.</span></span>

    ![Снимок страницы "регистрация приложения"](./images/register-command-line-app.png)

1. <span data-ttu-id="ce191-117">Нажмите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="ce191-117">Select **Register**.</span></span> <span data-ttu-id="ce191-118">На странице **приложения-приложения Graph Graph** , СКОПИРУЙТЕ значения **идентификатора Application (Client)** и **идентификатора каталога (клиента)** и сохраните их, они понадобятся позже.</span><span class="sxs-lookup"><span data-stu-id="ce191-118">On the **Graph Azure Function Test App** page, copy the values of the **Application (client) ID** and **Directory (tenant) ID** and save them, you will need them in the later steps.</span></span>

    ![Снимок экрана с ИДЕНТИФИКАТОРом приложения для новой регистрации приложения](./images/aad-application-id.png)

## <a name="register-an-app-for-the-azure-function"></a><span data-ttu-id="ce191-120">Регистрация приложения для функции Azure</span><span class="sxs-lookup"><span data-stu-id="ce191-120">Register an app for the Azure Function</span></span>

1. <span data-ttu-id="ce191-121">Вернитесь к **регистрациям приложений** и выберите пункт **создать регистрацию**.</span><span class="sxs-lookup"><span data-stu-id="ce191-121">Return to **App Registrations** , and select **New registration**.</span></span> <span data-ttu-id="ce191-122">На странице **Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="ce191-122">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="ce191-123">Введите **имя** `Graph Azure Function`.</span><span class="sxs-lookup"><span data-stu-id="ce191-123">Set **Name** to `Graph Azure Function`.</span></span>
    - <span data-ttu-id="ce191-124">Установите **Поддерживаемые типы учетных** записей **только для учетных записей в данном организационном каталоге**.</span><span class="sxs-lookup"><span data-stu-id="ce191-124">Set **Supported account types** to **Accounts in this organizational directory only**.</span></span>
    - <span data-ttu-id="ce191-125">Оставьте пустым **URI перенаправления** .</span><span class="sxs-lookup"><span data-stu-id="ce191-125">Leave **Redirect URI** blank.</span></span>

1. <span data-ttu-id="ce191-126">Нажмите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="ce191-126">Select **Register**.</span></span> <span data-ttu-id="ce191-127">На странице " **функция Graph Azure** " СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="ce191-127">On the **Graph Azure Function** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

1. <span data-ttu-id="ce191-128">Выберите **Сертификаты и секреты** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="ce191-128">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="ce191-129">Нажмите кнопку **Новый секрет клиента**.</span><span class="sxs-lookup"><span data-stu-id="ce191-129">Select the **New client secret** button.</span></span> <span data-ttu-id="ce191-130">Введите значение в поле **Описание** и выберите один из вариантов **истечения срока действия** , а затем нажмите кнопку **добавить**.</span><span class="sxs-lookup"><span data-stu-id="ce191-130">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![Снимок экрана: диалоговое окно добавления секрета клиента](./images/aad-new-client-secret.png)

1. <span data-ttu-id="ce191-132">Скопируйте значение секрета клиента, а затем покиньте эту страницу.</span><span class="sxs-lookup"><span data-stu-id="ce191-132">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="ce191-133">Оно вам понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="ce191-133">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="ce191-134">Это секрет клиента, он никогда не отображается еще раз, поэтому убедитесь, что вы скопировали его.</span><span class="sxs-lookup"><span data-stu-id="ce191-134">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Снимок экрана с недавно добавленным секретом клиента](./images/aad-copy-client-secret.png)

1. <span data-ttu-id="ce191-136">Выберите **разрешения API** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="ce191-136">Select **API Permissions** under **Manage**.</span></span> <span data-ttu-id="ce191-137">Нажмите кнопку **Добавить разрешение**.</span><span class="sxs-lookup"><span data-stu-id="ce191-137">Choose **Add a permission**.</span></span>

1. <span data-ttu-id="ce191-138">Выберите **Microsoft Graph** , а затем **делегированные разрешения**.</span><span class="sxs-lookup"><span data-stu-id="ce191-138">Select **Microsoft Graph** , then **Delegated Permissions**.</span></span> <span data-ttu-id="ce191-139">Добавить **почту. Read** и выберите команду **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="ce191-139">Add **Mail.Read** and select **Add permissions**.</span></span>

    ![Снимок экрана с настроенными разрешениями для регистрации приложения функции Azure](./images/web-api-configured-permissions.png)

1. <span data-ttu-id="ce191-141">Выберите **открыть доступ к API** в разделе **Управление** , а затем выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="ce191-141">Select **Expose an API** under **Manage** , then choose **Add a scope**.</span></span>

1. <span data-ttu-id="ce191-142">Примите **URI идентификатора приложения** по умолчанию и нажмите кнопку **сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="ce191-142">Accept the default **Application ID URI** and choose **Save and continue**.</span></span>

1. <span data-ttu-id="ce191-143">Заполните форму **Добавить область** следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ce191-143">Fill in the **Add a scope** form as follows:</span></span>

    - <span data-ttu-id="ce191-144">**Имя области:** Mail. Read</span><span class="sxs-lookup"><span data-stu-id="ce191-144">**Scope name:** Mail.Read</span></span>
    - <span data-ttu-id="ce191-145">**Кто может согласиться?:** Администраторы и пользователи</span><span class="sxs-lookup"><span data-stu-id="ce191-145">**Who can consent?:** Admins and users</span></span>
    - <span data-ttu-id="ce191-146">**Отображаемое имя разрешения администратора:** Чтение входящих в папку "Входящие" пользователей</span><span class="sxs-lookup"><span data-stu-id="ce191-146">**Admin consent display name:** Read all users' inboxes</span></span>
    - <span data-ttu-id="ce191-147">**Описание согласия администратора:** Позволяет приложению считывать папки "Входящие" всех пользователей</span><span class="sxs-lookup"><span data-stu-id="ce191-147">**Admin consent description:** Allows the app to read all users' inboxes</span></span>
    - <span data-ttu-id="ce191-148">**Отображаемое имя согласия пользователя:** Чтение папки "Входящие"</span><span class="sxs-lookup"><span data-stu-id="ce191-148">**User consent display name:** Read your inbox</span></span>
    - <span data-ttu-id="ce191-149">**Описание согласия пользователя:** Позволяет приложению читать папку "Входящие"</span><span class="sxs-lookup"><span data-stu-id="ce191-149">**User consent description:** Allows the app to read your inbox</span></span>
    - <span data-ttu-id="ce191-150">**Состояние:** Доступ</span><span class="sxs-lookup"><span data-stu-id="ce191-150">**State:** Enabled</span></span>

1. <span data-ttu-id="ce191-151">Нажмите кнопку **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="ce191-151">Select **Add scope**.</span></span>

1. <span data-ttu-id="ce191-152">Скопируйте новую область, а затем потребуются ее на последующих этапах.</span><span class="sxs-lookup"><span data-stu-id="ce191-152">Copy the new scope, you'll need it in later steps.</span></span>

    ![Снимок экрана с определенными областями для регистрации приложения функции Azure](./images/web-api-defined-scopes.png)

1. <span data-ttu-id="ce191-154">Выберите **Манифест** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="ce191-154">Select **Manifest** under **Manage**.</span></span>

1. <span data-ttu-id="ce191-155">Выберите `knownClientApplications` в манифесте и замените текущее значение `[]` на `[TEST_APP_ID]` , где `TEST_APP_ID` — это идентификатор приложения, в котором находится приложение **функции Graph Test App Registration App** .</span><span class="sxs-lookup"><span data-stu-id="ce191-155">Locate `knownClientApplications` in the manifest, and replace it's current value of `[]` with `[TEST_APP_ID]`, where `TEST_APP_ID` is the application ID of the **Graph Azure Function Test App** app registration.</span></span> <span data-ttu-id="ce191-156">Выберите **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="ce191-156">Select **Save**.</span></span>

> [!NOTE]
> <span data-ttu-id="ce191-157">Добавление идентификатора приложения тестового приложения в `knownClientApplications` свойство в манифесте функции Azure позволяет тестовому приложению инициировать [общий ход разрешения](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow#default-and-combined-consent).</span><span class="sxs-lookup"><span data-stu-id="ce191-157">Adding the test application's app ID to the `knownClientApplications` property in the Azure Function's manifest allows the test application to trigger a [combined consent flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow#default-and-combined-consent).</span></span> <span data-ttu-id="ce191-158">Это необходимо для работы процесса "от имени".</span><span class="sxs-lookup"><span data-stu-id="ce191-158">This is necessary for the on-behalf-of flow to work.</span></span>

## <a name="add-azure-function-scope-to-test-application-registration"></a><span data-ttu-id="ce191-159">Добавление области функции Azure для проверки регистрации приложения</span><span class="sxs-lookup"><span data-stu-id="ce191-159">Add Azure Function scope to test application registration</span></span>

1. <span data-ttu-id="ce191-160">Вернитесь к **функции Graph Test App** Registration и выберите **разрешения API** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="ce191-160">Return to the **Graph Azure Function Test App** registration, and select **API Permissions** under **Manage**.</span></span> <span data-ttu-id="ce191-161">Выберите **Добавить разрешение**.</span><span class="sxs-lookup"><span data-stu-id="ce191-161">Select **Add a permission**.</span></span>

1. <span data-ttu-id="ce191-162">Выберите **Мои API** , а затем нажмите кнопку **загрузить еще больше**.</span><span class="sxs-lookup"><span data-stu-id="ce191-162">Select **My APIs** , then select **Load more**.</span></span> <span data-ttu-id="ce191-163">Выберите **функция Graph Azure**.</span><span class="sxs-lookup"><span data-stu-id="ce191-163">Select **Graph Azure Function**.</span></span>

    ![Снимок экрана: диалоговое окно разрешений API запроса](./images/test-app-add-permissions.png)

1. <span data-ttu-id="ce191-165">Выберите разрешение **почта. чтение** и нажмите кнопку **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="ce191-165">Select the **Mail.Read** permission, then select **Add permissions**.</span></span>

1. <span data-ttu-id="ce191-166">В разделе **настроенные разрешения** удалите разрешение **User. Read** для **Microsoft Graph** , выбрав элемент **...** справа от разрешения и выбрав пункт **удалить разрешение**.</span><span class="sxs-lookup"><span data-stu-id="ce191-166">In the **Configured permissions** , remove the **User.Read** permission under **Microsoft Graph** by selecting the **...** to the right of the permission and selecting **Remove permission**.</span></span> <span data-ttu-id="ce191-167">Нажмите кнопку **Да, удалить** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="ce191-167">Select **Yes, remove** to confirm.</span></span>

    ![Снимок экрана с настроенными разрешениями для проверки регистрации приложения приложения](./images/test-app-configured-permissions.png)

## <a name="register-an-app-for-the-azure-function-webhook"></a><span data-ttu-id="ce191-169">Регистрация приложения для веб-перехватчика функции Azure</span><span class="sxs-lookup"><span data-stu-id="ce191-169">Register an app for the Azure Function webhook</span></span>

1. <span data-ttu-id="ce191-170">Вернитесь к **регистрациям приложений** и выберите пункт **создать регистрацию**.</span><span class="sxs-lookup"><span data-stu-id="ce191-170">Return to **App Registrations** , and select **New registration**.</span></span> <span data-ttu-id="ce191-171">На странице **Зарегистрировать приложение** задайте необходимые значения следующим образом.</span><span class="sxs-lookup"><span data-stu-id="ce191-171">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="ce191-172">Введите **имя** `Graph Azure Function Webhook`.</span><span class="sxs-lookup"><span data-stu-id="ce191-172">Set **Name** to `Graph Azure Function Webhook`.</span></span>
    - <span data-ttu-id="ce191-173">Установите **Поддерживаемые типы учетных** записей **только для учетных записей в данном организационном каталоге**.</span><span class="sxs-lookup"><span data-stu-id="ce191-173">Set **Supported account types** to **Accounts in this organizational directory only**.</span></span>
    - <span data-ttu-id="ce191-174">Оставьте пустым **URI перенаправления** .</span><span class="sxs-lookup"><span data-stu-id="ce191-174">Leave **Redirect URI** blank.</span></span>

1. <span data-ttu-id="ce191-175">Нажмите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="ce191-175">Select **Register**.</span></span> <span data-ttu-id="ce191-176">На странице веб- **перехватчик функции Graph Azure** СКОПИРУЙТЕ значение **идентификатора Application (Client)** и сохраните его, он понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="ce191-176">On the **Graph Azure Function webhook** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

1. <span data-ttu-id="ce191-177">Выберите **Сертификаты и секреты** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="ce191-177">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="ce191-178">Нажмите кнопку **Новый секрет клиента**.</span><span class="sxs-lookup"><span data-stu-id="ce191-178">Select the **New client secret** button.</span></span> <span data-ttu-id="ce191-179">Введите значение в поле **Описание** и выберите один из вариантов **истечения срока действия** , а затем нажмите кнопку **добавить**.</span><span class="sxs-lookup"><span data-stu-id="ce191-179">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

1. <span data-ttu-id="ce191-180">Скопируйте значение секрета клиента, а затем покиньте эту страницу.</span><span class="sxs-lookup"><span data-stu-id="ce191-180">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="ce191-181">Оно вам понадобится на следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="ce191-181">You will need it in the next step.</span></span>

1. <span data-ttu-id="ce191-182">Выберите **разрешения API** в разделе **Управление**.</span><span class="sxs-lookup"><span data-stu-id="ce191-182">Select **API Permissions** under **Manage**.</span></span> <span data-ttu-id="ce191-183">Нажмите кнопку **Добавить разрешение**.</span><span class="sxs-lookup"><span data-stu-id="ce191-183">Choose **Add a permission**.</span></span>

1. <span data-ttu-id="ce191-184">Выберите **Microsoft Graph** , а затем — **разрешения приложений**.</span><span class="sxs-lookup"><span data-stu-id="ce191-184">Select **Microsoft Graph** , then **Application Permissions**.</span></span> <span data-ttu-id="ce191-185">Добавьте **User. Read. ALL** и **mail. Read** , а затем выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="ce191-185">Add **User.Read.All** and **Mail.Read** , then select **Add permissions**.</span></span>

1. <span data-ttu-id="ce191-186">В **настроенных разрешениях** удалите делегированного **пользователя.** разрешение на чтение **в Microsoft Graph** , выбрав элемент **...** справа от разрешения и выбрав пункт **удалить разрешение**.</span><span class="sxs-lookup"><span data-stu-id="ce191-186">In the **Configured permissions** , remove the delegated **User.Read** permission under **Microsoft Graph** by selecting the **...** to the right of the permission and selecting **Remove permission**.</span></span> <span data-ttu-id="ce191-187">Нажмите кнопку **Да, удалить** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="ce191-187">Select **Yes, remove** to confirm.</span></span>

1. <span data-ttu-id="ce191-188">Нажмите кнопку **предоставить согласие администратора для...** , а затем выберите **Да** , чтобы предоставить согласие администратора для настроенных разрешений приложения.</span><span class="sxs-lookup"><span data-stu-id="ce191-188">Select the **Grant admin consent for...** button, then select **Yes** to grant admin consent for the configured application permissions.</span></span> <span data-ttu-id="ce191-189">В столбце **состояние** в **настроенной таблице разрешений** изменяется значение **предоставлено для...**.</span><span class="sxs-lookup"><span data-stu-id="ce191-189">The **Status** column in the **Configured permissions** table changes to **Granted for ...**.</span></span>

    ![Снимок экрана с настроенными разрешениями для веб-перехватчика с предоставлением разрешения администратора](./images/webhook-configured-permissions.png)
