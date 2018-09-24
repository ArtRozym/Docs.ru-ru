---
title: Создание веб-API с помощью ASP.NET Core и Visual Studio для Mac
author: rick-anderson
description: Создание веб-API с помощью MVC ASP.NET Core и Visual Studio для Mac
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2018
uid: tutorials/first-web-api-mac
ms.openlocfilehash: 40f9bd9c57b97826edfddeb00cb4fb38a026d46e
ms.sourcegitcommit: b2723654af4969a24545f09ebe32004cb5e84a96
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46011629"
---
# <a name="create-a-web-api-with-aspnet-core-and-visual-studio-for-mac"></a><span data-ttu-id="7600a-103">Создание веб-API с помощью ASP.NET Core и Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="7600a-103">Create a Web API with ASP.NET Core and Visual Studio for Mac</span></span>

<span data-ttu-id="7600a-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) и [Майк Уоссон](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="7600a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="7600a-105">В этом руководстве создается веб-API для управления элементами списка дел.</span><span class="sxs-lookup"><span data-stu-id="7600a-105">In this tutorial, build a web API for managing a list of "to-do" items.</span></span> <span data-ttu-id="7600a-106">При этом пользовательский интерфейс не создается.</span><span class="sxs-lookup"><span data-stu-id="7600a-106">The UI isn't constructed.</span></span>

<span data-ttu-id="7600a-107">Существуют три версии этого руководства:</span><span class="sxs-lookup"><span data-stu-id="7600a-107">There are three versions of this tutorial:</span></span>

