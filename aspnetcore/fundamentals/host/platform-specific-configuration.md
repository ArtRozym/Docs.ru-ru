---
title: Усовершенствование приложения из внешней сборки в ASP.NET Core с IHostingStartup
author: guardrex
description: Узнайте, как улучшить приложение ASP.NET Core из внешней сборки, используя реализацию IHostingStartup.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2018
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 2eddfa03b28564fcca7cc098e353b05e23b7c6f6
ms.sourcegitcommit: a669c4e3f42e387e214a354ac4143555602e6f66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336306"
---
# <a name="enhance-an-app-from-an-external-assembly-in-aspnet-core-with-ihostingstartup"></a><span data-ttu-id="0f1de-103">Усовершенствование приложения из внешней сборки в ASP.NET Core с IHostingStartup</span><span class="sxs-lookup"><span data-stu-id="0f1de-103">Enhance an app from an external assembly in ASP.NET Core with IHostingStartup</span></span>

<span data-ttu-id="0f1de-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="0f1de-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0f1de-105">Реализация [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (размещение при запуске) позволяет добавлять в приложение улучшения из внешней сборки при запуске.</span><span class="sxs-lookup"><span data-stu-id="0f1de-105">An [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="0f1de-106">Например, внешняя библиотека может использовать реализацию размещения при запуске, чтобы доставить дополнительные поставщики конфигурации или службы для приложения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span> <span data-ttu-id="0f1de-107">`IHostingStartup` *доступен в ASP.NET Core 2.0 или в более поздних версиях.*</span><span class="sxs-lookup"><span data-stu-id="0f1de-107">`IHostingStartup` *is available in ASP.NET Core 2.0 or later.*</span></span>

