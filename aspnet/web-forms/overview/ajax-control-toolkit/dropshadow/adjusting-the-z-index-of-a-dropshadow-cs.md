---
uid: web-forms/overview/ajax-control-toolkit/dropshadow/adjusting-the-z-index-of-a-dropshadow-cs
title: Настройка Z-индекса DropShadow (C#) | Документация Майкрософт
author: wenz
description: Элемент управления DropShadow в AJAX Control Toolkit расширяет панель с эффектом отбрасывания тени. Тем не менее Эта теневая иногда конфликтует с другими элементами управления, для пап...
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 14133833-e518-4347-87b9-6b6f71f14a77
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/dropshadow/adjusting-the-z-index-of-a-dropshadow-cs
msc.type: authoredcontent
ms.openlocfilehash: 5cee2acfac74c0790a7ad82bbfb503a8a16f6b47
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41837807"
---
<a name="adjusting-the-z-index-of-a-dropshadow-c"></a><span data-ttu-id="44295-104">Настройка Z-индекса DropShadow (C#)</span><span class="sxs-lookup"><span data-stu-id="44295-104">Adjusting the Z-Index of a DropShadow (C#)</span></span>
====================
<span data-ttu-id="44295-105">по [Кристиан Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="44295-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="44295-106">[Скачать код](http://download.microsoft.com/download/5/1/6/51652a81-500b-4f6b-88d3-617103e7941e/DropShadow1.cs.zip) или [скачать PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dropshadow1CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="44295-106">[Download Code](http://download.microsoft.com/download/5/1/6/51652a81-500b-4f6b-88d3-617103e7941e/DropShadow1.cs.zip) or [Download PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/dropshadow1CS.pdf)</span></span>

> <span data-ttu-id="44295-107">Элемент управления DropShadow в AJAX Control Toolkit расширяет панель с эффектом отбрасывания тени.</span><span class="sxs-lookup"><span data-stu-id="44295-107">The DropShadow control in the AJAX Control Toolkit extends a panel with a drop shadow.</span></span> <span data-ttu-id="44295-108">Тем не менее Эта теневая иногда конфликтует с другими элементами управления, например элемент управления ASP.NET Menu.</span><span class="sxs-lookup"><span data-stu-id="44295-108">However this shadow sometimes conflicts with other controls, for instance the ASP.NET Menu control.</span></span> <span data-ttu-id="44295-109">Когда запись меню появляется его фоне тени.</span><span class="sxs-lookup"><span data-stu-id="44295-109">When a menu entry pops up, it appears behind the drop shadow.</span></span>


## <a name="overview"></a><span data-ttu-id="44295-110">Обзор</span><span class="sxs-lookup"><span data-stu-id="44295-110">Overview</span></span>

<span data-ttu-id="44295-111">Элемент управления DropShadow в AJAX Control Toolkit расширяет панель с эффектом отбрасывания тени.</span><span class="sxs-lookup"><span data-stu-id="44295-111">The DropShadow control in the AJAX Control Toolkit extends a panel with a drop shadow.</span></span> <span data-ttu-id="44295-112">Тем не менее Эта теневая иногда конфликтует с другими элементами управления, например элемент управления ASP.NET Menu.</span><span class="sxs-lookup"><span data-stu-id="44295-112">However this shadow sometimes conflicts with other controls, for instance the ASP.NET Menu control.</span></span> <span data-ttu-id="44295-113">Когда запись меню появляется его фоне тени.</span><span class="sxs-lookup"><span data-stu-id="44295-113">When a menu entry pops up, it appears behind the drop shadow.</span></span>

## <a name="steps"></a><span data-ttu-id="44295-114">Шаги</span><span class="sxs-lookup"><span data-stu-id="44295-114">Steps</span></span>

<span data-ttu-id="44295-115">Код начинается с элемента управления, содержащий достаточного объема текста, чтобы панель содержит достаточного объема текста для эффекта, который должен отображаться:</span><span class="sxs-lookup"><span data-stu-id="44295-115">The code commences with the Panel itself, containing enough text so that the panel contains enough text for the effect to be visible:</span></span>

[!code-aspx[Main](adjusting-the-z-index-of-a-dropshadow-cs/samples/sample1.aspx)]

<span data-ttu-id="44295-116">Другую панель помещается непосредственно перед `panelShadow` панели.</span><span class="sxs-lookup"><span data-stu-id="44295-116">Another panel is placed directly before the `panelShadow` panel.</span></span> <span data-ttu-id="44295-117">Она включает меню в горизонтальном направлении, чтобы пункты меню отображаются над (вернее: в разделе) `dropShadow` панель):</span><span class="sxs-lookup"><span data-stu-id="44295-117">It contains a menu with horizontal orientation so that menu entries appear over (or rather: under) the `dropShadow` panel):</span></span>

[!code-aspx[Main](adjusting-the-z-index-of-a-dropshadow-cs/samples/sample2.aspx)]

<span data-ttu-id="44295-118">Затем `DropShadowExtender` добавляется расширение `panelShadow` панели с помощью эффекта тени:</span><span class="sxs-lookup"><span data-stu-id="44295-118">Then, the `DropShadowExtender` is added to extend the `panelShadow` panel with a drop shadow effect:</span></span>

[!code-aspx[Main](adjusting-the-z-index-of-a-dropshadow-cs/samples/sample3.aspx)]

<span data-ttu-id="44295-119">Наконец, AJAX ASP.NET `ScriptManager` управления включает набор средств управления для работы:</span><span class="sxs-lookup"><span data-stu-id="44295-119">Finally, the ASP.NET AJAX `ScriptManager` control enables the Control Toolkit to work:</span></span>

[!code-aspx[Main](adjusting-the-z-index-of-a-dropshadow-cs/samples/sample4.aspx)]

<span data-ttu-id="44295-120">При выполнении этого скрипта, пункты меню отображаются под панели.</span><span class="sxs-lookup"><span data-stu-id="44295-120">When you run this script, the menu entries appear underneath the panel.</span></span> <span data-ttu-id="44295-121">Тем не менее меню используется класс CSS `panel` где вам просто нужно определить два действия, чтобы сделать элементы отображаются перед элементами на другие панели:</span><span class="sxs-lookup"><span data-stu-id="44295-121">However the menu uses the CSS class `panel` where you just have to define two things to make elements appear in front of the other panel:</span></span>

- <span data-ttu-id="44295-122">Относительное расположение</span><span class="sxs-lookup"><span data-stu-id="44295-122">Relative positioning</span></span>
- <span data-ttu-id="44295-123">Положительное значение z индекса</span><span class="sxs-lookup"><span data-stu-id="44295-123">A positive z-index</span></span>

[!code-css[Main](adjusting-the-z-index-of-a-dropshadow-cs/samples/sample5.css)]

<span data-ttu-id="44295-124">Затем `DropShadowExtender` управления больше не конфликтуют с меню элемента управления.</span><span class="sxs-lookup"><span data-stu-id="44295-124">Then, the `DropShadowExtender` control does not conflict any longer with the Menu control.</span></span>


<span data-ttu-id="44295-125">[![Ранее: Элемент меню не отображается](adjusting-the-z-index-of-a-dropshadow-cs/_static/image2.png)](adjusting-the-z-index-of-a-dropshadow-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="44295-125">[![Before: The menu entry is not visible](adjusting-the-z-index-of-a-dropshadow-cs/_static/image2.png)](adjusting-the-z-index-of-a-dropshadow-cs/_static/image1.png)</span></span>

<span data-ttu-id="44295-126">Ранее: Элемент меню не отображается ([Просмотр полноразмерного изображения](adjusting-the-z-index-of-a-dropshadow-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="44295-126">Before: The menu entry is not visible ([Click to view full-size image](adjusting-the-z-index-of-a-dropshadow-cs/_static/image3.png))</span></span>


<span data-ttu-id="44295-127">[![Стало: Появляется запись меню](adjusting-the-z-index-of-a-dropshadow-cs/_static/image5.png)](adjusting-the-z-index-of-a-dropshadow-cs/_static/image4.png)</span><span class="sxs-lookup"><span data-stu-id="44295-127">[![After: The menu entry appears](adjusting-the-z-index-of-a-dropshadow-cs/_static/image5.png)](adjusting-the-z-index-of-a-dropshadow-cs/_static/image4.png)</span></span>

<span data-ttu-id="44295-128">Стало: Запись меню появится ([Просмотр полноразмерного изображения](adjusting-the-z-index-of-a-dropshadow-cs/_static/image6.png))</span><span class="sxs-lookup"><span data-stu-id="44295-128">After: The menu entry appears ([Click to view full-size image](adjusting-the-z-index-of-a-dropshadow-cs/_static/image6.png))</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="44295-129">Вперед</span><span class="sxs-lookup"><span data-stu-id="44295-129">Next</span></span>](manipulating-dropshadow-properties-from-client-code-cs.md)
