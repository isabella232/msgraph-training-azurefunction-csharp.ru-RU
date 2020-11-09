---
ms.openlocfilehash: 611fb4171d0b9b14f1019aa0852c19222cdea501
ms.sourcegitcommit: 57a5c2e1a562d8af092a3e78786d711ce1e8f9cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822668"
---
# <a name="contributing-to-microsoft-graph-training-repositories"></a><span data-ttu-id="bb90f-101">Дополнение к репозиториям обучения Microsoft Graph</span><span class="sxs-lookup"><span data-stu-id="bb90f-101">Contributing to Microsoft Graph training repositories</span></span>

<span data-ttu-id="bb90f-102">Благодарим вас за участие в этом проекте!</span><span class="sxs-lookup"><span data-stu-id="bb90f-102">Thank you for contributing to this project!</span></span> <span data-ttu-id="bb90f-103">Прежде чем отправлять запрос на включение внесенных изменений, необходимо учесть следующие рекомендации.</span><span class="sxs-lookup"><span data-stu-id="bb90f-103">Before submitting your pull request, be sure to consider the following.</span></span>

## <a name="overview"></a><span data-ttu-id="bb90f-104">Обзор</span><span class="sxs-lookup"><span data-stu-id="bb90f-104">Overview</span></span>

<span data-ttu-id="bb90f-105">Код в этом репозитории выполняет три задачи:</span><span class="sxs-lookup"><span data-stu-id="bb90f-105">The code in this repository serves three purposes:</span></span>

