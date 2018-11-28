---
title: Добавление модели в приложение Razor Pages в ASP.NET Core
author: rick-anderson
description: Узнайте, как добавлять классы для управления фильмами в базе данных с использованием Entity Framework Core (EF Core).
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: c4b23f75da298e4ee804f649219c2ce466b6d6ea
ms.sourcegitcommit: 710fc5fcac258cc8415976dc66bdb355b3e061d5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52299447"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="f4abf-103">Добавление модели в приложение Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4abf-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [model1](~/includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="f4abf-104">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="f4abf-104">Add a data model</span></span>

<span data-ttu-id="f4abf-105">В обозревателе решений щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-105">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="f4abf-106">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="f4abf-106">Name the folder *Models*.</span></span>

<span data-ttu-id="f4abf-107">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="f4abf-107">Right click the *Models* folder.</span></span> <span data-ttu-id="f4abf-108">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-108">Select **Add** > **Class**.</span></span> <span data-ttu-id="f4abf-109">Назначьте классу имя **Movie** и замените содержимое класса `Movie` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="f4abf-109">Name the class **Movie** and replace the contents of the `Movie` class with the following code:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie21/Models/Movie1.cs?name=snippet)]

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f4abf-110">Создание модели фильма</span><span class="sxs-lookup"><span data-stu-id="f4abf-110">Scaffold the movie model</span></span>

<span data-ttu-id="f4abf-111">В этом разделе создается модель фильма.</span><span class="sxs-lookup"><span data-stu-id="f4abf-111">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f4abf-112">То есть средство формирования шаблонов создает страницы для операций создания, чтения, обновления и удаления для модели фильма.</span><span class="sxs-lookup"><span data-stu-id="f4abf-112">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<span data-ttu-id="f4abf-113">Создайте папку *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="f4abf-113">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="f4abf-114">В **Обозревателе решений** щелкните правой кнопкой мыши на папку *Pages* > **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-114">In **Solution Explorer**, right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="f4abf-115">Назовите папку *Movies*</span><span class="sxs-lookup"><span data-stu-id="f4abf-115">Name the folder *Movies*</span></span>

<span data-ttu-id="f4abf-116">В **Обозревателе решений** щелкните правой кнопкой мыши на папку *Pages/Movies* > **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-116">In **Solution Explorer**, right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/sca.png)

<span data-ttu-id="f4abf-118">В диалоговом окне **Добавить шаблон** выберите **Razor Pages с помощью Entity Framework (CRUD)** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-118">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/add_scaffold.png)

<span data-ttu-id="f4abf-120">Заполните поля в диалоговом окне **Добавить Razor Pages с помощью Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="f4abf-120">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f4abf-121">В раскрывающемся списке **Класс модели** выберите **Фильм (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-121">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="f4abf-122">В строке **Класс контекста данных** нажмите на значок плюса **+** и примите сгенерированное имя **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-122">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="f4abf-123">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-123">Select **Add**.</span></span>

![Изображение из предыдущих инструкций.](model/_static/arp.png)

<span data-ttu-id="f4abf-125">В процессе формирования шаблонов создаются и обновляются указанные ниже файлы.</span><span class="sxs-lookup"><span data-stu-id="f4abf-125">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="f4abf-126">Создаваемые файлы</span><span class="sxs-lookup"><span data-stu-id="f4abf-126">Files created</span></span>

* <span data-ttu-id="f4abf-127">*Pages/Movies*: Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="f4abf-127">*Pages/Movies*: Create, Delete, Details, Edit, Index.</span></span> <span data-ttu-id="f4abf-128">Эти страницы подробно описываются в следующем учебнике.</span><span class="sxs-lookup"><span data-stu-id="f4abf-128">These pages are detailed in the next tutorial.</span></span>
* <span data-ttu-id="f4abf-129">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f4abf-129">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="f4abf-130">Обновляемые файлы</span><span class="sxs-lookup"><span data-stu-id="f4abf-130">File updated</span></span>

* <span data-ttu-id="f4abf-131">*Startup.cs*. Изменения в этом файле подробно описываются в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="f4abf-131">*Startup.cs*: Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="f4abf-132">*appsettings.json*. Добавляется строка подключения, используемая для подключения к локальной базе данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-132">*appsettings.json*: The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f4abf-133">Проверка контекста, зарегистрированного с помощью внедрения зависимостей</span><span class="sxs-lookup"><span data-stu-id="f4abf-133">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f4abf-134">ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f4abf-134">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f4abf-135">С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="f4abf-135">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f4abf-136">Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора.</span><span class="sxs-lookup"><span data-stu-id="f4abf-136">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f4abf-137">Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="f4abf-137">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f4abf-138">Средство формирования шаблонов автоматически создает контекст базы данных и регистрирует его с использованием контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="f4abf-138">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f4abf-139">Проверьте метод `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f4abf-139">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f4abf-140">Средством формирования шаблонов была добавлена выделенная строка:</span><span class="sxs-lookup"><span data-stu-id="f4abf-140">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Startup.cs?name=snippet_ConfigureServices&highlight=12-13)]

