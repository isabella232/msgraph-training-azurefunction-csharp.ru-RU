---
ms.openlocfilehash: 1e4267a5d8bdfe02dbc0170122113c35df822659
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822689"
---
<!-- markdownlint-disable MD002 MD041 -->

В этом руководстве описывается создание функции Azure, которая использует API Microsoft Graph для получения сведений о календаре для пользователя.

> [!TIP]
> Если вы предпочитаете просто скачать заполненный учебник, вы можете скачать или клонировать [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp). Ознакомьтесь с файлом README в папке **Demo** , чтобы получить инструкции по настройке приложения с идентификатором и секретом приложения.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к работе с этим руководством, на компьютере для разработки должны быть установлены следующие средства.

- [Пакет SDK для .NET Core](https://dotnet.microsoft.com/download)
- [Основные средства функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-run-local)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [ngrok](https://ngrok.com/)

Кроме того, у вас должна быть рабочая или учебная учетная запись Майкрософт с доступом к учетной записи глобального администратора в той же организации. Если у вас нет учетной записи Майкрософт, вы можете [зарегистрироваться в программе для разработчиков office 365](https://developer.microsoft.com/office/dev-program) , чтобы получить бесплатную подписку на Office 365.

> [!NOTE]
> Это руководство было написано в следующих версиях этих средств. Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.
>
> - Пакет SDK для .NET Core 3.1.301
> - 3.0.2630 основных средств для функций Azure
> - 2.8.0 инфраструктуры Azure
> - ngrok 2.3.35

## <a name="feedback"></a>Отзывы

Сообщите о нем в [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).