* <span data-ttu-id="7600a-108">macOS: создание веб-API с помощью Visual Studio для Mac (этот учебник)</span><span class="sxs-lookup"><span data-stu-id="7600a-108">macOS: Web API with Visual Studio for Mac (This tutorial)</span></span>
* <span data-ttu-id="7600a-109">Windows: [создание веб-API с помощью Visual Studio для Windows](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="7600a-109">Windows: [Web API with Visual Studio for Windows](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="7600a-110">macOS, Linux, Windows: [создание веб-API с помощью Visual Studio Code](xref:tutorials/web-api-vsc)</span><span class="sxs-lookup"><span data-stu-id="7600a-110">macOS, Linux, Windows: [Web API with Visual Studio Code](xref:tutorials/web-api-vsc)</span></span>

<!-- WARNING: The code AND images in this doc are used by uid: tutorials/web-api-vsc, tutorials/first-web-api-mac and tutorials/first-web-api. If you change any code/images in this tutorial, update uid: tutorials/web-api-vsc -->

[!INCLUDE[template files](../includes/webApi/intro.md)]

<span data-ttu-id="7600a-111">Пример использования постоянной базы данных см. в статье [Введение в MVC ASP.NET Core в macOS](xref:tutorials/first-mvc-app-xplat/index).</span><span class="sxs-lookup"><span data-stu-id="7600a-111">See [Introduction to ASP.NET Core MVC on macOS or Linux](xref:tutorials/first-mvc-app-xplat/index) for an example that uses a persistent database.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7600a-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="7600a-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-macos.md)]

## <a name="create-the-project"></a><span data-ttu-id="7600a-113">Создание проекта</span><span class="sxs-lookup"><span data-stu-id="7600a-113">Create the project</span></span>

<span data-ttu-id="7600a-114">В Visual Studio выберите **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="7600a-114">From Visual Studio, select **File** > **New Solution**.</span></span>

![Новое решение macOS](first-web-api-mac/_static/sln.png)

<span data-ttu-id="7600a-116">Выберите **Приложение .NET Core** > **Веб-API ASP.NET Core** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="7600a-116">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

![Диалоговое окно "Новый проект" в macOS](first-web-api-mac/_static/1.png)

<span data-ttu-id="7600a-118">Введите *TodoApi* в поле **Имя проекта** и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="7600a-118">Enter *TodoApi* for the **Project Name**, and then click **Create**.</span></span>

![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

### <a name="launch-the-app"></a><span data-ttu-id="7600a-120">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="7600a-120">Launch the app</span></span>

<span data-ttu-id="7600a-121">В Visual Studio откройте меню **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="7600a-121">In Visual Studio, select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="7600a-122">Visual Studio откроет браузер и перейдет к `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="7600a-122">Visual Studio launches a browser and navigates to `http://localhost:5000`.</span></span> <span data-ttu-id="7600a-123">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="7600a-123">You get an HTTP 404 (Not Found) error.</span></span> <span data-ttu-id="7600a-124">Измените URL-адрес на `http://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="7600a-124">Change the URL to `http://localhost:<port>/api/values`.</span></span> <span data-ttu-id="7600a-125">Отображаются данные `ValuesController`:</span><span class="sxs-lookup"><span data-stu-id="7600a-125">The `ValuesController` data is displayed:</span></span>

```json
["value1","value2"]
```

### <a name="add-support-for-entity-framework-core"></a><span data-ttu-id="7600a-126">Добавление поддержки для Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7600a-126">Add support for Entity Framework Core</span></span>

<span data-ttu-id="7600a-127">Установите поставщик базы данных [Entity Framework Core InMemory](/ef/core/providers/in-memory/).</span><span class="sxs-lookup"><span data-stu-id="7600a-127">Install the [Entity Framework Core InMemory](/ef/core/providers/in-memory/) database provider.</span></span> <span data-ttu-id="7600a-128">Этот поставщик базы данных позволяет использовать Entity Framework Core с выполняющейся в памяти базой данных.</span><span class="sxs-lookup"><span data-stu-id="7600a-128">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span>

* <span data-ttu-id="7600a-129">В меню **Проект** выберите пункт **Добавить пакеты NuGet**.</span><span class="sxs-lookup"><span data-stu-id="7600a-129">From the **Project** menu, select **Add NuGet Packages**.</span></span>

  * <span data-ttu-id="7600a-130">Также можно щелкнуть **Зависимости** правой кнопкой мыши и выбрать команду **Добавить пакеты**.</span><span class="sxs-lookup"><span data-stu-id="7600a-130">Alternatively, you can right-click **Dependencies**, and then select **Add Packages**.</span></span>

* <span data-ttu-id="7600a-131">Введите `EntityFrameworkCore.InMemory` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="7600a-131">Enter `EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="7600a-132">Выберите `Microsoft.EntityFrameworkCore.InMemory`, а затем **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="7600a-132">Select `Microsoft.EntityFrameworkCore.InMemory`, and then select **Add Package**.</span></span>

### <a name="add-a-model-class"></a><span data-ttu-id="7600a-133">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="7600a-133">Add a model class</span></span>

<span data-ttu-id="7600a-134">Модель — это объект, представляющий данные в приложении.</span><span class="sxs-lookup"><span data-stu-id="7600a-134">A model is an object representing the data in your app.</span></span> <span data-ttu-id="7600a-135">В этом случае единственной моделью является задача.</span><span class="sxs-lookup"><span data-stu-id="7600a-135">In this case, the only model is a to-do item.</span></span>

<span data-ttu-id="7600a-136">В обозревателе решений щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="7600a-136">In Solution Explorer, right-click the project.</span></span> <span data-ttu-id="7600a-137">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="7600a-137">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7600a-138">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="7600a-138">Name the folder *Models*.</span></span>

![Новая папка](first-web-api-mac/_static/folder.png)

> [!NOTE]
> <span data-ttu-id="7600a-140">Классы моделей можно размещать в любом месте в проекте, но по соглашению используется папка *Models*.</span><span class="sxs-lookup"><span data-stu-id="7600a-140">You can put model classes anywhere in your project, but the *Models* folder is used by convention.</span></span>

<span data-ttu-id="7600a-141">Щелкните папку *Models* правой кнопкой мыши и выберите **Добавить** > **Новый файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="7600a-141">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span> <span data-ttu-id="7600a-142">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="7600a-142">Name the class *TodoItem*, and then click **New**.</span></span>

<span data-ttu-id="7600a-143">Замените сгенерированный код на следующий:</span><span class="sxs-lookup"><span data-stu-id="7600a-143">Replace the generated code with:</span></span>

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="7600a-144">После создания `TodoItem` база данных формирует `Id`.</span><span class="sxs-lookup"><span data-stu-id="7600a-144">The database generates the `Id` when a `TodoItem` is created.</span></span>

### <a name="create-the-database-context"></a><span data-ttu-id="7600a-145">Создание контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="7600a-145">Create the database context</span></span>

<span data-ttu-id="7600a-146">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для заданной модели данных.</span><span class="sxs-lookup"><span data-stu-id="7600a-146">The *database context* is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7600a-147">Этот класс создается путем наследования от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7600a-147">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

<span data-ttu-id="7600a-148">Добавьте класс `TodoContext` в папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="7600a-148">Add a `TodoContext` class to the *Models* folder.</span></span>

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Models/TodoContext.cs)]

[!INCLUDE[Register the database context](../includes/webApi/register_dbContext.md)]

## <a name="add-a-controller"></a><span data-ttu-id="7600a-149">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="7600a-149">Add a controller</span></span>

<span data-ttu-id="7600a-150">В обозревателе решений добавьте в папку *Controllers* класс `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="7600a-150">In Solution Explorer, in the *Controllers* folder, add the class `TodoController`.</span></span>

<span data-ttu-id="7600a-151">Замените сгенерированный код следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7600a-151">Replace the generated code with the following:</span></span>

[!INCLUDE[code and get todo items](../includes/webApi/getTodoItems.md)]

### <a name="launch-the-app"></a><span data-ttu-id="7600a-152">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="7600a-152">Launch the app</span></span>

<span data-ttu-id="7600a-153">В Visual Studio откройте меню **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="7600a-153">In Visual Studio, select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="7600a-154">Visual Studio запустит браузер и перейдет к `http://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="7600a-154">Visual Studio launches a browser and navigates to `http://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7600a-155">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="7600a-155">You get an HTTP 404 (Not Found) error.</span></span> <span data-ttu-id="7600a-156">Измените URL-адрес на `http://localhost:<port>/api/values`.</span><span class="sxs-lookup"><span data-stu-id="7600a-156">Change the URL to `http://localhost:<port>/api/values`.</span></span> <span data-ttu-id="7600a-157">Отображаются данные `ValuesController`:</span><span class="sxs-lookup"><span data-stu-id="7600a-157">The `ValuesController` data is displayed:</span></span>

```json
["value1","value2"]
```

<span data-ttu-id="7600a-158">Перейдите к контроллеру `Todo` по адресу `http://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="7600a-158">Navigate to the `Todo` controller at `http://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="7600a-159">Возвращается следующий файл JSON:</span><span class="sxs-lookup"><span data-stu-id="7600a-159">The following JSON is returned:</span></span>

```json
[{"key":1,"name":"Item1","isComplete":false}]
```

## <a name="implement-the-other-crud-operations"></a><span data-ttu-id="7600a-160">Реализация других операций CRUD</span><span class="sxs-lookup"><span data-stu-id="7600a-160">Implement the other CRUD operations</span></span>

<span data-ttu-id="7600a-161">Добавим к контроллеру методы `Create`, `Update` и `Delete`.</span><span class="sxs-lookup"><span data-stu-id="7600a-161">We'll add `Create`, `Update`, and `Delete` methods to the controller.</span></span> <span data-ttu-id="7600a-162">Эти методы являются вариациями темы, поэтому мы просто покажем код и выделим основные различия.</span><span class="sxs-lookup"><span data-stu-id="7600a-162">These methods are variations on a theme, so I'll just show the code and highlight the main differences.</span></span> <span data-ttu-id="7600a-163">После добавления или изменения кода выполните сборку проекта.</span><span class="sxs-lookup"><span data-stu-id="7600a-163">Build the project after adding or changing code.</span></span>

### <a name="create"></a><span data-ttu-id="7600a-164">Создать</span><span class="sxs-lookup"><span data-stu-id="7600a-164">Create</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="7600a-165">Приведенный выше метод отвечает на запрос HTTP POST, как указано атрибутом [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="7600a-165">The preceding method responds to an HTTP POST, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7600a-166">Атрибут [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) сообщает MVC, что необходимо получить значение элемента задачи из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="7600a-166">The [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute tells MVC to get the value of the to-do item from the body of the HTTP request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](first-web-api/samples/2.1/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="7600a-167">Приведенный выше метод отвечает на запрос HTTP POST, как указано атрибутом [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="7600a-167">The preceding method responds to an HTTP POST, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7600a-168">MVC получает значение элемента задачи из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="7600a-168">MVC gets the value of the to-do item from the body of the HTTP request.</span></span>

::: moniker-end

<span data-ttu-id="7600a-169">Метод `CreatedAtRoute` возвращает ответ 201.</span><span class="sxs-lookup"><span data-stu-id="7600a-169">The `CreatedAtRoute` method returns a 201 response.</span></span> <span data-ttu-id="7600a-170">Это стандартный ответ для метода HTTP POST, который создает ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="7600a-170">It's the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="7600a-171">`CreatedAtRoute` также добавляет в ответ заголовок расположения.</span><span class="sxs-lookup"><span data-stu-id="7600a-171">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="7600a-172">Заголовок расположения указывает URI вновь созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="7600a-172">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="7600a-173">См. раздел [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7600a-173">See [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

### <a name="use-postman-to-send-a-create-request"></a><span data-ttu-id="7600a-174">Отправка запроса Create с помощью Postman</span><span class="sxs-lookup"><span data-stu-id="7600a-174">Use Postman to send a Create request</span></span>

* <span data-ttu-id="7600a-175">Запустите приложение (**Выполнить** > **Запуск с отладкой**).</span><span class="sxs-lookup"><span data-stu-id="7600a-175">Start the app (**Run** > **Start With Debugging**).</span></span>
* <span data-ttu-id="7600a-176">Откройте Postman.</span><span class="sxs-lookup"><span data-stu-id="7600a-176">Open Postman.</span></span>

![Консоль Postman](first-web-api/_static/pmc.png)

* <span data-ttu-id="7600a-178">Измените номера порта в localhost URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="7600a-178">Update the port number in the localhost URL.</span></span>
* <span data-ttu-id="7600a-179">Укажите метод HTTP *POST*.</span><span class="sxs-lookup"><span data-stu-id="7600a-179">Set the HTTP method to *POST*.</span></span>
* <span data-ttu-id="7600a-180">Откройте вкладку **Текст**.</span><span class="sxs-lookup"><span data-stu-id="7600a-180">Click the **Body** tab.</span></span>
* <span data-ttu-id="7600a-181">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="7600a-181">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7600a-182">Задайте тип *JSON (приложение/json)*.</span><span class="sxs-lookup"><span data-stu-id="7600a-182">Set the type to *JSON (application/json)*.</span></span>
* <span data-ttu-id="7600a-183">Введите текст запроса с элементом задачи наподобие следующего JSON:</span><span class="sxs-lookup"><span data-stu-id="7600a-183">Enter a request body with a to-do item resembling the following JSON:</span></span>

```json
{
  "name":"walk dog",
  "isComplete":true
}
```

* <span data-ttu-id="7600a-184">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="7600a-184">Click the **Send** button.</span></span>

::: moniker range=">= aspnetcore-2.1"

> [!TIP]
> <span data-ttu-id="7600a-185">Если ответ не отображается после нажатия кнопки **Отправить**, отключите параметр **Проверка сертификации SSL**.</span><span class="sxs-lookup"><span data-stu-id="7600a-185">If no response displays after clicking **Send**, disable the **SSL certification verification** option.</span></span> <span data-ttu-id="7600a-186">Он находится в разделе **Файл** > **Параметры**.</span><span class="sxs-lookup"><span data-stu-id="7600a-186">This is found under **File** > **Settings**.</span></span> <span data-ttu-id="7600a-187">Нажмите кнопку **Отправить** еще раз после отключения параметра.</span><span class="sxs-lookup"><span data-stu-id="7600a-187">Click the **Send** button again after disabling the setting.</span></span>

::: moniker-end

<span data-ttu-id="7600a-188">Откройте вкладку **Заголовки** на панели **Ответ** и скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="7600a-188">Click the **Headers** tab in the **Response** pane and copy the **Location** header value:</span></span>

![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

<span data-ttu-id="7600a-190">URI заголовка расположения позволяет получить доступ к созданному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="7600a-190">You can use the Location header URI to access the resource you created.</span></span> <span data-ttu-id="7600a-191">Метод `Create` возвращает [CreatedAtRoute](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.createdatroute#Microsoft_AspNetCore_Mvc_ControllerBase_CreatedAtRoute_System_String_System_Object_System_Object_).</span><span class="sxs-lookup"><span data-stu-id="7600a-191">The `Create` method returns [CreatedAtRoute](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.createdatroute#Microsoft_AspNetCore_Mvc_ControllerBase_CreatedAtRoute_System_String_System_Object_System_Object_).</span></span> <span data-ttu-id="7600a-192">Первый параметр, передаваемый `CreatedAtRoute`, представляет именованный маршрут для создания URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="7600a-192">The first parameter passed to `CreatedAtRoute` represents the named route to use for generating the URL.</span></span> <span data-ttu-id="7600a-193">Помните, что метод `GetById` создал именованный маршрут `"GetTodo"`:</span><span class="sxs-lookup"><span data-stu-id="7600a-193">Recall that the `GetById` method created the `"GetTodo"` named route:</span></span>

```csharp
[HttpGet("{id}", Name = "GetTodo")]
```

### <a name="update"></a><span data-ttu-id="7600a-194">Обновление</span><span class="sxs-lookup"><span data-stu-id="7600a-194">Update</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](first-web-api/samples/2.1/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

::: moniker-end

<span data-ttu-id="7600a-195">Страница `Update` аналогична странице `Create`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="7600a-195">`Update` is similar to `Create`, but uses HTTP PUT.</span></span> <span data-ttu-id="7600a-196">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7600a-196">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7600a-197">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только сведения о ней.</span><span class="sxs-lookup"><span data-stu-id="7600a-197">According to the HTTP spec, a PUT request requires the client to send the entire updated entity, not just the deltas.</span></span> <span data-ttu-id="7600a-198">Чтобы обеспечить поддержку частичных обновлений, используйте HTTP PATCH.</span><span class="sxs-lookup"><span data-stu-id="7600a-198">To support partial updates, use HTTP PATCH.</span></span>

```json
{
  "key": 1,
  "name": "walk dog",
  "isComplete": true
}
```

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

### <a name="delete"></a><span data-ttu-id="7600a-200">Удаление</span><span class="sxs-lookup"><span data-stu-id="7600a-200">Delete</span></span>

[!code-csharp[](first-web-api/samples/2.0/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="7600a-201">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7600a-201">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmd.png)

[!INCLUDE[jQuery](../includes/webApi/add-jquery.md)]

[!INCLUDE[next steps](../includes/webApi/next.md)]
