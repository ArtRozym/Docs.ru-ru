---
title: ASP.NET Core MVC и EF Core — обновление связанных данных — 7 из 10
author: rick-anderson
description: В этом руководстве описано обновление связанных данных путем обновления полей внешнего ключа и свойств навигации.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 37985c945f2e4b15cfcefb0c126c3209e0bdeac4
ms.sourcegitcommit: 4d74644f11e0dac52b4510048490ae731c691496
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50090737"
---
# <a name="aspnet-core-mvc-with-ef-core---update-related-data---7-of-10"></a><span data-ttu-id="7712a-103">ASP.NET Core MVC и EF Core — обновление связанных данных — 7 из 10</span><span class="sxs-lookup"><span data-stu-id="7712a-103">ASP.NET Core MVC with EF Core - Update Related Data - 7 of 10</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc-21.md)]

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7712a-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="7712a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7712a-105">На примере учебного веб-приложения "Университет Contoso" демонстрируется процесс создания веб-приложений ASP.NET Core MVC с помощью Entity Framework Core и Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7712a-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="7712a-106">Сведения о серии руководств см. в [первом руководстве серии](intro.md).</span><span class="sxs-lookup"><span data-stu-id="7712a-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="7712a-107">В предыдущем руководстве вы отобразили связанные данные. В этом руководстве описано обновление связанных данных путем обновления полей внешнего ключа и свойств навигации.</span><span class="sxs-lookup"><span data-stu-id="7712a-107">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="7712a-108">На следующих рисунках изображены некоторые из страниц, с которыми вы будете работать.</span><span class="sxs-lookup"><span data-stu-id="7712a-108">The following illustrations show some of the pages that you'll work with.</span></span>

![Страница редактирования курса](update-related-data/_static/course-edit.png)