- <span data-ttu-id="bb90f-106">Файлы Markdown в папке [Tutorial](/tutorial) публикуются в учебнике на странице [учебников по Microsoft Graph](https://docs.microsoft.com/graph/tutorials) .</span><span class="sxs-lookup"><span data-stu-id="bb90f-106">The Markdown files in the [tutorial](/tutorial) folder are published as a tutorial on the [Microsoft Graph tutorials](https://docs.microsoft.com/graph/tutorials) page.</span></span>
- <span data-ttu-id="bb90f-107">Пример проекта в папке [Demo](/demo) — это источник [быстрого запуска Microsoft Graph](https://developer.microsoft.com/graph/quick-start). \* *\** _</span><span class="sxs-lookup"><span data-stu-id="bb90f-107">The sample project in the [demo](/demo) folder is the source for a [Microsoft Graph quick start](https://developer.microsoft.com/graph/quick-start).\* *\** _</span></span>
- <span data-ttu-id="bb90f-108">Пример проекта в папке demo также можно скачать непосредственно из GitHub и запускать без проверки после некоторой простой настройки.</span><span class="sxs-lookup"><span data-stu-id="bb90f-108">The sample project in the demo folder is also downloadable directly from GitHub and should run as-is after some simple configuration.</span></span>

> <span data-ttu-id="bb90f-109">_*\**_ Не все учебные репозитории доступны в качестве быстрого запуска (пока).</span><span class="sxs-lookup"><span data-stu-id="bb90f-109">_*\**_ Not all training repositories are available as quick starts (yet).</span></span>

<span data-ttu-id="bb90f-110">Это важно помнить, так как изменения в одном месте _may \* требуют изменений в другой, чтобы обеспечить синхронизацию. Вхеривер возможно, файлы Markdown ссылаются на файлы исходного кода напрямую (с помощью пользовательского `:::code` синтаксиса), поэтому обновление кода в исходном коде будет автоматически обновлять код в Markdown.</span><span class="sxs-lookup"><span data-stu-id="bb90f-110">This is important to keep in mind, because changes in one place _may\* require changes in another, to keep things in sync. Whereever possible, the Markdown files refer to the source code files directly (using a custom `:::code` syntax), so that updating code in source will automatically update the code in Markdown.</span></span>

## <a name="updating-code"></a><span data-ttu-id="bb90f-111">Обновление кода</span><span class="sxs-lookup"><span data-stu-id="bb90f-111">Updating code</span></span>

<span data-ttu-id="bb90f-112">`:::code`Синтаксис, используемый в Markdown, зависит от определенных комментариев в файле исходного кода.</span><span class="sxs-lookup"><span data-stu-id="bb90f-112">The `:::code` syntax used in Markdown depends on specific comments in the source code file.</span></span> <span data-ttu-id="bb90f-113">Эти комментарии выглядят следующим образом:</span><span class="sxs-lookup"><span data-stu-id="bb90f-113">These comments look like the following:</span></span>

```csharp
// <MySnippet>
Console.WriteLine("Hello World!");
// </MySnippet>
```

<span data-ttu-id="bb90f-114">Если вы обновляете код между этими комментариями "маркера", файлы Markdown будут автоматически получать эти изменения при публикации на сайте документации Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="bb90f-114">If you update code between these "marker" comments, the Markdown files will automatically get those changes when published to the Microsoft Graph documentation site.</span></span> <span data-ttu-id="bb90f-115">Если вы обновляете код за прерядом этих комментариев, вероятно, вам потребуется обновить соответствующие Markdown.</span><span class="sxs-lookup"><span data-stu-id="bb90f-115">If you update code outside of those comments, it's very likely that you'll need to update the corresponding Markdown.</span></span>

## <a name="adding-features"></a><span data-ttu-id="bb90f-116">Добавление компонентов</span><span class="sxs-lookup"><span data-stu-id="bb90f-116">Adding features</span></span>

<span data-ttu-id="bb90f-117">Когда этом мы ценим, не отправляйте запросы на включение внесенных изменений, чтобы добавить новые функции в пример.</span><span class="sxs-lookup"><span data-stu-id="bb90f-117">While the enthusiasm is appreciated, please don't send pull requests to add new features to the sample.</span></span> <span data-ttu-id="bb90f-118">Так как этот репозиторий в основном является "построение вашего первого приложения", набор функций ограничен в соответствии с проектированием.</span><span class="sxs-lookup"><span data-stu-id="bb90f-118">Because this repository is primarily a "build your first app" tutorial, the feature set is limited, by design.</span></span>

## <a name="submitting-pull-requests"></a><span data-ttu-id="bb90f-119">Отправка запросов на включение внесенных изменений</span><span class="sxs-lookup"><span data-stu-id="bb90f-119">Submitting pull requests</span></span>

<span data-ttu-id="bb90f-120">Отправьте все запросы на включение внесенных изменений в `master` ветвь.</span><span class="sxs-lookup"><span data-stu-id="bb90f-120">Please submit all pull requests to the `master` branch.</span></span>

<!-- markdownlint-disable MD026 -->
## <a name="when-do-changes-get-published"></a><span data-ttu-id="bb90f-121">Когда будут опубликованы изменения?</span><span class="sxs-lookup"><span data-stu-id="bb90f-121">When do changes get published?</span></span>
<!-- markdownlint-enable MD026 -->

<span data-ttu-id="bb90f-122">Публикация обновлений на сайте [учебников по Microsoft Graph](https://docs.microsoft.com/graph/tutorials) не выполняется автоматически.</span><span class="sxs-lookup"><span data-stu-id="bb90f-122">Publishing of updates to the [Microsoft Graph tutorials](https://docs.microsoft.com/graph/tutorials) site is not automatic.</span></span> <span data-ttu-id="bb90f-123">Сначала необходимо повысить уровень изменений до `live` ветви, а затем запустить сборку с помощью администраторов сайта.</span><span class="sxs-lookup"><span data-stu-id="bb90f-123">Changes must first be promoted to the `live` branch, then a build must be triggered by the site admins.</span></span> <span data-ttu-id="bb90f-124">Как правило, это делается на основе "по мере необходимости".</span><span class="sxs-lookup"><span data-stu-id="bb90f-124">This is typically done on an "as-needed" basis.</span></span>

## <a name="code-of-conduct"></a><span data-ttu-id="bb90f-125">Правила поведения</span><span class="sxs-lookup"><span data-stu-id="bb90f-125">Code of conduct</span></span>

<span data-ttu-id="bb90f-p106">Этот проект соответствует [правилам поведения Майкрософт, касающимся обращения с открытым кодом](https://opensource.microsoft.com/codeofconduct/). Читайте дополнительные сведения в [разделе вопросов и ответов по правилам поведения](https://opensource.microsoft.com/codeofconduct/faq/) или отправляйте новые вопросы и замечания по адресу [opencode@microsoft.com](mailto:opencode@microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="bb90f-p106">This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.</span></span>
