---
ms.openlocfilehash: fbd43b4708cd1aeaad93b70fcf151869bd9e543e
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822710"
---
<!-- markdownlint-disable MD002 MD041 -->

В этом упражнении вы узнаете, какие изменения необходимы для примера функции Azure, чтобы подготовиться к [публикации в приложении функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local#publish).

## <a name="update-code"></a>Обновление кода

Конфигурация считывается из хранилища секрета пользователя, которое применяется только к компьютеру, на котором выполняется разработка. Перед публикацией в Azure необходимо изменить место хранения конфигурации и соответствующим образом обновить код в **Startup.CS** .

Секреты приложений должны храниться в безопасном хранилище, таком как [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview).

## <a name="update-cors-setting-for-azure-function"></a>Обновление параметра CORS для функции Azure

В этом примере мы настроили CORS в **local.settings.json** для того, чтобы тестовое приложение вызывало функцию. Вам потребуется настроить опубликованную функцию, чтобы разрешить все приложения SPA, которые будут вызывать ее.

## <a name="update-app-registrations"></a>Обновление регистраций приложений

`knownClientApplications`Свойство в манифесте для регистрации приложения **функции Graph Azure** необходимо обновить с идентификаторами всех приложений, которые будут вызывать функцию Azure.

## <a name="recreate-existing-subscriptions"></a>Повторное создание существующих подписок

Все подписки, созданные с помощью URL-адреса веб-перехватчика на локальном компьютере или ngrok, необходимо повторно создать с помощью рабочего URL-адреса `Notify` функции Azure.
