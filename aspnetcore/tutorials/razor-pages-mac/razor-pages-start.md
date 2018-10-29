---
title: Начало работы с Razor Pages в ASP.NET Core в macOS с использованием Visual Studio для Mac
author: rick-anderson
description: Узнайте, как начать работу с Razor Pages в ASP.NET Core с использованием Visual Studio для Mac.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: tutorials/razor-pages-mac/razor-pages-start
ms.openlocfilehash: 8a4cc6c52684558ce9176f98205e439096e88cbf
ms.sourcegitcommit: 4d74644f11e0dac52b4510048490ae731c691496
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50089655"
---
# <a name="get-started-with-razor-pages-in-aspnet-core-on-macos-with-visual-studio-for-mac"></a><span data-ttu-id="839ea-103">Начало работы с Razor Pages в ASP.NET Core в macOS с использованием Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="839ea-103">Get started with Razor Pages in ASP.NET Core on macOS with Visual Studio for Mac</span></span>

<span data-ttu-id="839ea-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="839ea-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="839ea-105">В этом учебнике приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="839ea-105">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span> <span data-ttu-id="839ea-106">Перед работой с этим учебником рекомендуем изучить статью [Введение в Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="839ea-106">We recommend you review [Introduction to Razor Pages](xref:razor-pages/index) before starting this tutorial.</span></span> <span data-ttu-id="839ea-107">Razor Pages — это рекомендуемый способ создания пользовательского интерфейса для веб-приложений в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="839ea-107">Razor Pages is the recommended way to build UI for web applications in ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="839ea-108">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="839ea-108">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-prereqs-macos.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="839ea-109">Создание веб-приложения Razor</span><span class="sxs-lookup"><span data-stu-id="839ea-109">Create a Razor web app</span></span>

<span data-ttu-id="839ea-110">Из терминала выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="839ea-110">From a terminal, run the following commands:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```console
dotnet new razor -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

::: moniker-end

<span data-ttu-id="839ea-111">Указанные выше команды используют [интерфейс командной строки .NET Core](/dotnet/core/tools/dotnet) для создания и запуска проекта Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="839ea-111">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create and run a Razor Pages project.</span></span> <span data-ttu-id="839ea-112">Откройте в браузере страницу http://localhost:5000 для просмотра приложения.</span><span class="sxs-lookup"><span data-stu-id="839ea-112">Open a browser to http://localhost:5000 to view the application.</span></span>

![Домашняя или индексная страница](../razor-pages/razor-pages-start/_static/home.png)

[!INCLUDE [razor-pages-start](../../includes/RP/razor-pages-start.md)]

## <a name="open-the-project"></a><span data-ttu-id="839ea-114">Открытие проекта</span><span class="sxs-lookup"><span data-stu-id="839ea-114">Open the project</span></span>

<span data-ttu-id="839ea-115">Нажмите клавиши CTRL+C, чтобы завершить работу приложения.</span><span class="sxs-lookup"><span data-stu-id="839ea-115">Press Ctrl+C to shut down the application.</span></span>

<span data-ttu-id="839ea-116">В Visual Studio откройте меню **Файл > Открыть файл** и выберите файл *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="839ea-116">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="839ea-117">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="839ea-117">Launch the app</span></span>

<span data-ttu-id="839ea-118">В Visual Studio выберите **Выполнить > Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="839ea-118">In Visual Studio, select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="839ea-119">Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="839ea-119">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5000`.</span></span>

<span data-ttu-id="839ea-120">В следующем учебнике мы добавим в проект модель.</span><span class="sxs-lookup"><span data-stu-id="839ea-120">In the next tutorial, we add a model to the project.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="839ea-121">Далее: добавление модели</span><span class="sxs-lookup"><span data-stu-id="839ea-121">Next: Adding a model</span></span>](xref:tutorials/razor-pages-mac/model)
