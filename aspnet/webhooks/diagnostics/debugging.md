---
uid: webhooks/diagnostics/debugging
title: Веб-перехватчики ASP.NET Отладка | Документация Майкрософт
author: rick-anderson
description: Способы отладки ASP.NET веб-перехватчики.
ms.author: riande
ms.date: 01/17/2012
ms.assetid: 467da78b-3c35-4c51-8b08-77a32379e4a8
ms.openlocfilehash: 517d282fc22703b5861b748aea51023fa0a12a26
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41837056"
---
# <a name="aspnet-webhooks-debugging"></a><span data-ttu-id="0082c-103">Веб-перехватчики ASP.NET отладки</span><span class="sxs-lookup"><span data-stu-id="0082c-103">ASP.NET WebHooks debugging</span></span>  

## <a name="debugging-in-azure"></a><span data-ttu-id="0082c-104">Отладка в Azure</span><span class="sxs-lookup"><span data-stu-id="0082c-104">Debugging in Azure</span></span>

<span data-ttu-id="0082c-105">Отладка веб-приложения во время выполнения в Azure, см. в разделе учебника [Устранение неполадок веб-приложения в службе приложений Azure с помощью Visual Studio](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#webserverlogs).</span><span class="sxs-lookup"><span data-stu-id="0082c-105">To debug your Web Application while running in Azure, please see the tutorial [Troubleshoot a web app in Azure App Service using Visual Studio](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#webserverlogs).</span></span>

## <a name="debugging-with-source-and-symbols"></a><span data-ttu-id="0082c-106">Отладка с помощью исходного кода и символов</span><span class="sxs-lookup"><span data-stu-id="0082c-106">Debugging with Source and Symbols</span></span>

<span data-ttu-id="0082c-107">В дополнение к отладке кода, можно отлаживать непосредственно в веб-перехватчики Microsoft ASP.NET и на самом деле все .NET.</span><span class="sxs-lookup"><span data-stu-id="0082c-107">In addition to debugging your own code, it is possible to debug directly into Microsoft ASP.NET WebHooks, and in fact all of .NET.</span></span> <span data-ttu-id="0082c-108">Это работает независимо от того, является ли отладка локально или удаленно.</span><span class="sxs-lookup"><span data-stu-id="0082c-108">This works regardless of whether you debug locally or remotely.</span></span> <span data-ttu-id="0082c-109">Во-первых, настроить Visual Studio, чтобы найти источник и символы в **Отладка** и затем **параметры и настройки**.</span><span class="sxs-lookup"><span data-stu-id="0082c-109">First, configure Visual Studio to find the source and symbols by going to **Debug** and then **Options and Settings**.</span></span> <span data-ttu-id="0082c-110">Задание параметров следующим образом:</span><span class="sxs-lookup"><span data-stu-id="0082c-110">Set the options like this:</span></span>

![Параметры и настройки](_static/SourceSymbols.png)

<span data-ttu-id="0082c-112">Затем добавьте ссылку на [symbolsource.org](http://symbolsource.org) для загрузки исходного кода и символов.</span><span class="sxs-lookup"><span data-stu-id="0082c-112">Then add a link to [symbolsource.org](http://symbolsource.org) for downloading the source and symbols.</span></span> <span data-ttu-id="0082c-113">Перейдите к **символы** вкладке меню вверху и добавьте следующее расположение символов:</span><span class="sxs-lookup"><span data-stu-id="0082c-113">Go to the **Symbols** tab of the menu above and add the following as a symbol location:</span></span>

```
http://srv.symbolsource.org/pdb/Public
```

<span data-ttu-id="0082c-114">Кроме того убедитесь, что каталог кэша имеет короткое имя; в противном случае имена файлов можно получить слишком длинное вызывающее символы, не загружаются.</span><span class="sxs-lookup"><span data-stu-id="0082c-114">In addition, make sure that the cache directory has a short name; otherwise the file names can get too long which will cause the symbols to not load.</span></span> <span data-ttu-id="0082c-115">— Пример пути:</span><span class="sxs-lookup"><span data-stu-id="0082c-115">A sample path is:</span></span>

```
C:\SymCache
```

<span data-ttu-id="0082c-116">Параметры должны выглядеть примерно следующим образом:</span><span class="sxs-lookup"><span data-stu-id="0082c-116">The settings should look similar to this:</span></span>

![Пример расположения файла параметры символов](_static/SymSource.png)