![Страница редактирования преподавателя](update-related-data/_static/instructor-edit-courses.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a><span data-ttu-id="7712a-111">Настройка страниц создания и редактирования для курсов</span><span class="sxs-lookup"><span data-stu-id="7712a-111">Customize the Create and Edit Pages for Courses</span></span>

<span data-ttu-id="7712a-112">Создаваемая сущность курса должна иметь связь с существующей кафедрой.</span><span class="sxs-lookup"><span data-stu-id="7712a-112">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="7712a-113">Чтобы упростить эту задачу, шаблонный код включает методы контроллеров, а также представления "Create" (Создание) и "Edit" (Редактирование) с раскрывающимся списком для выбора кафедры.</span><span class="sxs-lookup"><span data-stu-id="7712a-113">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="7712a-114">Раскрывающийся список задает свойство внешнего ключа `Course.DepartmentID`, и это все, что нужно Entity Framework для загрузки свойства навигации `Department` с соответствующей сущностью Department.</span><span class="sxs-lookup"><span data-stu-id="7712a-114">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="7712a-115">Вы будете использовать этот шаблонный код, немного его изменив, чтобы добавить обработку ошибок и сортировку раскрывающегося списка.</span><span class="sxs-lookup"><span data-stu-id="7712a-115">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="7712a-116">В *CoursesController.cs* удалите методы Create и Edit и замените их следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="7712a-116">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="7712a-117">После метода HttpPost `Edit` создайте метод, загружающий сведения о кафедре для раскрывающегося списка.</span><span class="sxs-lookup"><span data-stu-id="7712a-117">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="7712a-118">Метод `PopulateDepartmentsDropDownList` возвращает список всех кафедр, отсортированных по имени, создает коллекцию `SelectList` для раскрывающегося списка и передает ее в представление в `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="7712a-118">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="7712a-119">Этот метод принимает необязательный параметр `selectedDepartment`, позволяющий вызывающему коду указать элемент, который будет выбран при отрисовке раскрывающегося списка.</span><span class="sxs-lookup"><span data-stu-id="7712a-119">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="7712a-120">Представление передаст имя "DepartmentID" во вспомогательную функцию тегов `<select>`, после чего ей станет известно, что нужно искать в объекте `ViewBag` коллекцию `SelectList` с именем "DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="7712a-120">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="7712a-121">Метод HttpGet `Create` вызывает метод `PopulateDepartmentsDropDownList` без установки выбранного элемента, так как кафедра для нового курса еще не задана:</span><span class="sxs-lookup"><span data-stu-id="7712a-121">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="7712a-122">Метод HttpGet `Edit` задает выбранный элемент на основе идентификатора кафедры, который уже назначен редактируемому курсу:</span><span class="sxs-lookup"><span data-stu-id="7712a-122">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="7712a-123">Методы HttpPost для `Create` и `Edit` также содержат код, который задает выбранный элемент, когда они повторно отображают страницу после ошибки.</span><span class="sxs-lookup"><span data-stu-id="7712a-123">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="7712a-124">Это гарантирует, что при повторном отображении страницы для вывода сообщения об ошибке сохраняется выбор кафедры.</span><span class="sxs-lookup"><span data-stu-id="7712a-124">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="7712a-125">Добавление .AsNoTrackin в методы Details и Delete</span><span class="sxs-lookup"><span data-stu-id="7712a-125">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="7712a-126">Чтобы оптимизировать производительность страниц "Details" (Сведения) и "Delete" (Удаление) курса, добавьте вызовы `AsNoTracking` в методы `Details` и HttpGet `Delete`.</span><span class="sxs-lookup"><span data-stu-id="7712a-126">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="7712a-127">Изменение представлений курса</span><span class="sxs-lookup"><span data-stu-id="7712a-127">Modify the Course views</span></span>

<span data-ttu-id="7712a-128">Во *Views/Courses/Create.cshtml* добавьте параметр "Select Department" (Выбрать кафедру) в раскрывающийся список **Department** (Кафедра), измените заголовок с **DepartmentID** на **Department** и добавьте сообщение о проверке.</span><span class="sxs-lookup"><span data-stu-id="7712a-128">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

<span data-ttu-id="7712a-129">Во *Views/Courses/Edit.cshtml* внесите для поля "Department" (Кафедра) изменение, аналогичное внесенному в *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7712a-129">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="7712a-130">Кроме того, добавьте во *Views/Courses/Edit.cshtml* поле номера курса перед полем **Title** (Название).</span><span class="sxs-lookup"><span data-stu-id="7712a-130">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="7712a-131">Так как номер курса является первичным ключом, он отображается, но не может быть изменен.</span><span class="sxs-lookup"><span data-stu-id="7712a-131">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="7712a-132">Представление "Edit" (Редактирование) уже содержит скрытое поле (`<input type="hidden">`) для номера курса.</span><span class="sxs-lookup"><span data-stu-id="7712a-132">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="7712a-133">Добавление вспомогательной функции тегов `<label>` не устраняет потребность в этом скрытом поле, так как не приводит к включению номера курса в передаваемые данные, когда пользователь нажимает кнопку **Save** (Сохранить) на странице **Edit** (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="7712a-133">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="7712a-134">Во *Views/Courses/Delete.cshtml* добавьте поле номера курса в верхней части страницы и измените идентификатор кафедры на ее имя.</span><span class="sxs-lookup"><span data-stu-id="7712a-134">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="7712a-135">Во *Views/Courses/Details.cshtml* внесите изменение, аналогичное внесенному в *Delete.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7712a-135">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="7712a-136">Тестирование страниц курса</span><span class="sxs-lookup"><span data-stu-id="7712a-136">Test the Course pages</span></span>

<span data-ttu-id="7712a-137">Запустите приложение, выберите вкладку **Courses** (Курсы), щелкните **Create New** (Создать) и введите данные для нового курса:</span><span class="sxs-lookup"><span data-stu-id="7712a-137">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![Страница "Create" (Создание) курса](update-related-data/_static/course-create.png)

<span data-ttu-id="7712a-139">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="7712a-139">Click **Create**.</span></span> <span data-ttu-id="7712a-140">Отображается страница индекса курсов, где в список добавлен новый курс.</span><span class="sxs-lookup"><span data-stu-id="7712a-140">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="7712a-141">Название кафедры в списке страницы индекса поступает из свойства навигации, показывая, что связь установлена правильно.</span><span class="sxs-lookup"><span data-stu-id="7712a-141">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="7712a-142">Нажмите кнопку **Edit** (Изменить) на странице индекса курсов.</span><span class="sxs-lookup"><span data-stu-id="7712a-142">Click **Edit** on a course in the Courses Index page.</span></span>

![Страница редактирования курса](update-related-data/_static/course-edit.png)

<span data-ttu-id="7712a-144">Измените данные на странице и нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="7712a-144">Change data on the page and click **Save**.</span></span> <span data-ttu-id="7712a-145">Отображается страница индекса курсов с обновленными данными о курсах.</span><span class="sxs-lookup"><span data-stu-id="7712a-145">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-an-edit-page-for-instructors"></a><span data-ttu-id="7712a-146">Добавление страницы редактирования для преподавателей</span><span class="sxs-lookup"><span data-stu-id="7712a-146">Add an Edit Page for Instructors</span></span>

<span data-ttu-id="7712a-147">При редактировании записи преподавателя может потребоваться обновить назначенный преподавателю кабинет.</span><span class="sxs-lookup"><span data-stu-id="7712a-147">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="7712a-148">Сущность Instructor имеет связь один к нулю или к одному с сущностью OfficeAssignment, что означает, что код должен обрабатывать следующие ситуации:</span><span class="sxs-lookup"><span data-stu-id="7712a-148">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="7712a-149">Если пользователь сбрасывает назначение кабинета, которое изначально имело некоторое значение, удалите сущность OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="7712a-149">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="7712a-150">Если пользователь вводит значение для назначения кабинета, которое изначально было пустым, создайте сущность OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="7712a-150">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="7712a-151">Если пользователь изменяет значение для назначения кабинета, измените значение в существующей сущности OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="7712a-151">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="7712a-152">Обновление контроллера преподавателей</span><span class="sxs-lookup"><span data-stu-id="7712a-152">Update the Instructors controller</span></span>

<span data-ttu-id="7712a-153">В *InstructorsController.cs* измените код метода HttpGet `Edit`, чтобы он загружал свойство навигации `OfficeAssignment` сущности Instructor и вызывал `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="7712a-153">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

<span data-ttu-id="7712a-154">Замените метод HttpPost `Edit` следующим кодом, чтобы обрабатывать обновления назначения кабинета:</span><span class="sxs-lookup"><span data-stu-id="7712a-154">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="7712a-155">Этот код выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="7712a-155">The code does the following:</span></span>

-  <span data-ttu-id="7712a-156">Изменяет имя метода на `EditPost`, так как сигнатура теперь аналогична методу HttpGet `Edit` (атрибут `ActionName` указывает, что URL-адрес `/Edit/` по-прежнему используется).</span><span class="sxs-lookup"><span data-stu-id="7712a-156">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

-  <span data-ttu-id="7712a-157">Получает текущую сущность Instructor из базы данных, используя безотложную загрузку для свойства навигации `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="7712a-157">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="7712a-158">Это аналогично тому, что вы сделали в методе HttpGet `Edit`.</span><span class="sxs-lookup"><span data-stu-id="7712a-158">This is the same as what you did in the HttpGet `Edit` method.</span></span>

-  <span data-ttu-id="7712a-159">Обновляет извлеченную сущность Instructor, используя значения из связывателя модели.</span><span class="sxs-lookup"><span data-stu-id="7712a-159">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="7712a-160">Перегрузка `TryUpdateModel` позволяет добавить включаемые свойства в список разрешений.</span><span class="sxs-lookup"><span data-stu-id="7712a-160">The `TryUpdateModel` overload enables you to whitelist the properties you want to include.</span></span> <span data-ttu-id="7712a-161">Это защищает от чрезмерной передачи данных, как описано во [втором руководстве](crud.md).</span><span class="sxs-lookup"><span data-stu-id="7712a-161">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

-   <span data-ttu-id="7712a-162">Если расположение кабинета отсутствует, задает для свойства Instructor.OfficeAssignment значение null, что приводит к удалению связанной строки в таблице OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="7712a-162">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- <span data-ttu-id="7712a-163">Сохраняет изменения в базу данных.</span><span class="sxs-lookup"><span data-stu-id="7712a-163">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="7712a-164">Обновление представления редактирования преподавателя</span><span class="sxs-lookup"><span data-stu-id="7712a-164">Update the Instructor Edit view</span></span>

<span data-ttu-id="7712a-165">В конце *Views/Instructors/Edit.cshtml*, перед кнопкой **Save** (Сохранить), добавьте новое поле для редактирования расположения кабинета:</span><span class="sxs-lookup"><span data-stu-id="7712a-165">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="7712a-166">Запустите приложение, выберите вкладку **Instructors** (Преподаватели), а затем щелкните **Edit** (Изменить) для преподавателя.</span><span class="sxs-lookup"><span data-stu-id="7712a-166">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="7712a-167">Измените значение **Office Location** (Расположение кабинета) и нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="7712a-167">Change the **Office Location** and click **Save**.</span></span>

![Страница редактирования преподавателя](update-related-data/_static/instructor-edit-office.png)

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="7712a-169">Добавление назначений курсов на страницу редактирования преподавателя</span><span class="sxs-lookup"><span data-stu-id="7712a-169">Add Course assignments to the Instructor Edit page</span></span>

<span data-ttu-id="7712a-170">Преподаватели могут вести любое число курсов.</span><span class="sxs-lookup"><span data-stu-id="7712a-170">Instructors may teach any number of courses.</span></span> <span data-ttu-id="7712a-171">Теперь вы улучшите страницу редактирования преподавателя, добавив возможность изменять назначения курсов с помощью группы флажков, как показано на следующем снимке экрана:</span><span class="sxs-lookup"><span data-stu-id="7712a-171">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Страница редактирования преподавателя с курсами](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="7712a-173">Сущности Course и Instructor имеют связь многие ко многим.</span><span class="sxs-lookup"><span data-stu-id="7712a-173">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="7712a-174">Для добавления и удаления связей можно добавлять сущности в список объединенного набора сущностей CourseAssignments и удалять их из него.</span><span class="sxs-lookup"><span data-stu-id="7712a-174">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="7712a-175">Пользовательский интерфейс, позволяющий изменить назначенные для преподавателя курсы, представляет собой группу флажков.</span><span class="sxs-lookup"><span data-stu-id="7712a-175">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="7712a-176">Отображается флажок для каждого курса в базе данных, а флажки для курсов, назначенных данному преподавателю, установлены.</span><span class="sxs-lookup"><span data-stu-id="7712a-176">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="7712a-177">Пользователь может устанавливать и снимать флажки, изменяя назначения курсов.</span><span class="sxs-lookup"><span data-stu-id="7712a-177">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="7712a-178">Если бы количество курсов было значительно больше, возможно, вам потребовалось бы использовать другой метод отображения данных в этом представлении, но вы бы использовали тот же самый способ управления сущностью объединения для создания и удаления связей.</span><span class="sxs-lookup"><span data-stu-id="7712a-178">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="7712a-179">Обновление контроллера преподавателей</span><span class="sxs-lookup"><span data-stu-id="7712a-179">Update the Instructors controller</span></span>

<span data-ttu-id="7712a-180">Чтобы предоставить данные в представлении для списка флажков, нужно использовать класс модели представления.</span><span class="sxs-lookup"><span data-stu-id="7712a-180">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="7712a-181">Создайте в папке *SchoolViewModels* файл *AssignedCourseData.cs* и замените существующий код следующим:</span><span class="sxs-lookup"><span data-stu-id="7712a-181">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="7712a-182">В файле *InstructorsController.cs* замените код метода HttpGet `Edit` приведенным ниже кодом.</span><span class="sxs-lookup"><span data-stu-id="7712a-182">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="7712a-183">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="7712a-183">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="7712a-184">Код добавляет безотложную загрузку для свойства навигации `Courses` и вызывает новый метод `PopulateAssignedCourseData` для предоставления сведений массиву флажков с помощью класса модели представления `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="7712a-184">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="7712a-185">Код в методе `PopulateAssignedCourseData` считывает все сущности Course, чтобы загрузить список курсов, используя класс модели представления.</span><span class="sxs-lookup"><span data-stu-id="7712a-185">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="7712a-186">Для каждого курса код проверяет, существует ли этот курс в свойстве навигации `Courses` преподавателя.</span><span class="sxs-lookup"><span data-stu-id="7712a-186">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="7712a-187">Чтобы создать эффективную подстановку при проверке того, назначен ли курс преподавателю, назначаемые курсы помещаются в коллекцию `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="7712a-187">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="7712a-188">У курсов, назначенных преподавателю, для свойства `Assigned` задается значение true.</span><span class="sxs-lookup"><span data-stu-id="7712a-188">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="7712a-189">Представление будет использовать это свойство, чтобы определить, какие флажки нужно отображать как выбранные.</span><span class="sxs-lookup"><span data-stu-id="7712a-189">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="7712a-190">Наконец, список передается в представление в `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="7712a-190">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="7712a-191">Добавьте код, выполняемый, когда пользователь нажимает кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="7712a-191">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="7712a-192">Замените метод `EditPost` на следующий код и добавьте новый метод, который обновляет свойство навигации `Courses` для сущности Instructor.</span><span class="sxs-lookup"><span data-stu-id="7712a-192">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="7712a-193">Сигнатура метода теперь отличается от метода HttpGet `Edit`, поэтому имя метода изменяется с `EditPost` обратно на `Edit`.</span><span class="sxs-lookup"><span data-stu-id="7712a-193">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="7712a-194">Так как представление не содержит коллекцию сущностей Course, связыватель модели не может автоматически обновить свойство навигации `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="7712a-194">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="7712a-195">Вместо использования связывателя модели для обновления свойства навигации `CourseAssignments` вы делаете это в новом методе `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="7712a-195">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="7712a-196">Поэтому нужно исключить свойство `CourseAssignments` из привязки модели.</span><span class="sxs-lookup"><span data-stu-id="7712a-196">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="7712a-197">Это не требует внесения никаких изменений в код, вызывающем `TryUpdateModel`, так как вы используете перегрузку на базе списка разрешений, а `CourseAssignments` отсутствует в списке включений.</span><span class="sxs-lookup"><span data-stu-id="7712a-197">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="7712a-198">Если никакие флажки не выбраны, код в `UpdateInstructorCourses` инициализирует свойство навигации `CourseAssignments` с использованием пустой коллекции и возвращает следующее:</span><span class="sxs-lookup"><span data-stu-id="7712a-198">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="7712a-199">После этого код в цикле проходит по всем курсам в базе данных и сравнивает каждый из них с теми, которые сейчас назначены преподавателю, в противоположность тем, которые были выбраны в представлении.</span><span class="sxs-lookup"><span data-stu-id="7712a-199">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="7712a-200">Чтобы упростить эффективную подстановку, последние две коллекции хранятся в объектах `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="7712a-200">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="7712a-201">Если флажок для курса был установлен, но курс отсутствует в свойстве навигации `Instructor.CourseAssignments`, этот курс добавляется в коллекцию в свойстве навигации.</span><span class="sxs-lookup"><span data-stu-id="7712a-201">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="7712a-202">Если флажок для курса не был установлен, но курс присутствует в свойстве навигации `Instructor.CourseAssignments`, этот курс удаляется из свойства навигации.</span><span class="sxs-lookup"><span data-stu-id="7712a-202">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="7712a-203">Обновление представлений преподавателя</span><span class="sxs-lookup"><span data-stu-id="7712a-203">Update the Instructor views</span></span>

<span data-ttu-id="7712a-204">Во *Views/Instructors/Edit.cshtml* добавьте поле **Courses** (Курсы) с массивом флажков, добавив приведенный ниже код сразу после элементов `div` для поля **Office** (Кабинет) и перед элементом `div` для кнопки **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="7712a-204">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="7712a-205">При вставке кода в Visual Studio разрывы строк изменяются, нарушая код.</span><span class="sxs-lookup"><span data-stu-id="7712a-205">When you paste the code in Visual Studio, line breaks will be changed in a way that breaks the code.</span></span>  <span data-ttu-id="7712a-206">Один раз нажмите клавиши CTRL+Z, чтобы отменить автоматическое форматирование.</span><span class="sxs-lookup"><span data-stu-id="7712a-206">Press Ctrl+Z one time to undo the automatic formatting.</span></span>  <span data-ttu-id="7712a-207">Это исправляет разрывы строк, благодаря чему код приобретает показанный здесь вид.</span><span class="sxs-lookup"><span data-stu-id="7712a-207">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="7712a-208">Выравнивать отступы необязательно, однако строки `@</tr><tr>`, `@:<td>`, `@:</td>` и `@:</tr>` должны находиться на одной строке, как показано здесь. В противном случае возникает ошибка времени выполнения.</span><span class="sxs-lookup"><span data-stu-id="7712a-208">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="7712a-209">Выделите блок нового кода и три раза нажмите клавишу TAB, чтобы выровнять его с существующим кодом.</span><span class="sxs-lookup"><span data-stu-id="7712a-209">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="7712a-210">С ходом работы над решением этой проблемы вы можете ознакомиться [здесь](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="7712a-210">You can check the status of this problem [here](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="7712a-211">Этот код создает таблицу HTML с тремя столбцами.</span><span class="sxs-lookup"><span data-stu-id="7712a-211">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="7712a-212">Каждый столбец содержит флажок, за которым идет заголовок с номером и названием курса.</span><span class="sxs-lookup"><span data-stu-id="7712a-212">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="7712a-213">Все флажки имеют одинаковое имя ("selectedCourses"), уведомляющее связывателя модели, что их следует рассматривать как группу.</span><span class="sxs-lookup"><span data-stu-id="7712a-213">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="7712a-214">Атрибуту значения для каждого флажка присваивается значение `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="7712a-214">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="7712a-215">При передаче страницы связыватель модели передает контроллеру массив, содержащий значения `CourseID` только для выбранных флажков.</span><span class="sxs-lookup"><span data-stu-id="7712a-215">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="7712a-216">При первичной отрисовке для флажков курсов, назначенных преподавателю, заданы атрибуты, в результате чего они отображаются установленными (выбранными).</span><span class="sxs-lookup"><span data-stu-id="7712a-216">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="7712a-217">Запустите приложение, выберите вкладку **Instructors** (Преподаватели), а затем щелкните **Edit** (Изменить) для преподавателя, чтобы открыть страницу **Edit** (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="7712a-217">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Страница редактирования преподавателя с курсами](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="7712a-219">Измените некоторые назначения курсов и нажмите кнопку "Save" (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="7712a-219">Change some course assignments and click Save.</span></span> <span data-ttu-id="7712a-220">Вносимые вами изменения отражаются на странице индекса.</span><span class="sxs-lookup"><span data-stu-id="7712a-220">The changes you make are reflected on the Index page.</span></span>

> [!NOTE]
> <span data-ttu-id="7712a-221">Описываемый здесь подход к редактированию данных курсов для преподавателя эффективен при ограниченном числе курсов.</span><span class="sxs-lookup"><span data-stu-id="7712a-221">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="7712a-222">Для коллекций большего размера следовало бы применять другой пользовательский интерфейс и другой метод обновления.</span><span class="sxs-lookup"><span data-stu-id="7712a-222">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="7712a-223">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="7712a-223">Update the Delete page</span></span>

<span data-ttu-id="7712a-224">В файле *InstructorsController.cs* удалите метод `DeleteConfirmed` и вставьте вместо него приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="7712a-224">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="7712a-225">Этот код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="7712a-225">This code makes the following changes:</span></span>

* <span data-ttu-id="7712a-226">Выполняет безотложную загрузку для свойства навигации `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="7712a-226">Does eager loading for the `CourseAssignments` navigation property.</span></span>  <span data-ttu-id="7712a-227">Вам нужно включить его, иначе EF не будет знать о связанных сущностях `CourseAssignment` и не удалит их.</span><span class="sxs-lookup"><span data-stu-id="7712a-227">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span>  <span data-ttu-id="7712a-228">Чтобы избежать необходимости считывать их, можно настроить каскадное удаление в базе данных.</span><span class="sxs-lookup"><span data-stu-id="7712a-228">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="7712a-229">Если преподаватель, которого требуется удалить, назначен в качестве администратора любой из кафедр, удаляется назначение преподавателя из таких кафедр.</span><span class="sxs-lookup"><span data-stu-id="7712a-229">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-the-create-page"></a><span data-ttu-id="7712a-230">Добавление расположения кабинета и курсов на страницу создания</span><span class="sxs-lookup"><span data-stu-id="7712a-230">Add office location and courses to the Create page</span></span>

<span data-ttu-id="7712a-231">В файле *InstructorsController.cs* удалите методы HttpGet и HttpPost `Create` и вставьте вместо них следующий код:</span><span class="sxs-lookup"><span data-stu-id="7712a-231">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="7712a-232">Этот код аналогичен коду для методов `Edit`, за исключением того, что изначально никакие курсы не выбраны.</span><span class="sxs-lookup"><span data-stu-id="7712a-232">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="7712a-233">Метод HttpGet `Create` вызывает метод `PopulateAssignedCourseData` не потому, что могут быть выбраны курсы, а чтобы предоставить пустую коллекцию для цикла `foreach` в представлении (в противном случае код представления выдаст исключение пустой ссылки).</span><span class="sxs-lookup"><span data-stu-id="7712a-233">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="7712a-234">Метод HttpPost `Create` добавляет каждый выбранный курс в свойство навигации `CourseAssignments` до того, как выполнить поиск ошибок проверки и добавить нового преподавателя в базу данных.</span><span class="sxs-lookup"><span data-stu-id="7712a-234">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="7712a-235">Курсы добавляются даже при наличии ошибок модели, поэтому когда имеются такие ошибки (например, пользователь ввел недопустимую дату) и страница отображается повторно с сообщением об ошибке, все выбранные курсы восстанавливаются автоматически.</span><span class="sxs-lookup"><span data-stu-id="7712a-235">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="7712a-236">Обратите внимание, что для добавления курсов в свойство навигации `CourseAssignments` нужно инициализировать это свойство как пустую коллекцию:</span><span class="sxs-lookup"><span data-stu-id="7712a-236">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="7712a-237">Это можно сделать не только в коде контроллера, но и в модели Instructor, изменив метод получения свойств для автоматического создания коллекции, если она не существует, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="7712a-237">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

<span data-ttu-id="7712a-238">При подобном изменении свойства `CourseAssignments` можно удалить код явной инициализации свойства в контроллере.</span><span class="sxs-lookup"><span data-stu-id="7712a-238">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="7712a-239">Во *Views/Instructor/Create.cshtml* добавьте текстовое поле для расположения кабинета и флажки для курсов перед кнопкой "Submit" (Отправить).</span><span class="sxs-lookup"><span data-stu-id="7712a-239">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="7712a-240">Как и в случае со страницей редактирования, [исправьте форматирование, если Visual Studio переформатирует код при вставке](#notepad).</span><span class="sxs-lookup"><span data-stu-id="7712a-240">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="7712a-241">Проверьте работу, запустив приложение и создав преподавателя.</span><span class="sxs-lookup"><span data-stu-id="7712a-241">Test by running the app and creating an instructor.</span></span>

## <a name="handling-transactions"></a><span data-ttu-id="7712a-242">Обработка транзакций</span><span class="sxs-lookup"><span data-stu-id="7712a-242">Handling Transactions</span></span>

<span data-ttu-id="7712a-243">Как описано в [руководстве по CRUD](crud.md), платформа Entity Framework реализует транзакции неявно.</span><span class="sxs-lookup"><span data-stu-id="7712a-243">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="7712a-244">Если вам требуется дополнительный контроль, например в сценариях с операциями, выполняемыми в транзакции вне платформы Entity Framework, ознакомьтесь с разделом [Транзакции](/ef/core/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="7712a-244">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="summary"></a><span data-ttu-id="7712a-245">Сводка</span><span class="sxs-lookup"><span data-stu-id="7712a-245">Summary</span></span>

<span data-ttu-id="7712a-246">Вы ознакомились с введением в работу со связанными данными.</span><span class="sxs-lookup"><span data-stu-id="7712a-246">You have now completed the introduction to working with related data.</span></span> <span data-ttu-id="7712a-247">Следующее руководство описывает обработку конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="7712a-247">In the next tutorial you'll see how to handle concurrency conflicts.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="7712a-248">[Назад](read-related-data.md)
> [Вперед](concurrency.md)</span><span class="sxs-lookup"><span data-stu-id="7712a-248">[Previous](read-related-data.md)
[Next](concurrency.md)</span></span>
