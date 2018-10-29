---
title: Авторизация на основе ресурсов в ASP.NET Core
author: scottaddie
description: Узнайте, как реализовать авторизацию на основе ресурсов в приложениях ASP.NET Core при атрибут Authorize будет недостаточно.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/07/2017
uid: security/authorization/resourcebased
ms.openlocfilehash: 2cb3844a38f7482c27fb471343109d51a516ea20
ms.sourcegitcommit: 375e9a67f5e1f7b0faaa056b4b46294cc70f55b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50206700"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="d94ce-103">Авторизация на основе ресурсов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d94ce-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="d94ce-104">Стратегии авторизации зависит от ресурса к которому выполняется доступ.</span><span class="sxs-lookup"><span data-stu-id="d94ce-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="d94ce-105">Рассмотрим документ, который имеет свойство автора.</span><span class="sxs-lookup"><span data-stu-id="d94ce-105">Consider a document which has an author property.</span></span> <span data-ttu-id="d94ce-106">Только автор может обновлять документа.</span><span class="sxs-lookup"><span data-stu-id="d94ce-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="d94ce-107">Следовательно документа должны извлекаться из хранилища данных для оценки авторизации.</span><span class="sxs-lookup"><span data-stu-id="d94ce-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="d94ce-108">Атрибут оценивается перед привязкой данных и перед выполнением обработчика страницы или действие, которое загружает документ.</span><span class="sxs-lookup"><span data-stu-id="d94ce-108">Attribute evaluation occurs before data binding and before execution of the page handler or action which loads the document.</span></span> <span data-ttu-id="d94ce-109">По этим причинам декларативной авторизации с помощью `[Authorize]` атрибут будет недостаточно.</span><span class="sxs-lookup"><span data-stu-id="d94ce-109">For these reasons, declarative authorization with an `[Authorize]` attribute won't suffice.</span></span> <span data-ttu-id="d94ce-110">Вместо этого можно вызвать метод настраиваемой авторизации&mdash;стиль называется принудительной авторизации.</span><span class="sxs-lookup"><span data-stu-id="d94ce-110">Instead, you can invoke a custom authorization method&mdash;a style known as imperative authorization.</span></span>

