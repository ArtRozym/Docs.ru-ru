---
title: Клиент .NET SignalR ASP.NET Core
author: tdykstra
description: Сведения о клиенте .NET, ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 09/10/2018
uid: signalr/dotnet-client
ms.openlocfilehash: 205ca8ca228dcc2cc77f7e9b6431943851a3b152
ms.sourcegitcommit: 1a2fc47fb5d3da0f2a3c3269613ab20eb3b0da2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44373323"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="efa2e-103">Клиент .NET SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="efa2e-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="efa2e-104">Автор: [Рэйчел Аппель](http://twitter.com/rachelappel) (Rachel Appel)</span><span class="sxs-lookup"><span data-stu-id="efa2e-104">By [Rachel Appel](http://twitter.com/rachelappel)</span></span>

<span data-ttu-id="efa2e-105">Клиентская библиотека ASP.NET Core SignalR .NET позволяет взаимодействовать с концентраторами SignalR из приложений .NET.</span><span class="sxs-lookup"><span data-stu-id="efa2e-105">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

> [!NOTE]
> <span data-ttu-id="efa2e-106">Xamarin имеет особые требования для версии Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="efa2e-106">Xamarin has special requirements for Visual Studio version.</span></span> <span data-ttu-id="efa2e-107">Дополнительные сведения см. в разделе [клиентом SignalR 2.1.1 в Xamarin](https://github.com/aspnet/Announcements/issues/305).</span><span class="sxs-lookup"><span data-stu-id="efa2e-107">For more information, see [SignalR Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).</span></span>

<span data-ttu-id="efa2e-108">[Просмотреть или скачать образец кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([как скачивать](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="efa2e-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<span data-ttu-id="efa2e-109">В образце кода в этой статье показана приложения WPF, использующее клиент ASP.NET Core SignalR .NET.</span><span class="sxs-lookup"><span data-stu-id="efa2e-109">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="efa2e-110">Установка пакета клиента SignalR .NET</span><span class="sxs-lookup"><span data-stu-id="efa2e-110">Install the SignalR .NET client package</span></span>

<span data-ttu-id="efa2e-111">`Microsoft.AspNetCore.SignalR.Client` Пакет требуется для клиентов .NET для подключения к концентраторов SignalR.</span><span class="sxs-lookup"><span data-stu-id="efa2e-111">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="efa2e-112">Чтобы установить клиентскую библиотеку, выполните следующую команду **консоль диспетчера пакетов** окна:</span><span class="sxs-lookup"><span data-stu-id="efa2e-112">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="efa2e-113">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="efa2e-113">Connect to a hub</span></span>

<span data-ttu-id="efa2e-114">Чтобы установить подключение, создайте `HubConnectionBuilder` и вызвать `Build`.</span><span class="sxs-lookup"><span data-stu-id="efa2e-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="efa2e-115">URL-адрес концентратора, протокола, тип транспорта, уровень ведения журнала, заголовки и другие параметры можно настроить при создании подключения.</span><span class="sxs-lookup"><span data-stu-id="efa2e-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="efa2e-116">Настройте все необходимые параметры, вставляя `HubConnectionBuilder` методы в `Build`.</span><span class="sxs-lookup"><span data-stu-id="efa2e-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="efa2e-117">Установить подключение с `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="efa2e-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=14-16,32)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="efa2e-118">Вызов методов концентратора из клиента</span><span class="sxs-lookup"><span data-stu-id="efa2e-118">Call hub methods from client</span></span>

<span data-ttu-id="efa2e-119">`InvokeAsync` вызывает методы концентратора.</span><span class="sxs-lookup"><span data-stu-id="efa2e-119">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="efa2e-120">Передайте имя метода концентратора и все аргументы, заданные в методе концентратора, чтобы `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="efa2e-120">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="efa2e-121">SignalR является асинхронным, поэтому используйте `async` и `await` при выполнении вызовов.</span><span class="sxs-lookup"><span data-stu-id="efa2e-121">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="efa2e-122">Вызывать методы клиента от концентратора</span><span class="sxs-lookup"><span data-stu-id="efa2e-122">Call client methods from hub</span></span>

<span data-ttu-id="efa2e-123">Определите методы концентратора вызовы с использованием `connection.On` после сборки, но прежде чем выполнять подключение.</span><span class="sxs-lookup"><span data-stu-id="efa2e-123">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="efa2e-124">Предыдущий код в `connection.On` выполняется, когда серверный код вызывает ее с помощью `SendAsync` метод.</span><span class="sxs-lookup"><span data-stu-id="efa2e-124">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="efa2e-125">Обработка ошибок и ведение журнала</span><span class="sxs-lookup"><span data-stu-id="efa2e-125">Error handling and logging</span></span>

<span data-ttu-id="efa2e-126">Обработка ошибок с помощью оператора try-catch.</span><span class="sxs-lookup"><span data-stu-id="efa2e-126">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="efa2e-127">Проверьте `Exception` объектом, чтобы определить правильное действие, предпринимаемое после возникновения ошибки.</span><span class="sxs-lookup"><span data-stu-id="efa2e-127">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="efa2e-128">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="efa2e-128">Additional resources</span></span>

* [<span data-ttu-id="efa2e-129">Центры</span><span class="sxs-lookup"><span data-stu-id="efa2e-129">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="efa2e-130">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="efa2e-130">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="efa2e-131">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="efa2e-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