<span data-ttu-id="0f1de-108">[Просмотреть или скачать образец кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([как скачивать](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0f1de-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="0f1de-109">Атрибут HostingStartup</span><span class="sxs-lookup"><span data-stu-id="0f1de-109">HostingStartup attribute</span></span>

<span data-ttu-id="0f1de-110">Атрибут [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) указывает на наличие начальной сборки размещения, которая будет активирована во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-110">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="0f1de-111">Входная сборка или сборка, содержащая класс `Startup`, автоматически сканируется на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-111">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="0f1de-112">Список сборок, в котором будет выполняться поиск атрибута `HostingStartup`, загружается во время выполнения из конфигурации в [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span><span class="sxs-lookup"><span data-stu-id="0f1de-112">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span></span> <span data-ttu-id="0f1de-113">Список сборок, которые необходимо исключить из обнаружения, загружается из [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span><span class="sxs-lookup"><span data-stu-id="0f1de-113">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span></span> <span data-ttu-id="0f1de-114">Дополнительные сведения см. в разделах [Веб-узел: начальные сборки размещения](xref:fundamentals/host/web-host#hosting-startup-assemblies) и [Веб-узел: исключаемые сборки размещения при запуске](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="0f1de-114">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="0f1de-115">В следующем примере пространство имен для начальной сборки размещения — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-115">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="0f1de-116">Класс, содержащий код запуска размещения, — `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="0f1de-116">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="0f1de-117">Атрибут `HostingStartup` обычно находится в файле класса реализации начальной сборки размещения `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-117">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="0f1de-118">Обнаружение загруженных начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="0f1de-118">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="0f1de-119">Для обнаружения загруженных сборок размещения при запуске включите ведение журнала и проверьте журналы приложения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-119">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="0f1de-120">В журнал вносятся ошибки, возникающие при загрузке сборок.</span><span class="sxs-lookup"><span data-stu-id="0f1de-120">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="0f1de-121">Загруженные начальные сборки размещения регистрируются на уровне отладки, также регистрируются все ошибки.</span><span class="sxs-lookup"><span data-stu-id="0f1de-121">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="0f1de-122">Отключение автоматической загрузки начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="0f1de-122">Disable automatic loading of hosting startup assemblies</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0f1de-123">Чтобы отключить автоматическую загрузку начальных сборок размещения, используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="0f1de-123">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="0f1de-124">Чтобы предотвратить загрузку всех начальных сборок размещения, установите значение `true` или `1` для одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="0f1de-124">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="0f1de-125">Параметр конфигурации узла [Запретить размещение при запуске](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="0f1de-125">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="0f1de-126">Переменная среды `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-126">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="0f1de-127">Чтобы предотвратить загрузку конкретных сборок размещения, установите строку, содержащую разделенный точками с запятой список сборок размещения, которые необходимо исключить при запуске, в качестве значения одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="0f1de-127">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="0f1de-128">Параметр конфигурации узла [Исключаемые сборки размещения при запуске](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="0f1de-128">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="0f1de-129">Переменная среды `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-129">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0f1de-130">Чтобы отключить автоматическую загрузку начальных сборок размещения, установите значение `true` или `1` для одной из следующих переменных:</span><span class="sxs-lookup"><span data-stu-id="0f1de-130">To disable automatic loading of hosting startup assemblies, set one of the following to `true` or `1`:</span></span>

* <span data-ttu-id="0f1de-131">Параметр конфигурации узла [Запретить размещение при запуске](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="0f1de-131">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
* <span data-ttu-id="0f1de-132">Переменная среды `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-132">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

::: moniker-end

<span data-ttu-id="0f1de-133">Если заданы параметры конфигурации узла и переменная среды, на поведение влияют параметры узла.</span><span class="sxs-lookup"><span data-stu-id="0f1de-133">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="0f1de-134">При отключении сборок размещения при запуске с использованием параметра узла или переменной среды сборка отключается на глобальном уровне, и также могут быть отключены некоторые характеристики приложения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-134">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="0f1de-135">Проект</span><span class="sxs-lookup"><span data-stu-id="0f1de-135">Project</span></span>

<span data-ttu-id="0f1de-136">Создайте размещение при запуске для любого из указанных ниже типов проектов:</span><span class="sxs-lookup"><span data-stu-id="0f1de-136">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="0f1de-137">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="0f1de-137">Class library</span></span>](#class-library)
* [<span data-ttu-id="0f1de-138">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="0f1de-138">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="0f1de-139">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="0f1de-139">Class library</span></span>

<span data-ttu-id="0f1de-140">Расширение размещения при запуске можно указать в библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="0f1de-140">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="0f1de-141">Библиотека содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-141">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="0f1de-142">[Пример кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) включает приложение Razor Pages *HostingStartupApp* и библиотеку классов *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="0f1de-142">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="0f1de-143">Библиотека классов:</span><span class="sxs-lookup"><span data-stu-id="0f1de-143">The class library:</span></span>

* <span data-ttu-id="0f1de-144">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-144">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="0f1de-145">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения с помощью поставщика конфигурации, размещаемой в памяти ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span><span class="sxs-lookup"><span data-stu-id="0f1de-145">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span></span>
* <span data-ttu-id="0f1de-146">Включает атрибут `HostingStartup`, определяющий пространство имен и класс размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="0f1de-146">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="0f1de-147">Метод `ServiceKeyInjection` класса [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) использует [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="0f1de-147">The `ServiceKeyInjection` class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="0f1de-148">`IHostingStartup.Configure` в начальной сборке размещения вызывается средой выполнения до `Startup.Configure` в пользовательском коде, что позволяет пользовательскому коду перезаписать конфигурацию, предоставленную начальной сборкой размещения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-148">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

<span data-ttu-id="0f1de-149">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="0f1de-149">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="0f1de-150">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения библиотеки класса:</span><span class="sxs-lookup"><span data-stu-id="0f1de-150">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="0f1de-151">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0f1de-151">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="0f1de-152">[Пример кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) также включает проект пакета NuGet, предоставляющий отдельное размещение при запуске *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="0f1de-152">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="0f1de-153">Характеристики пакета аналогичны характеристикам библиотеки классов, приведенным ранее.</span><span class="sxs-lookup"><span data-stu-id="0f1de-153">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="0f1de-154">Пакет:</span><span class="sxs-lookup"><span data-stu-id="0f1de-154">The package:</span></span>

* <span data-ttu-id="0f1de-155">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-155">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="0f1de-156">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-156">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="0f1de-157">Включает атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-157">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="0f1de-158">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="0f1de-158">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="0f1de-159">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения пакета:</span><span class="sxs-lookup"><span data-stu-id="0f1de-159">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="0f1de-160">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="0f1de-160">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="0f1de-161">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="0f1de-161">Console app without an entry point</span></span>

<span data-ttu-id="0f1de-162">*Этот подход может использоваться только для приложений .NET Core, но не для приложений .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="0f1de-162">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="0f1de-163">Улучшение динамического размещения при запуске, для активации которого не требуется ссылка во время компиляции, может быть указано в консольном приложении без точки входа.</span><span class="sxs-lookup"><span data-stu-id="0f1de-163">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point.</span></span> <span data-ttu-id="0f1de-164">Приложение содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-164">The app contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="0f1de-165">Чтобы создать динамическое размещение при запуске, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="0f1de-165">To create a dynamic hosting startup:</span></span>

1. <span data-ttu-id="0f1de-166">Библиотека реализации создается на основе класса, содержащего реализацию `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-166">An implementation library is created from the class that contains the `IHostingStartup` implementation.</span></span> <span data-ttu-id="0f1de-167">Библиотека реализации считается обычным пакетом.</span><span class="sxs-lookup"><span data-stu-id="0f1de-167">The implementation library is treated as a normal package.</span></span>
1. <span data-ttu-id="0f1de-168">Консольное приложение без точки входа ссылается на пакет библиотеки реализации.</span><span class="sxs-lookup"><span data-stu-id="0f1de-168">A console app without an entry point references the implementation library package.</span></span> <span data-ttu-id="0f1de-169">Консольное приложение используется по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="0f1de-169">A console app is used because:</span></span>
   * <span data-ttu-id="0f1de-170">Файл зависимостей — это запускаемый ресурс приложения, поэтому библиотека не может предоставить файл зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0f1de-170">A dependencies file is a runnable app asset, so a library can't furnish a dependencies file.</span></span>
   * <span data-ttu-id="0f1de-171">Библиотеку невозможно добавить непосредственно в [хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store), для которого требуется запускаемый проект для общей среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-171">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>
1. <span data-ttu-id="0f1de-172">Консольное приложение публикуется для получения зависимостей начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="0f1de-172">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="0f1de-173">После публикации консольного приложения неиспользуемые зависимости исключаются из файла зависимостей.</span><span class="sxs-lookup"><span data-stu-id="0f1de-173">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
1. <span data-ttu-id="0f1de-174">Приложения и его файл зависимостей размещаются в хранилище пакетов среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-174">The app and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="0f1de-175">Чтобы обнаружить сборку начального размещения и ее файл зависимостей, ссылка на них осуществляется в паре переменных среды.</span><span class="sxs-lookup"><span data-stu-id="0f1de-175">To discover the hosting startup assembly and its dependencies file, they're referenced in a pair of environment variables.</span></span>

<span data-ttu-id="0f1de-176">Консольное приложение ссылается на пакет [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="0f1de-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="0f1de-177">Атрибут [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) определяет класс как реализацию `IHostingStartup` для загрузки и выполнения при построении [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span><span class="sxs-lookup"><span data-stu-id="0f1de-177">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span></span> <span data-ttu-id="0f1de-178">В следующем примере используется пространство имен `StartupEnhancement` и класс `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="0f1de-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="0f1de-179">Класс реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="0f1de-180">Метод класса [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) использует [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="0f1de-180">The class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="0f1de-181">`IHostingStartup.Configure` в начальной сборке размещения вызывается средой выполнения до `Startup.Configure` в пользовательском коде, что позволяет пользовательскому коду перезаписать конфигурацию, предоставленную начальной сборкой размещения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="0f1de-182">При создании проекта `IHostingStartup` файл зависимостей (*\*. deps.json*) задает расположение `runtime` для сборки в папке *bin*:</span><span class="sxs-lookup"><span data-stu-id="0f1de-182">When building an `IHostingStartup` project, the dependencies file (*\*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="0f1de-183">Показана только часть файла.</span><span class="sxs-lookup"><span data-stu-id="0f1de-183">Only part of the file is shown.</span></span> <span data-ttu-id="0f1de-184">Имя сборки в примере — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="0f1de-185">Указание начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="0f1de-185">Specify the hosting startup assembly</span></span>

<span data-ttu-id="0f1de-186">Для библиотеки класса или размещения при запуске с помощью консольного приложения укажите имя начальной сборки размещения в переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-186">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="0f1de-187">Переменная среды — это список сборок, разделенный точками с запятой.</span><span class="sxs-lookup"><span data-stu-id="0f1de-187">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="0f1de-188">Только начальные сборки размещения проверяются на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-188">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="0f1de-189">Для примера приложения *HostingStartupApp* необходимо установить следующее значение для переменной среды, чтобы обнаружить сборки размещения при запуске, описанные ранее:</span><span class="sxs-lookup"><span data-stu-id="0f1de-189">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="0f1de-190">Начальную сборку размещения также можно задать с помощью параметра конфигурации узла [Начальные сборки размещения](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="0f1de-190">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="0f1de-191">При наличии нескольких стартовых сборок размещения их методы [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) выполняются в порядке расположения сборок.</span><span class="sxs-lookup"><span data-stu-id="0f1de-191">When multiple hosting startup assembles are present, their [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="0f1de-192">Активация</span><span class="sxs-lookup"><span data-stu-id="0f1de-192">Activation</span></span>

<span data-ttu-id="0f1de-193">Ниже приведены варианты активации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="0f1de-193">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="0f1de-194">[Хранилище среды выполнения](#runtime-store) &ndash; для активации не требуется ссылка во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="0f1de-194">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="0f1de-195">Пример приложения помещает начальную сборку размещения и файлы зависимостей в папку *deployment*, чтобы облегчить развертывание размещения при запуске в среде с несколькими компьютерами.</span><span class="sxs-lookup"><span data-stu-id="0f1de-195">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="0f1de-196">Папка *deployment* также включает сценарий PowerShell, который создает или изменяет переменные среды в системе развертывания, чтобы включить размещение при запуске.</span><span class="sxs-lookup"><span data-stu-id="0f1de-196">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="0f1de-197">Ссылки во время компиляции, необходимые для активации</span><span class="sxs-lookup"><span data-stu-id="0f1de-197">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="0f1de-198">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="0f1de-198">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="0f1de-199">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="0f1de-199">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="0f1de-200">Хранилище среды выполнения</span><span class="sxs-lookup"><span data-stu-id="0f1de-200">Runtime store</span></span>

<span data-ttu-id="0f1de-201">Реализация размещения при запуске помещается в [хранилище среды выполнения](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="0f1de-201">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="0f1de-202">Ссылка на сборку во время компиляции не требуется расширенному приложению.</span><span class="sxs-lookup"><span data-stu-id="0f1de-202">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="0f1de-203">После сборки размещения при запуске файл проекта размещения при запуске выступает в качестве файла манифеста для команды [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="0f1de-203">After the hosting startup is built, the hosting startup's project file serves as the manifest file for the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```console
dotnet store --manifest <PROJECT_FILE> --runtime <RUNTIME_IDENTIFIER>
```

<span data-ttu-id="0f1de-204">Эта команда помещает начальную сборку размещения и другие зависимости, которые не являются частью общей платформы, в хранилище среды выполнения в профиле пользователя:</span><span class="sxs-lookup"><span data-stu-id="0f1de-204">This command places the hosting startup assembly and other dependencies that aren't part of the shared framework in the user profile's runtime store at:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f1de-205">Windows</span><span class="sxs-lookup"><span data-stu-id="0f1de-205">Windows</span></span>](#tab/windows)

```
%USERPROFILE%\.dotnet\store\x64\<TARGET_FRAMEWORK_MONIKER>\<ENHANCEMENT_ASSEMBLY_NAME>\<ENHANCEMENT_VERSION>\lib\<TARGET_FRAMEWORK_MONIKER>\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="0f1de-206">macOS</span><span class="sxs-lookup"><span data-stu-id="0f1de-206">macOS</span></span>](#tab/macos)

```
/Users/<USER>/.dotnet/store/x64/<TARGET_FRAMEWORK_MONIKER>/<ENHANCEMENT_ASSEMBLY_NAME>/<ENHANCEMENT_VERSION>/lib/<TARGET_FRAMEWORK_MONIKER>/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="0f1de-207">Linux</span><span class="sxs-lookup"><span data-stu-id="0f1de-207">Linux</span></span>](#tab/linux)

```
/Users/<USER>/.dotnet/store/x64/<TARGET_FRAMEWORK_MONIKER>/<ENHANCEMENT_ASSEMBLY_NAME>/<ENHANCEMENT_VERSION>/lib/<TARGET_FRAMEWORK_MONIKER>/
```

---

<span data-ttu-id="0f1de-208">Если вы хотите разместить сборку и зависимости для глобального использования, добавьте параметр `-o|--output` в команду `dotnet store`, указав следующий путь:</span><span class="sxs-lookup"><span data-stu-id="0f1de-208">If you desire to place the assembly and dependencies for global use, add the `-o|--output` option to the `dotnet store` command with the following path:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f1de-209">Windows</span><span class="sxs-lookup"><span data-stu-id="0f1de-209">Windows</span></span>](#tab/windows)

```
%PROGRAMFILES%\dotnet\store\x64\<TARGET_FRAMEWORK_MONIKER>\<ENHANCEMENT_ASSEMBLY_NAME>\<ENHANCEMENT_VERSION>\lib\<TARGET_FRAMEWORK_MONIKER>\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="0f1de-210">macOS</span><span class="sxs-lookup"><span data-stu-id="0f1de-210">macOS</span></span>](#tab/macos)

```
/usr/local/share/dotnet/store/x64/<TARGET_FRAMEWORK_MONIKER>/<ENHANCEMENT_ASSEMBLY_NAME>/<ENHANCEMENT_VERSION>/lib/<TARGET_FRAMEWORK_MONIKER>/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="0f1de-211">Linux</span><span class="sxs-lookup"><span data-stu-id="0f1de-211">Linux</span></span>](#tab/linux)

```
/usr/local/share/dotnet/store/x64/<TARGET_FRAMEWORK_MONIKER>/<ENHANCEMENT_ASSEMBLY_NAME>/<ENHANCEMENT_VERSION>/lib/<TARGET_FRAMEWORK_MONIKER>/
```

---

<span data-ttu-id="0f1de-212">**Изменение и размещение файла зависимостей размещения при запуске**</span><span class="sxs-lookup"><span data-stu-id="0f1de-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="0f1de-213">Расположение среды выполнения указывается в файле *\*.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="0f1de-213">The runtime location is specified in the *\*.deps.json* file.</span></span> <span data-ttu-id="0f1de-214">Для активации улучшения элемент `runtime` должен указывать на расположение среды выполнения сборки для улучшения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-214">To activate the enhancement, the `runtime` element must specify the location of the enhancement's runtime assembly.</span></span> <span data-ttu-id="0f1de-215">Расположение `runtime` должно иметь префикс `lib/<TARGET_FRAMEWORK_MONIKER>/`:</span><span class="sxs-lookup"><span data-stu-id="0f1de-215">Prefix the `runtime` location with `lib/<TARGET_FRAMEWORK_MONIKER>/`:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement2.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="0f1de-216">В коде примера (проект *StartupDiagnostics*) изменение файла *\*.deps.json* осуществляется сценарием [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="0f1de-216">In the sample code (*StartupDiagnostics* project), modification of the *\*.deps.json* file is performed by a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span> <span data-ttu-id="0f1de-217">Скрипт PowerShell запускается автоматически целевым объектом сборки в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="0f1de-217">The PowerShell script is automatically triggered by a build target in the project file.</span></span>

<span data-ttu-id="0f1de-218">Файл реализации *\*.deps.json* должен находиться в доступном расположении.</span><span class="sxs-lookup"><span data-stu-id="0f1de-218">The implementation's *\*.deps.json* file must be in an accessible location.</span></span>

<span data-ttu-id="0f1de-219">Для индивидуального использования поместите файл в папку *additionalDeps* в параметрах профиля пользователя `.dotnet`:</span><span class="sxs-lookup"><span data-stu-id="0f1de-219">For per-user use, place the file in the *additonalDeps* folder of the user profile's `.dotnet` settings:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f1de-220">Windows</span><span class="sxs-lookup"><span data-stu-id="0f1de-220">Windows</span></span>](#tab/windows)

```
%USERPROFILE%\.dotnet\x64\additionalDeps\<ENHANCEMENT_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\<SHARED_FRAMEWORK_VERSION>\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="0f1de-221">macOS</span><span class="sxs-lookup"><span data-stu-id="0f1de-221">macOS</span></span>](#tab/macos)

```
/Users/<USER>/.dotnet/x64/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="0f1de-222">Linux</span><span class="sxs-lookup"><span data-stu-id="0f1de-222">Linux</span></span>](#tab/linux)

```
/Users/<USER>/.dotnet/x64/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/
```

---

<span data-ttu-id="0f1de-223">Для глобального использования поместите файл в папку *additionalDeps* в установке .NET Core:</span><span class="sxs-lookup"><span data-stu-id="0f1de-223">For global use, place the file in the *additonalDeps* folder of the .NET Core installation:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f1de-224">Windows</span><span class="sxs-lookup"><span data-stu-id="0f1de-224">Windows</span></span>](#tab/windows)

```
%PROGRAMFILES%\dotnet\additionalDeps\<ENHANCEMENT_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\<SHARED_FRAMEWORK_VERSION>\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="0f1de-225">macOS</span><span class="sxs-lookup"><span data-stu-id="0f1de-225">macOS</span></span>](#tab/macos)

```
/usr/local/share/dotnet/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="0f1de-226">Linux</span><span class="sxs-lookup"><span data-stu-id="0f1de-226">Linux</span></span>](#tab/linux)

```
/usr/local/share/dotnet/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/
```

---

<span data-ttu-id="0f1de-227">Версия общей платформы отражает версию общей среды выполнения, которую использует целевое приложение.</span><span class="sxs-lookup"><span data-stu-id="0f1de-227">The shared framework version reflects the version of the shared runtime that the target app uses.</span></span> <span data-ttu-id="0f1de-228">Общая среда выполнения указана в файле *\*.runtimeconfig.json*.</span><span class="sxs-lookup"><span data-stu-id="0f1de-228">The shared runtime is shown in the *\*.runtimeconfig.json* file.</span></span> <span data-ttu-id="0f1de-229">В примере приложения (*HostingStartupApp*) общая среда выполнения задается в файле *HostingStartupApp.runtimeconfig.json*.</span><span class="sxs-lookup"><span data-stu-id="0f1de-229">In the sample app (*HostingStartupApp*), the shared runtime is specified in the *HostingStartupApp.runtimeconfig.json* file.</span></span>

<span data-ttu-id="0f1de-230">**Указание расположения для файла зависимостей размещения при запуске**</span><span class="sxs-lookup"><span data-stu-id="0f1de-230">**List the hosting startup's dependencies file**</span></span>

<span data-ttu-id="0f1de-231">Расположение файла зависимостей размещения *\*.deps.json* указывается в переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-231">The location of the implementation's *\*.deps.json* file is listed in the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="0f1de-232">Если файл размещен в папке *.dotnet* профиля пользователя, установите следующее значение переменной среды:</span><span class="sxs-lookup"><span data-stu-id="0f1de-232">If the file is placed in the user profile's *.dotnet* folder, set the environment variable's value to:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f1de-233">Windows</span><span class="sxs-lookup"><span data-stu-id="0f1de-233">Windows</span></span>](#tab/windows)

```
%USERPROFILE%\.dotnet\x64\additionalDeps\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="0f1de-234">macOS</span><span class="sxs-lookup"><span data-stu-id="0f1de-234">macOS</span></span>](#tab/macos)

```
/Users/<USER>/.dotnet/x64/additionalDeps/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="0f1de-235">Linux</span><span class="sxs-lookup"><span data-stu-id="0f1de-235">Linux</span></span>](#tab/linux)

```
/Users/<USER>/.dotnet/x64/additionalDeps/
```

---

<span data-ttu-id="0f1de-236">Если файл расположен в установке .NET Core для глобального использования, укажите полный путь к файлу:</span><span class="sxs-lookup"><span data-stu-id="0f1de-236">If the file is placed in the .NET Core installation for global use, provide the full path to the file:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f1de-237">Windows</span><span class="sxs-lookup"><span data-stu-id="0f1de-237">Windows</span></span>](#tab/windows)

```
%PROGRAMFILES%\dotnet\additionalDeps\<ENHANCEMENT_ASSEMBLY_NAME>\shared\Microsoft.NETCore.App\<SHARED_FRAMEWORK_VERSION>\<ENHANCEMENT_ASSEMBLY_NAME>.deps.json
```

# <a name="macostabmacos"></a>[<span data-ttu-id="0f1de-238">macOS</span><span class="sxs-lookup"><span data-stu-id="0f1de-238">macOS</span></span>](#tab/macos)

```
/usr/local/share/dotnet/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/<ENHANCEMENT_ASSEMBLY_NAME>.deps.json
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="0f1de-239">Linux</span><span class="sxs-lookup"><span data-stu-id="0f1de-239">Linux</span></span>](#tab/linux)

```
/usr/local/share/dotnet/additionalDeps/<ENHANCEMENT_ASSEMBLY_NAME>/shared/Microsoft.NETCore.App/<SHARED_FRAMEWORK_VERSION>/<ENHANCEMENT_ASSEMBLY_NAME>.deps.json
```

---

<span data-ttu-id="0f1de-240">Для примера приложения (*HostingStartupApp*) файл зависимостей (*HostingStartupApp.runtimeconfig.json*) размещается в профиле пользователя.</span><span class="sxs-lookup"><span data-stu-id="0f1de-240">For the sample app (*HostingStartupApp*) to find the dependencies file (*HostingStartupApp.runtimeconfig.json*), the dependencies file is placed in the user's profile.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f1de-241">Windows</span><span class="sxs-lookup"><span data-stu-id="0f1de-241">Windows</span></span>](#tab/windows)

<span data-ttu-id="0f1de-242">Установите следующее значение для переменной среды `DOTNET_ADDITIONAL_DEPS`:</span><span class="sxs-lookup"><span data-stu-id="0f1de-242">Set the `DOTNET_ADDITIONAL_DEPS` environment variable to the following value:</span></span>

```
%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\
```

# <a name="macostabmacos"></a>[<span data-ttu-id="0f1de-243">macOS</span><span class="sxs-lookup"><span data-stu-id="0f1de-243">macOS</span></span>](#tab/macos)

<span data-ttu-id="0f1de-244">Установите следующее значение для переменной среды `DOTNET_ADDITIONAL_DEPS`:</span><span class="sxs-lookup"><span data-stu-id="0f1de-244">Set the `DOTNET_ADDITIONAL_DEPS` environment variable to the following value:</span></span>

```
/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/
```

# <a name="linuxtablinux"></a>[<span data-ttu-id="0f1de-245">Linux</span><span class="sxs-lookup"><span data-stu-id="0f1de-245">Linux</span></span>](#tab/linux)

<span data-ttu-id="0f1de-246">Установите следующее значение для переменной среды `DOTNET_ADDITIONAL_DEPS`:</span><span class="sxs-lookup"><span data-stu-id="0f1de-246">Set the `DOTNET_ADDITIONAL_DEPS` environment variable to the following value:</span></span>

```
/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/
```

---

<span data-ttu-id="0f1de-247">Примеры установки переменных среды для различных операционных систем см. в разделе [Использование нескольких сред](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="0f1de-247">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0f1de-248">**Развертывание**</span><span class="sxs-lookup"><span data-stu-id="0f1de-248">**Deployment**</span></span>

<span data-ttu-id="0f1de-249">Чтобы упростить развертывание размещения при запуске в среде с несколькими компьютерами, пример приложения создает в опубликованном результате папку *deployment*. Эта папка содержит:</span><span class="sxs-lookup"><span data-stu-id="0f1de-249">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="0f1de-250">Начальную сборку размещения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-250">The hosting startup assembly.</span></span>
* <span data-ttu-id="0f1de-251">Файл зависимостей размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="0f1de-251">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="0f1de-252">Сценарий PowerShell, который создает или изменяет `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` и `DOTNET_ADDITIONAL_DEPS` для поддержки активации размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="0f1de-252">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="0f1de-253">Запустите сценарий из командной строки PowerShell с правами администратора в системе развертывания.</span><span class="sxs-lookup"><span data-stu-id="0f1de-253">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="0f1de-254">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="0f1de-254">NuGet package</span></span>

<span data-ttu-id="0f1de-255">Расширение размещения при запуске можно указать в пакете NuGet.</span><span class="sxs-lookup"><span data-stu-id="0f1de-255">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="0f1de-256">Пакет содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-256">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="0f1de-257">Типы размещения при запуске, предоставляемые пакетом, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="0f1de-257">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="0f1de-258">Файл проекта расширенного приложения создает ссылку на пакет для размещения при запуске в файле проекта приложения (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="0f1de-258">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="0f1de-259">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения (*\*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="0f1de-259">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="0f1de-260">Этот подход применяется к пакету начальной сборки размещения, опубликованному на [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="0f1de-260">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="0f1de-261">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="0f1de-261">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="0f1de-262">Дополнительные сведения о пакетах NuGet и хранилище среды выполнения см. в разделах:</span><span class="sxs-lookup"><span data-stu-id="0f1de-262">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="0f1de-263">Создание пакета NuGet с помощью кроссплатформенных средств</span><span class="sxs-lookup"><span data-stu-id="0f1de-263">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="0f1de-264">Публикация пакетов</span><span class="sxs-lookup"><span data-stu-id="0f1de-264">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="0f1de-265">Хранилище пакетов среды выполнения</span><span class="sxs-lookup"><span data-stu-id="0f1de-265">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="0f1de-266">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="0f1de-266">Project bin folder</span></span>

<span data-ttu-id="0f1de-267">Расширение размещения при запуске может быть представлено сборкой, разворачиваемой в папке *bin* расширенного приложения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-267">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="0f1de-268">Типы размещения при запуске, предоставляемые сборкой, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="0f1de-268">The hosting startup types provided by the assembly are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="0f1de-269">Файл проекта расширенного приложения создает ссылку сборки на размещение при запуске (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="0f1de-269">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="0f1de-270">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения (*\*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="0f1de-270">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*\*.deps.json*).</span></span> <span data-ttu-id="0f1de-271">Этот подход применяется, когда в сценарии развертывания используются вызовы для перемещения скомпилированной сборки библиотеки размещения при запуске (файла DLL) в принимающий проект или в расположение, доступное для принимающего проекта, и создается ссылка на сборку размещения при запуске во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="0f1de-271">This approach applies when the deployment scenario calls for moving the compiled hosting startup library's assembly (DLL file) to the consuming project or to a location accessible by the consuming project and a compile-time reference is made to the hosting startup's assembly.</span></span>
* <span data-ttu-id="0f1de-272">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="0f1de-272">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

## <a name="sample-code"></a><span data-ttu-id="0f1de-273">Пример кода</span><span class="sxs-lookup"><span data-stu-id="0f1de-273">Sample code</span></span>

<span data-ttu-id="0f1de-274">В [примере кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([инструкции по скачиванию примера](xref:tutorials/index#how-to-download-a-sample)) показаны сценарии реализации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="0f1de-274">The [sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:tutorials/index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="0f1de-275">В каждой из двух сборок начального размещения (библиотеки классов) устанавливаются две пары "ключ-значение", хранящиеся в памяти:</span><span class="sxs-lookup"><span data-stu-id="0f1de-275">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="0f1de-276">Пакет NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="0f1de-276">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="0f1de-277">Библиотека классов (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="0f1de-277">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="0f1de-278">Размещения при запуске активируется из сборки среды выполнения, развертываемой из хранилища (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="0f1de-278">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="0f1de-279">Эта сборка добавляет два вида ПО промежуточного слоя, которые предоставляют диагностические сведения о следующих показателях:</span><span class="sxs-lookup"><span data-stu-id="0f1de-279">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="0f1de-280">Зарегистрированные службы</span><span class="sxs-lookup"><span data-stu-id="0f1de-280">Registered services</span></span>
  * <span data-ttu-id="0f1de-281">Адрес (схема, узел, базовый путь, путь, строка запроса)</span><span class="sxs-lookup"><span data-stu-id="0f1de-281">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="0f1de-282">Подключение (удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента)</span><span class="sxs-lookup"><span data-stu-id="0f1de-282">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="0f1de-283">Заголовки запросов</span><span class="sxs-lookup"><span data-stu-id="0f1de-283">Request headers</span></span>
  * <span data-ttu-id="0f1de-284">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="0f1de-284">Environment variables</span></span>

<span data-ttu-id="0f1de-285">Для выполнения образца:</span><span class="sxs-lookup"><span data-stu-id="0f1de-285">To run the sample:</span></span>

<span data-ttu-id="0f1de-286">**Активация из пакета NuGet**</span><span class="sxs-lookup"><span data-stu-id="0f1de-286">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="0f1de-287">Скомпилируйте пакет *HostingStartupPackage* с помощью команды [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="0f1de-287">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="0f1de-288">Добавьте имя сборки пакета *HostingStartupPackage* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-288">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="0f1de-289">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="0f1de-289">Compile and run the app.</span></span> <span data-ttu-id="0f1de-290">Ссылка на пакет появится в расширенном приложении (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="0f1de-290">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="0f1de-291">Объект `<PropertyGroup>` в файле проекта приложения определяет выходные данные проекта пакета (*../ HostingStartupPackage/bin/Debug*) в качестве источника пакета.</span><span class="sxs-lookup"><span data-stu-id="0f1de-291">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="0f1de-292">Это позволяет приложению использовать пакет без отправки пакета на сайт [nuget.org](https://www.nuget.org/). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="0f1de-292">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```
1. <span data-ttu-id="0f1de-293">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода пакета `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-293">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="0f1de-294">Если вы внесли изменения в проект *HostingStartupPackage* и перекомпилировали его, очистите локальные кэши пакета NuGet, чтобы убедиться, что *HostingStartupApp* получает обновленный пакет, а не устаревший пакет из локального кэша.</span><span class="sxs-lookup"><span data-stu-id="0f1de-294">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="0f1de-295">Чтобы очистить локальные кэши NuGet, выполните следующую команду [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="0f1de-295">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```console
dotnet nuget locals all --clear
```

<span data-ttu-id="0f1de-296">**Активации из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="0f1de-296">**Activation from a class library**</span></span>

1. <span data-ttu-id="0f1de-297">Скомпилируйте библиотеку классов *HostingStartupLibrary* с помощью команды [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="0f1de-297">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="0f1de-298">Добавьте имя сборки библиотеки классов *HostingStartupLibrary* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-298">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="0f1de-299">Разверните сборку библиотеки классов в папку *bin* приложения. Для этого скопируйте файл *HostingStartupLibrary.dll* из выходной папки компиляции библиотеки в папку *bin/Debug* приложения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-299">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="0f1de-300">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="0f1de-300">Compile and run the app.</span></span> <span data-ttu-id="0f1de-301">`<ItemGroup>` в файле проекта приложения ссылается на сборку библиотеки класса (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="0f1de-301">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="0f1de-302">Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="0f1de-302">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```
1. <span data-ttu-id="0f1de-303">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода библиотеки класса `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-303">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="0f1de-304">**Активация из сборки среды выполнения, развернутой в хранилище**</span><span class="sxs-lookup"><span data-stu-id="0f1de-304">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="0f1de-305">В проекте *StartupDiagnostics* используется [PowerShell](/powershell/scripting/powershell-scripting) для изменения файла *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="0f1de-305">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="0f1de-306">PowerShell устанавливается по умолчанию в Windows начиная с Windows 7 с пакетом обновления 1 (SP1) и Windows Server 2008 R2 с пакетом обновления 1 (SP1).</span><span class="sxs-lookup"><span data-stu-id="0f1de-306">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="0f1de-307">Для установки PowerShell на других платформах см. раздел [Установка Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span><span class="sxs-lookup"><span data-stu-id="0f1de-307">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="0f1de-308">Соберите проект *StartupDiagnostics*.</span><span class="sxs-lookup"><span data-stu-id="0f1de-308">Build the *StartupDiagnostics* project.</span></span> <span data-ttu-id="0f1de-309">После сборки проекта цель сборки в файле проекта автоматически выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="0f1de-309">After the project is built, a build target in the project file automatically:</span></span>
   * <span data-ttu-id="0f1de-310">Запускает скрипт PowerShell, чтобы изменить файл *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="0f1de-310">Triggers the PowerShell script to modify the *StartupDiagnostics.deps.json* file.</span></span>
   * <span data-ttu-id="0f1de-311">Перемещает файл *StartupDiagnostics.deps.json* в папку *additionalDeps* в профиле пользователя.</span><span class="sxs-lookup"><span data-stu-id="0f1de-311">Moves the *StartupDiagnostics.deps.json* file to the user profile's *additionalDeps* folder.</span></span>
1. <span data-ttu-id="0f1de-312">Выполните команду `dotnet store` в командной строке каталога начального размещения, чтобы сохранить сборку и ее зависимости в хранилище среды выполнения профиля пользователя:</span><span class="sxs-lookup"><span data-stu-id="0f1de-312">Execute the `dotnet store` command at a command prompt in the hosting startup's directory to store the assembly and its dependencies in the user profile's runtime store:</span></span>

   ```console
   dotnet store --manifest StartupDiagnostics.csproj --runtime <RID>
   ```
   <span data-ttu-id="0f1de-313">В Windows команда использует [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog) `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-313">For Windows, the command uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="0f1de-314">При выполнении размещения при запуске для другой среды выполнения укажите соответствующий RID.</span><span class="sxs-lookup"><span data-stu-id="0f1de-314">When providing the hosting startup for a different runtime, substitute the correct RID.</span></span>
1. <span data-ttu-id="0f1de-315">Задайте переменные среды:</span><span class="sxs-lookup"><span data-stu-id="0f1de-315">Set the environment variables:</span></span>
   * <span data-ttu-id="0f1de-316">Добавьте имя сборки *StartupDiagnostics* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-316">Add the assembly name of *StartupDiagnostics* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="0f1de-317">В Windows установите значение `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\` для переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="0f1de-317">On Windows, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `%UserProfile%\.dotnet\x64\additionalDeps\StartupDiagnostics\`.</span></span> <span data-ttu-id="0f1de-318">В macOS и Linux, установите значение `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/` для переменной среды `DOTNET_ADDITIONAL_DEPS`, где `<USER>` — профиль пользователя, который содержит размещение при запуске.</span><span class="sxs-lookup"><span data-stu-id="0f1de-318">On macOS/Linux, set the `DOTNET_ADDITIONAL_DEPS` environment variable to `/Users/<USER>/.dotnet/x64/additionalDeps/StartupDiagnostics/`, where `<USER>` is the user profile that contains the hosting startup.</span></span>
1. <span data-ttu-id="0f1de-319">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-319">Run the sample app.</span></span>
1. <span data-ttu-id="0f1de-320">Запросите конечную точку `/services` для просмотра зарегистрированных служб приложения.</span><span class="sxs-lookup"><span data-stu-id="0f1de-320">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="0f1de-321">Запросите конечную точку `/diag` для просмотра диагностических сведений.</span><span class="sxs-lookup"><span data-stu-id="0f1de-321">Request the `/diag` endpoint to see the diagnostic information.</span></span>