<span data-ttu-id="d94ce-111">[Просмотреть или скачать пример кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d94ce-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d94ce-112">[Создание приложения ASP.NET Core с помощью данных пользователя с помощью авторизации](xref:security/authorization/secure-data) содержит пример приложения, использующего авторизации на основе ресурсов.</span><span class="sxs-lookup"><span data-stu-id="d94ce-112">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="d94ce-113">Использование принудительной авторизации</span><span class="sxs-lookup"><span data-stu-id="d94ce-113">Use imperative authorization</span></span>

<span data-ttu-id="d94ce-114">Авторизации реализуется как [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) службы и регистрируется в службе коллекции в течение `Startup` класса.</span><span class="sxs-lookup"><span data-stu-id="d94ce-114">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="d94ce-115">Сделан доступным через службу [внедрения зависимостей](xref:fundamentals/dependency-injection) обработчики страницы или действий.</span><span class="sxs-lookup"><span data-stu-id="d94ce-115">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="d94ce-116">`IAuthorizationService` имеет два `AuthorizeAsync` перегрузок метода: один принимает ресурс и имя политики, а другой принимает ресурс и список требований для оценки.</span><span class="sxs-lookup"><span data-stu-id="d94ce-116">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d94ce-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d94ce-118">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-118">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

---

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="d94ce-119">В следующем примере ресурса должна быть обеспечена безопасность загружается в пользовательский `Document` объекта.</span><span class="sxs-lookup"><span data-stu-id="d94ce-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="d94ce-120">`AuthorizeAsync` Перегруженный метод вызывается, чтобы определить, разрешено ли текущий пользователь изменять указанный документ.</span><span class="sxs-lookup"><span data-stu-id="d94ce-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="d94ce-121">Пользовательские политики авторизации «EditPolicy» добавляется в решение.</span><span class="sxs-lookup"><span data-stu-id="d94ce-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="d94ce-122">См. в разделе [авторизации на основе политик — Custom](xref:security/authorization/policies) Дополнительные сведения о создании политик авторизации.</span><span class="sxs-lookup"><span data-stu-id="d94ce-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="d94ce-123">Следующий код в приведенных примерах предполагается, выполнена проверка подлинности и набор `User` свойство.</span><span class="sxs-lookup"><span data-stu-id="d94ce-123">The following code samples assume authentication has run and set the `User` property.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d94ce-124">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-124">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d94ce-125">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-125">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

---

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="d94ce-126">Написание обработчика, на основе ресурсов</span><span class="sxs-lookup"><span data-stu-id="d94ce-126">Write a resource-based handler</span></span>

<span data-ttu-id="d94ce-127">Написание обработчика для авторизации на основе ресурсов не сильно отличается от [написание обработчика plain требования](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="d94ce-127">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="d94ce-128">Создание класса пользовательского требования и реализовать класс обработчика требование.</span><span class="sxs-lookup"><span data-stu-id="d94ce-128">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="d94ce-129">Класс обработчика задает требование и тип ресурса.</span><span class="sxs-lookup"><span data-stu-id="d94ce-129">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="d94ce-130">Например, обработчик использование `SameAuthorRequirement` требование и `Document` ресурсов выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="d94ce-130">For example, a handler utilizing a `SameAuthorRequirement` requirement and a `Document` resource looks as follows:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d94ce-131">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-131">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d94ce-132">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-132">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

---

<span data-ttu-id="d94ce-133">Зарегистрируйте требование и обработчик в `Startup.ConfigureServices` метод:</span><span class="sxs-lookup"><span data-stu-id="d94ce-133">Register the requirement and handler in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a><span data-ttu-id="d94ce-134">Эксплуатационные требования</span><span class="sxs-lookup"><span data-stu-id="d94ce-134">Operational requirements</span></span>

<span data-ttu-id="d94ce-135">При создании решения, основываясь на результаты операции CRUD (Create, Read, Update, Delete), используйте [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) вспомогательный класс.</span><span class="sxs-lookup"><span data-stu-id="d94ce-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="d94ce-136">Этот класс позволяет написать один обработчик вместо отдельный класс для каждого типа операции.</span><span class="sxs-lookup"><span data-stu-id="d94ce-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="d94ce-137">Чтобы использовать его, укажите имена некоторых операций:</span><span class="sxs-lookup"><span data-stu-id="d94ce-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="d94ce-138">Обработчик реализуется следующим образом, с помощью `OperationAuthorizationRequirement` требование и `Document` ресурсов:</span><span class="sxs-lookup"><span data-stu-id="d94ce-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d94ce-139">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-139">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d94ce-140">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-140">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

---

<span data-ttu-id="d94ce-141">Предыдущий обработчик проверяет операцию, используя ресурс, удостоверение пользователя и требование `Name` свойство.</span><span class="sxs-lookup"><span data-stu-id="d94ce-141">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

<span data-ttu-id="d94ce-142">Для вызова обработчика оперативной ресурсов, указать операцию, при вызове `AuthorizeAsync` в обработчик страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="d94ce-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="d94ce-143">В следующем примере определяется, разрешено ли прошедшего проверку подлинности пользователя для просмотра предоставленного документа.</span><span class="sxs-lookup"><span data-stu-id="d94ce-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="d94ce-144">Следующий код в приведенных примерах предполагается, выполнена проверка подлинности и набор `User` свойство.</span><span class="sxs-lookup"><span data-stu-id="d94ce-144">The following code samples assume authentication has run and set the `User` property.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="d94ce-145">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-145">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="d94ce-146">Если авторизация выполняется успешно, возвращается на страницу для просмотра документа.</span><span class="sxs-lookup"><span data-stu-id="d94ce-146">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="d94ce-147">Если происходит сбой авторизации, но пользователь проходит проверку подлинности, возвращая `ForbidResult` информирует любое по промежуточного слоя проверки подлинности, который не удалось выполнить авторизацию.</span><span class="sxs-lookup"><span data-stu-id="d94ce-147">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="d94ce-148">Объект `ChallengeResult` возвращается, если необходимо выполнить проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="d94ce-148">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="d94ce-149">Для интерактивного обозревателя клиента может потребоваться перенаправить пользователя на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="d94ce-149">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="d94ce-150">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="d94ce-150">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="d94ce-151">Если авторизация выполняется успешно, возвращается представление для документа.</span><span class="sxs-lookup"><span data-stu-id="d94ce-151">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="d94ce-152">Если авторизация заканчивается неудачей, возвращая `ChallengeResult` информирует любое по промежуточного слоя проверки подлинности, что не удалось выполнить авторизацию, и по промежуточного слоя может принимать ответные действия.</span><span class="sxs-lookup"><span data-stu-id="d94ce-152">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="d94ce-153">Код состояния 401 или 403 может возвращаться соответствующий ответ.</span><span class="sxs-lookup"><span data-stu-id="d94ce-153">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="d94ce-154">Для интерактивного обозревателя клиента это может означать перенаправления пользователя на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="d94ce-154">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

---