<span data-ttu-id="f4abf-141">Контекст базы данных — это класс main, который координирует функциональные возможности EF Core для заданной модели данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-141">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="f4abf-142">Контекст данных получен из [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="f4abf-142">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f4abf-143">Контекст данных указывает сущности, которые включаются в модель данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-143">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="f4abf-144">В этом проекте соответствующий класс называется `RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="f4abf-144">In this project, the class is named `RazorPagesMovieContext`.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f4abf-145">Представленный выше код создает свойство [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) для набора сущностей.</span><span class="sxs-lookup"><span data-stu-id="f4abf-145">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f4abf-146">В терминологии Entity Framework набор сущностей обычно соответствует таблице базы данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-146">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f4abf-147">Сущность соответствует строке в таблице.</span><span class="sxs-lookup"><span data-stu-id="f4abf-147">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f4abf-148">Имя строки подключения передается в контекст путем вызова метода для объекта [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="f4abf-148">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f4abf-149">При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f4abf-149">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="pmc"></a>
## <a name="perform-initial-migration"></a><span data-ttu-id="f4abf-150">Выполнение первоначальной миграции</span><span class="sxs-lookup"><span data-stu-id="f4abf-150">Perform initial migration</span></span>

<span data-ttu-id="f4abf-151">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий.</span><span class="sxs-lookup"><span data-stu-id="f4abf-151">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="f4abf-152">Добавления первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="f4abf-152">Add an initial migration.</span></span>
* <span data-ttu-id="f4abf-153">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="f4abf-153">Update the database with the initial migration.</span></span>

<span data-ttu-id="f4abf-154">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-154">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="f4abf-156">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="f4abf-156">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<span data-ttu-id="f4abf-157">Кроме того, можно использовать следующие команды .NET Core CLI из папки проекта:</span><span class="sxs-lookup"><span data-stu-id="f4abf-157">Alternatively, the following .NET Core CLI commands can be used from the project folder:</span></span>

```console
dotnet ef migrations add Initial
dotnet ef database update
```

<span data-ttu-id="f4abf-158">Не обращайте внимание на это предупреждающее сообщение, причина которого будет устранена в одном из следующих руководств:</span><span class="sxs-lookup"><span data-stu-id="f4abf-158">Ignore the following warning message, which you fix in a later tutorial:</span></span>

```console
Microsoft.EntityFrameworkCore.Model.Validation[30000]
      No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'.
```

<span data-ttu-id="f4abf-159">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-159">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="f4abf-160">Схема создается на основе модели, указанной в `RazorPagesMovieContext` (в файле *Data/RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="f4abf-160">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="f4abf-161">Аргумент `Initial` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="f4abf-161">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="f4abf-162">Можно использовать любое имя, но обычно выбирается имя, описывающее миграцию.</span><span class="sxs-lookup"><span data-stu-id="f4abf-162">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="f4abf-163">Дополнительные сведения см. в статье [Введение в миграции](xref:data/ef-mvc/migrations#introduction-to-migrations).</span><span class="sxs-lookup"><span data-stu-id="f4abf-163">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="f4abf-164">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/{time-stamp}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-164">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<span data-ttu-id="f4abf-165">Если возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="f4abf-165">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="f4abf-166">Вы пропустили [шаг миграции](#pmc).</span><span class="sxs-lookup"><span data-stu-id="f4abf-166">You missed the [migrations step](#pmc).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!INCLUDE [model1](~/includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="f4abf-167">Добавление модели данных</span><span class="sxs-lookup"><span data-stu-id="f4abf-167">Add a data model</span></span>

<span data-ttu-id="f4abf-168">В обозревателе решений щелкните проект **RazorPagesMovie** правой кнопкой мыши и выберите пункт **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-168">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="f4abf-169">Присвойте папке имя *Models*.</span><span class="sxs-lookup"><span data-stu-id="f4abf-169">Name the folder *Models*.</span></span>

<span data-ttu-id="f4abf-170">Щелкните правой кнопкой мыши папку *Models*.</span><span class="sxs-lookup"><span data-stu-id="f4abf-170">Right click the *Models* folder.</span></span> <span data-ttu-id="f4abf-171">Выберите **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-171">Select **Add** > **Class**.</span></span> <span data-ttu-id="f4abf-172">Добавьте класс **Movie** и следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="f4abf-172">Name the class **Movie** and add the following properties:</span></span>

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a><span data-ttu-id="f4abf-173">Добавление строки подключения базы данных</span><span class="sxs-lookup"><span data-stu-id="f4abf-173">Add a database connection string</span></span>

<span data-ttu-id="f4abf-174">Добавьте строку подключения в файл *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f4abf-174">Add a connection string to the *appsettings.json* file.</span></span>

[!code-json[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a><span data-ttu-id="f4abf-175">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="f4abf-175">Register the database context</span></span>

<span data-ttu-id="f4abf-176">Зарегистрируйте контекст базы данных в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) в [методе ConfigureServices класса Startup](xref:fundamentals/startup#the-startup-class) (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f4abf-176">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in the [ConfigureServices method of the Startup class](xref:fundamentals/startup#the-startup-class) (*Startup.cs*):</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-5,7-9)]

<span data-ttu-id="f4abf-177">Выполните сборку проекта, чтобы убедиться в отсутствии ошибок.</span><span class="sxs-lookup"><span data-stu-id="f4abf-177">Build the project to verify you don't have any errors.</span></span>

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a><span data-ttu-id="f4abf-178">Добавление средств формирования шаблонов и выполнение первоначальной миграции</span><span class="sxs-lookup"><span data-stu-id="f4abf-178">Add scaffold tooling and perform initial migration</span></span>

<span data-ttu-id="f4abf-179">В этом разделе консоль диспетчера пакетов (PMC) используется для выполнения следующих действий.</span><span class="sxs-lookup"><span data-stu-id="f4abf-179">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="f4abf-180">Добавление веб-пакета создания кода для Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f4abf-180">Add the Visual Studio web code generation package.</span></span> <span data-ttu-id="f4abf-181">Этот пакет необходим для работы ядра формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="f4abf-181">This package is required to run the scaffolding engine.</span></span>
* <span data-ttu-id="f4abf-182">Добавление первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="f4abf-182">Add an initial migration.</span></span>
* <span data-ttu-id="f4abf-183">Обновления базы данных с помощью первоначальной миграции.</span><span class="sxs-lookup"><span data-stu-id="f4abf-183">Update the database with the initial migration.</span></span>

<span data-ttu-id="f4abf-184">В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-184">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Меню PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="f4abf-186">В PMC введите следующие команды:</span><span class="sxs-lookup"><span data-stu-id="f4abf-186">In the PMC, enter the following commands:</span></span>

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 2.0.3
Add-Migration Initial
Update-Database
```

<span data-ttu-id="f4abf-187">Кроме того, можно использовать следующие команды .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="f4abf-187">Alternatively, the following .NET Core CLI commands can be used:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet ef migrations add Initial
dotnet ef database update
```

<span data-ttu-id="f4abf-188">Не обращайте внимание на следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="f4abf-188">Ignore the following message:</span></span>

```console
Microsoft.EntityFrameworkCore.Model.Validation[30000]
      No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'
```

<span data-ttu-id="f4abf-189">Мы исправим это в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="f4abf-189">You fix that in the next tutorial.</span></span>

<span data-ttu-id="f4abf-190">Команда `Install-Package` устанавливает средства, необходимые для запуска ядра формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="f4abf-190">The `Install-Package` command installs the tooling required to run the scaffolding engine.</span></span>

<span data-ttu-id="f4abf-191">Команда `Add-Migration` формирует код для создания схемы исходной базы данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-191">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="f4abf-192">Схема создается на основе модели, указанной в `DbContext` (в файле *Models/MovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="f4abf-192">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="f4abf-193">Аргумент `Initial` используется для присвоения имен миграциям.</span><span class="sxs-lookup"><span data-stu-id="f4abf-193">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="f4abf-194">Можно использовать любое имя, но обычно выбирается имя, описывающее миграцию.</span><span class="sxs-lookup"><span data-stu-id="f4abf-194">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="f4abf-195">Дополнительные сведения см. в статье [Введение в миграции](xref:data/ef-mvc/migrations#introduction-to-migrations).</span><span class="sxs-lookup"><span data-stu-id="f4abf-195">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="f4abf-196">Команда `Update-Database` выполняет метод `Up` в файле *Migrations/{time-stamp}_InitialCreate.cs*, который создает базу данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-196">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [model 4windows](~/includes/RP/model4Win.md)]

[!INCLUDE [model 4](~/includes/RP/model4tbl.md)]

::: moniker-end

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="f4abf-197">Тестирование приложения</span><span class="sxs-lookup"><span data-stu-id="f4abf-197">Test the app</span></span>

* <span data-ttu-id="f4abf-198">Запустите приложение и добавьте `/Movies` к URL-адресу в браузере (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="f4abf-198">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="f4abf-199">Протестируйте ссылку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-199">Test the **Create** link.</span></span>

  ![Страница "Создать"](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="f4abf-201">Протестируйте ссылки **Изменить**, **Сведения** и **Удалить**.</span><span class="sxs-lookup"><span data-stu-id="f4abf-201">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f4abf-202">Если появляется исключение SQL, выполните миграции и обновите базу данных.</span><span class="sxs-lookup"><span data-stu-id="f4abf-202">If you get a SQL exception, verify you have run migrations and updated the database.</span></span>

<span data-ttu-id="f4abf-203">В следующем учебнике рассматриваются файлы, созданные с помощью формирования шаблонов.</span><span class="sxs-lookup"><span data-stu-id="f4abf-203">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f4abf-204">[Предыдущая статья — "Начало работы"](xref:tutorials/razor-pages/razor-pages-start)
> [Следующая статья — "Сформированные страницы Razor Pages"](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="f4abf-204">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>
