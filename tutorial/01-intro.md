---
ms.openlocfilehash: 1e4267a5d8bdfe02dbc0170122113c35df822659
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822689"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="dba41-101">В этом руководстве описывается создание функции Azure, которая использует API Microsoft Graph для получения сведений о календаре для пользователя.</span><span class="sxs-lookup"><span data-stu-id="dba41-101">This tutorial teaches you how to build an Azure Function that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="dba41-102">Если вы предпочитаете просто скачать заполненный учебник, вы можете скачать или клонировать [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).</span><span class="sxs-lookup"><span data-stu-id="dba41-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).</span></span> <span data-ttu-id="dba41-103">Ознакомьтесь с файлом README в папке **Demo** , чтобы получить инструкции по настройке приложения с идентификатором и секретом приложения.</span><span class="sxs-lookup"><span data-stu-id="dba41-103">See the README file in the **demo** folder for instructions on configuring the app with an app ID and secret.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dba41-104">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="dba41-104">Prerequisites</span></span>

<span data-ttu-id="dba41-105">Прежде чем приступить к работе с этим руководством, на компьютере для разработки должны быть установлены следующие средства.</span><span class="sxs-lookup"><span data-stu-id="dba41-105">Before you start this tutorial, you should have the following tools installed on your development machine.</span></span>

- [<span data-ttu-id="dba41-106">Пакет SDK для .NET Core</span><span class="sxs-lookup"><span data-stu-id="dba41-106">.NET Core SDK</span></span>](https://dotnet.microsoft.com/download)
- [<span data-ttu-id="dba41-107">Основные средства функций Azure</span><span class="sxs-lookup"><span data-stu-id="dba41-107">Azure Functions Core Tools</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-run-local)
- [<span data-ttu-id="dba41-108">Azure CLI</span><span class="sxs-lookup"><span data-stu-id="dba41-108">Azure CLI</span></span>](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [<span data-ttu-id="dba41-109">ngrok</span><span class="sxs-lookup"><span data-stu-id="dba41-109">ngrok</span></span>](https://ngrok.com/)

<span data-ttu-id="dba41-110">Кроме того, у вас должна быть рабочая или учебная учетная запись Майкрософт с доступом к учетной записи глобального администратора в той же организации.</span><span class="sxs-lookup"><span data-stu-id="dba41-110">You should also have a Microsoft work or school account, with access to a global administrator account in the same organization.</span></span> <span data-ttu-id="dba41-111">Если у вас нет учетной записи Майкрософт, вы можете [зарегистрироваться в программе для разработчиков office 365](https://developer.microsoft.com/office/dev-program) , чтобы получить бесплатную подписку на Office 365.</span><span class="sxs-lookup"><span data-stu-id="dba41-111">If you don't have a Microsoft account, you can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="dba41-112">Это руководство было написано в следующих версиях этих средств.</span><span class="sxs-lookup"><span data-stu-id="dba41-112">This tutorial was written with the following versions of the above tools.</span></span> <span data-ttu-id="dba41-113">Действия, описанные в этом руководстве, могут работать с другими версиями, но не тестировались.</span><span class="sxs-lookup"><span data-stu-id="dba41-113">The steps in this guide may work with other versions, but that has not been tested.</span></span>
>
> - <span data-ttu-id="dba41-114">Пакет SDK для .NET Core 3.1.301</span><span class="sxs-lookup"><span data-stu-id="dba41-114">.NET Core SDK 3.1.301</span></span>
> - <span data-ttu-id="dba41-115">3.0.2630 основных средств для функций Azure</span><span class="sxs-lookup"><span data-stu-id="dba41-115">Azure Functions Core Tools 3.0.2630</span></span>
> - <span data-ttu-id="dba41-116">2.8.0 инфраструктуры Azure</span><span class="sxs-lookup"><span data-stu-id="dba41-116">Azure CLI 2.8.0</span></span>
> - <span data-ttu-id="dba41-117">ngrok 2.3.35</span><span class="sxs-lookup"><span data-stu-id="dba41-117">ngrok 2.3.35</span></span>

## <a name="feedback"></a><span data-ttu-id="dba41-118">Отзывы</span><span class="sxs-lookup"><span data-stu-id="dba41-118">Feedback</span></span>

<span data-ttu-id="dba41-119">Сообщите о нем в [репозиторий GitHub](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).</span><span class="sxs-lookup"><span data-stu-id="dba41-119">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-azurefunction-csharp).</span></span>
