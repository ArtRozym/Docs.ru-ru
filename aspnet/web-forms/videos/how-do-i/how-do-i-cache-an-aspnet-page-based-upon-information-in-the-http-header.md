---
uid: web-forms/videos/how-do-i/how-do-i-cache-an-aspnet-page-based-upon-information-in-the-http-header
title: '[Инструкции]  Страницы ASP.NET на основе сведений в заголовке HTTP кэша | Документация Майкрософт'
author: rick-anderson
description: В этом видео Крис Пелз показано, как сохранить страницы в кэше вывода ASP.NET на основе сведений в заголовке HTTP. Во-первых, потенциальные верхни HTTP...
ms.author: riande
ms.date: 02/26/2009
ms.assetid: 0f8df1bd-080a-4eeb-980c-c2fbb05d30c2
msc.legacyurl: /web-forms/videos/how-do-i/how-do-i-cache-an-aspnet-page-based-upon-information-in-the-http-header
msc.type: video
ms.openlocfilehash: c90a3db1357df062909ad0e3b73fdeeb3dc16329
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41837247"
---
<a name="how-do-i--cache-an-aspnet-page-based-upon-information-in-the-http-header"></a><span data-ttu-id="16f01-104">[Инструкции]  Кэш страницы ASP.NET на основе сведений в заголовке HTTP</span><span class="sxs-lookup"><span data-stu-id="16f01-104">[How Do I:]  Cache an ASP.NET Page Based Upon Information in the HTTP Header</span></span>
====================
<span data-ttu-id="16f01-105">по [Крис Пелз](https://twitter.com/chrispels)</span><span class="sxs-lookup"><span data-stu-id="16f01-105">by [Chris Pels](https://twitter.com/chrispels)</span></span>

<span data-ttu-id="16f01-106">В этом видео Крис Пелз показано, как сохранить страницы в кэше вывода ASP.NET на основе сведений в заголовке HTTP.</span><span class="sxs-lookup"><span data-stu-id="16f01-106">In this video Chris Pels shows how to keep a page in the ASP.NET output cache based upon information in the page's HTTP header.</span></span> <span data-ttu-id="16f01-107">Во-первых рассмотрены потенциальные значения заголовка HTTP.</span><span class="sxs-lookup"><span data-stu-id="16f01-107">First, the potential HTTP header values are reviewed.</span></span> <span data-ttu-id="16f01-108">Затем пример страницы создается и используется директива OutputCache VaryByHeader атрибут, который содержит значение «принять язык», заголовок HTTP для управления кэшированием в зависимости от языка браузера пользователя.</span><span class="sxs-lookup"><span data-stu-id="16f01-108">Then, a sample page is created and then the OutputCache directive is used with the VaryByHeader attribute which contains a value "accept-language", an HTTP header, to control caching based upon the language of the user's browser.</span></span> <span data-ttu-id="16f01-109">На странице примеров просмотре в Internet Explorer, настроенная на английском языке, а затем в FireFox, настроенная на использование французского языка.</span><span class="sxs-lookup"><span data-stu-id="16f01-109">The sample page is viewed in IE which is set to English and then in FireFox which is set to use French.</span></span> <span data-ttu-id="16f01-110">Наконец рассматривается возможность переместите определение кэша CacheProfile в файле web.config.</span><span class="sxs-lookup"><span data-stu-id="16f01-110">Finally, the option to move the cache definition to a CacheProfile in the web.config file is discussed.</span></span>

[<span data-ttu-id="16f01-111">&#9654;Просмотрите видео (12 минут)</span><span class="sxs-lookup"><span data-stu-id="16f01-111">&#9654; Watch video (12 minutes)</span></span>](https://channel9.msdn.com/Blogs/ASP-NET-Site-Videos/how-do-i-cache-an-aspnet-page-based-upon-information-in-the-http-header)
