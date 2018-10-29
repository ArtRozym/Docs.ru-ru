---
title: ASP.NET Core MVC с EF Core — параллелизм — 8 из 10
author: rick-anderson
description: Это руководство описывает, как обрабатывать конфликты, когда несколько пользователей одновременно изменяют одну сущность.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 0ae566a76a2ef656843452ed537b8fdfbddaed22
ms.sourcegitcommit: 4d74644f11e0dac52b4510048490ae731c691496
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50090905"
---
# <a name="aspnet-core-mvc-with-ef-core---concurrency---8-of-10"></a><span data-ttu-id="0015c-103">ASP.NET Core MVC с EF Core — параллелизм — 8 из 10</span><span class="sxs-lookup"><span data-stu-id="0015c-103">ASP.NET Core MVC with EF Core - Concurrency - 8 of 10</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc-21.md)]

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0015c-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="0015c-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0015c-105">На примере учебного веб-приложения "Университет Contoso" демонстрируется процесс создания веб-приложений ASP.NET Core MVC с помощью Entity Framework Core и Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0015c-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="0015c-106">Сведения о серии руководств см. в [первом руководстве серии](intro.md).</span><span class="sxs-lookup"><span data-stu-id="0015c-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="0015c-107">В предыдущих учебниках вы узнали, как обновлять данные.</span><span class="sxs-lookup"><span data-stu-id="0015c-107">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="0015c-108">Это руководство описывает, как обрабатывать конфликты, когда несколько пользователей одновременно изменяют одну сущность.</span><span class="sxs-lookup"><span data-stu-id="0015c-108">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="0015c-109">Вы создадите веб-страницы, которые работают с сущностью Department и обрабатывают ошибки параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-109">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="0015c-110">На следующих рисунках показаны страницы "Edit" (Редактирование) и "Delete" (Удаление), включая некоторые сообщения, которые отображаются при возникновении конфликта параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-110">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Страница редактирования кафедры](concurrency/_static/edit-error.png)

![Страница удаления кафедры](concurrency/_static/delete-error.png)

## <a name="concurrency-conflicts"></a><span data-ttu-id="0015c-113">Конфликты параллелизма</span><span class="sxs-lookup"><span data-stu-id="0015c-113">Concurrency conflicts</span></span>

<span data-ttu-id="0015c-114">Конфликт параллелизма возникает, когда один пользователь отображает данные сущности, чтобы изменить их, а другой пользователь обновляет данные той же сущности до того, как изменение первого пользователя будет записано в базу данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-114">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="0015c-115">Если не включить обнаружение таких конфликтов, то пользователь, обновляющий базу данных последним, перезаписывает изменения другого пользователя.</span><span class="sxs-lookup"><span data-stu-id="0015c-115">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="0015c-116">Во многих приложениях такой риск допустим: при небольшом числе пользователей или обновлений, а также в случае, если перезапись некоторых изменений не является критической, стоимость реализации параллелизма может перевесить его преимущества.</span><span class="sxs-lookup"><span data-stu-id="0015c-116">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="0015c-117">В этом случае вам не нужно настраивать приложение для обработки конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-117">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="0015c-118">Пессимистичный параллелизм (блокировка)</span><span class="sxs-lookup"><span data-stu-id="0015c-118">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="0015c-119">Если приложению нужно предотвратить случайную потерю данных в сценариях параллелизма, одним из способов сделать это являются блокировки базы данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-119">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="0015c-120">Это называется пессимистичным параллелизмом.</span><span class="sxs-lookup"><span data-stu-id="0015c-120">This is called pessimistic concurrency.</span></span> <span data-ttu-id="0015c-121">Например, перед чтением строки из базы данных вы запрашиваете блокировку для доступа для обновления или только для чтения.</span><span class="sxs-lookup"><span data-stu-id="0015c-121">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="0015c-122">Если заблокировать строку для обновления, другие пользователи не могут заблокировать ее для обновления или только для чтения, так как получат копию данных, которые находятся в процессе изменения.</span><span class="sxs-lookup"><span data-stu-id="0015c-122">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="0015c-123">Если заблокировать строку только для чтения, другие пользователи также могут заблокировать ее только для чтения, но не для обновления.</span><span class="sxs-lookup"><span data-stu-id="0015c-123">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="0015c-124">Управление блокировками имеет недостатки.</span><span class="sxs-lookup"><span data-stu-id="0015c-124">Managing locks has disadvantages.</span></span> <span data-ttu-id="0015c-125">Оно может оказаться сложным с точки зрения программирования.</span><span class="sxs-lookup"><span data-stu-id="0015c-125">It can be complex to program.</span></span> <span data-ttu-id="0015c-126">Оно требует значительных ресурсов управления базами данных, а также может вызвать проблемы с производительностью по мере увеличения числа пользователей приложения.</span><span class="sxs-lookup"><span data-stu-id="0015c-126">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="0015c-127">Поэтому не все системы управления базами данных поддерживают пессимистичный параллелизм.</span><span class="sxs-lookup"><span data-stu-id="0015c-127">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="0015c-128">В Entity Framework Core нет встроенной поддержки этой функции, и данное руководство не рассказывает, как ее реализовать.</span><span class="sxs-lookup"><span data-stu-id="0015c-128">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="0015c-129">Оптимистическая блокировка</span><span class="sxs-lookup"><span data-stu-id="0015c-129">Optimistic Concurrency</span></span>

<span data-ttu-id="0015c-130">Альтернативой пессимистичному параллелизму является оптимистичный параллелизм (оптимистическая блокировка).</span><span class="sxs-lookup"><span data-stu-id="0015c-130">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="0015c-131">Оптимистическая блокировка допускает появление конфликтов параллелизма, а затем обрабатывает их соответствующим образом.</span><span class="sxs-lookup"><span data-stu-id="0015c-131">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="0015c-132">Например, Мария посещает страницу изменения кафедры и изменяет бюджет кафедры английской языка с 350 000,00 USD на 0,00 USD.</span><span class="sxs-lookup"><span data-stu-id="0015c-132">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Изменение бюджета на 0](concurrency/_static/change-budget.png)

<span data-ttu-id="0015c-134">Прежде чем Мария нажимает кнопку **Save** (Сохранить), Дмитрий заходит на ту же страницу и изменяет значение в поле "Start Date" (Дата начала) с 9/1/2007 на 9/1/2013.</span><span class="sxs-lookup"><span data-stu-id="0015c-134">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Изменение начальной даты на 2013 г.](concurrency/_static/change-date.png)

<span data-ttu-id="0015c-136">Сначала Мария нажимает кнопку **Save** (Сохранить) и видит свое изменение, когда браузер возвращается на страницу индекса.</span><span class="sxs-lookup"><span data-stu-id="0015c-136">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Бюджет изменен на нуль](concurrency/_static/budget-zero.png)

<span data-ttu-id="0015c-138">Затем Дмитрий нажимает кнопку **Save** (Сохранить) на странице редактирования, где все еще отображается бюджет 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="0015c-138">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="0015c-139">Дальнейший ход событий определяется порядком обработки конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-139">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="0015c-140">Некоторые параметры перечислены ниже:</span><span class="sxs-lookup"><span data-stu-id="0015c-140">Some of the options include the following:</span></span>

* <span data-ttu-id="0015c-141">Вы можете отслеживать, для какого свойства пользователь изменил и обновил только соответствующие столбцы в базе данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-141">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="0015c-142">В этом примере сценария данные не будут потеряны, так как эти два пользователя обновляли разные свойства.</span><span class="sxs-lookup"><span data-stu-id="0015c-142">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="0015c-143">Когда какой-либо пользователь просмотрит кафедру английского языка в следующий раз, он увидит изменения, внесенные как Марией, так и Дмитрием — дату начала 9/1/2013 и бюджет в нуль долларов США.</span><span class="sxs-lookup"><span data-stu-id="0015c-143">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="0015c-144">Этот метод обновления помогает снизить число конфликтов, которые могут привести к потере данных, но не позволяет избежать такой потери, когда конкурирующие изменения вносятся в одно свойство сущности.</span><span class="sxs-lookup"><span data-stu-id="0015c-144">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="0015c-145">То, работает ли Entity Framework в таком режиме, зависит от того, как вы реализуете код обновления.</span><span class="sxs-lookup"><span data-stu-id="0015c-145">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="0015c-146">В веб-приложении это часто нецелесообразно, так как может потребоваться обрабатывать большой объем состояний, чтобы отслеживать все исходные значения свойств для сущности, а также новые значения.</span><span class="sxs-lookup"><span data-stu-id="0015c-146">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="0015c-147">Обработка большого объема состояний может повлиять на производительность приложения, так как требует ресурсов сервера или должна быть включена непосредственно в веб-страницу (например, в скрытые поля) или файл cookie.</span><span class="sxs-lookup"><span data-stu-id="0015c-147">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="0015c-148">Вы можете позволить изменению Дмитрия перезаписать изменение Марии.</span><span class="sxs-lookup"><span data-stu-id="0015c-148">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="0015c-149">Когда какой-либо пользователь просмотрит кафедру английского языка в следующий раз, он увидит дату 9/1/2013 и восстановленное значение 350 000,00 USD.</span><span class="sxs-lookup"><span data-stu-id="0015c-149">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="0015c-150">Такой подход называется *победой клиента* или *сохранением последнего внесенного изменения*.</span><span class="sxs-lookup"><span data-stu-id="0015c-150">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="0015c-151">(Все значения из клиента имеют приоритет над данными в хранилище.) Как отмечено во введении к этому разделу, если вы не пишете код для обработки параллелизма, она выполняется автоматически.</span><span class="sxs-lookup"><span data-stu-id="0015c-151">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="0015c-152">Вы можете запретить обновление изменения Дмитрия в базе данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-152">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="0015c-153">Как правило, следует отобразить сообщение об ошибке, показать текущее состояние данных и позволить ему повторно применить свои изменения, если они ему нужны.</span><span class="sxs-lookup"><span data-stu-id="0015c-153">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="0015c-154">Это называется *победой хранилища*.</span><span class="sxs-lookup"><span data-stu-id="0015c-154">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="0015c-155">(Значения в хранилище имеют приоритет над данными, передаваемыми клиентом.) В этом руководстве вы реализуете сценарий победы хранилища.</span><span class="sxs-lookup"><span data-stu-id="0015c-155">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="0015c-156">Данный метод гарантирует, что никакие изменения не перезаписываются без оповещения пользователя о случившемся.</span><span class="sxs-lookup"><span data-stu-id="0015c-156">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="0015c-157">Обнаружение конфликтов параллелизма</span><span class="sxs-lookup"><span data-stu-id="0015c-157">Detecting concurrency conflicts</span></span>

<span data-ttu-id="0015c-158">Конфликты можно разрешать путем обработки исключений `DbConcurrencyException`, выдаваемых Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="0015c-158">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="0015c-159">Чтобы определить, когда именно нужно выдавать исключения, платформа Entity Framework должна быть в состоянии обнаруживать конфликты.</span><span class="sxs-lookup"><span data-stu-id="0015c-159">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="0015c-160">Поэтому нужно соответствующим образом настроить базу данных и модель данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-160">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="0015c-161">Ниже приведены некоторые варианты для реализации обнаружения конфликтов:</span><span class="sxs-lookup"><span data-stu-id="0015c-161">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="0015c-162">Включите в таблицу базы данных столбец отслеживания, который позволяет определять, когда была изменена строка.</span><span class="sxs-lookup"><span data-stu-id="0015c-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="0015c-163">Затем можно настроить Entity Framework для включения этого столбца в предложение Where команд SQL Update или Delete.</span><span class="sxs-lookup"><span data-stu-id="0015c-163">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="0015c-164">Типом данных для столбца отслеживания обычно является `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="0015c-164">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="0015c-165">Значение `rowversion` является последовательным номером, увеличивающимся при каждом обновлении строки.</span><span class="sxs-lookup"><span data-stu-id="0015c-165">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="0015c-166">В команде Update или Delete предложение Where содержит исходное значение столбца отслеживания (исходную версию строки).</span><span class="sxs-lookup"><span data-stu-id="0015c-166">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="0015c-167">Если обновляемая строка была изменена другим пользователем, значение в столбце `rowversion` отличается от исходного значения, поэтому оператору Update или Delete не удается найти строку для обновления из-за предложения Where.</span><span class="sxs-lookup"><span data-stu-id="0015c-167">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="0015c-168">Когда платформа Entity Framework обнаруживает, что ни одна из строк не была обновлена с помощью команды Update или Delete (то есть число затронутых строк равно нулю), она интерпретирует это как конфликт параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-168">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="0015c-169">Настройте Entity Framework для включения исходного значения каждого столбца таблицы в предложение Where команд Update и Delete.</span><span class="sxs-lookup"><span data-stu-id="0015c-169">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="0015c-170">Как и в случае с первым вариантом, если с момента первого чтения в строке что-либо изменилось, предложение Where не возвращает строку для обновления, что платформа Entity Framework интерпретирует как конфликт параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-170">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="0015c-171">Для таблиц базы данных со множеством столбцов этот подход может привести к очень большим предложениям Where и потребовать обрабатывать большой объем состояний.</span><span class="sxs-lookup"><span data-stu-id="0015c-171">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="0015c-172">Как было указано ранее, обслуживание большого объема состояний может негативно повлиять на производительность приложения.</span><span class="sxs-lookup"><span data-stu-id="0015c-172">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="0015c-173">Поэтому в общем случае данный подход не рекомендуется, кроме того, он не применяется и в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="0015c-173">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="0015c-174">Если вы хотите реализовать этот подход к параллелизму, нужно пометить все свойства, не относящиеся к первичному ключу, в сущности, где требуется отслеживать параллелизм, добавив к ним атрибут `ConcurrencyCheck`.</span><span class="sxs-lookup"><span data-stu-id="0015c-174">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="0015c-175">Это изменение позволяет Entity Framework включить все столбцы в предложение Where SQL операторов Update и Delete.</span><span class="sxs-lookup"><span data-stu-id="0015c-175">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="0015c-176">В оставшейся части этого руководства вам предстоит добавить свойство отслеживания `rowversion` в сущность Department, создать контроллер и представления, а также проверить правильность работы решения.</span><span class="sxs-lookup"><span data-stu-id="0015c-176">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="0015c-177">Добавление свойства отслеживания в сущность Department</span><span class="sxs-lookup"><span data-stu-id="0015c-177">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="0015c-178">В *Models/Department.cs* добавьте свойство отслеживания RowVersion:</span><span class="sxs-lookup"><span data-stu-id="0015c-178">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="0015c-179">Атрибут `Timestamp` указывает, что этот столбец будет включен в предложение Where команд Update и Delete, отправляемых в базу данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-179">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="0015c-180">Этот атрибут называется `Timestamp`, так как предыдущие версии SQL Server использовали тип данных `timestamp` SQL, пока его не сменил `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="0015c-180">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="0015c-181">Тип .NET для `rowversion` — это массив байтов.</span><span class="sxs-lookup"><span data-stu-id="0015c-181">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="0015c-182">Если вы предпочитаете использовать текучий API, можно воспользоваться методом `IsConcurrencyToken` (в *Data/SchoolContext.cs*), чтобы указать свойство отслеживания, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="0015c-182">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="0015c-183">Добавив свойство, вы изменили модель базы данных, поэтому нужно выполнить еще одну миграцию.</span><span class="sxs-lookup"><span data-stu-id="0015c-183">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="0015c-184">Сохраните изменения, выполните сборку проекта и введите в командном окне следующие команды:</span><span class="sxs-lookup"><span data-stu-id="0015c-184">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-a-departments-controller-and-views"></a><span data-ttu-id="0015c-185">Создание контроллера кафедр и представлений</span><span class="sxs-lookup"><span data-stu-id="0015c-185">Create a Departments controller and views</span></span>

<span data-ttu-id="0015c-186">Сформируйте шаблоны для контроллера кафедр и представлений, как делали это раньше для учащихся, курсов и преподавателей.</span><span class="sxs-lookup"><span data-stu-id="0015c-186">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Формирование шаблона кафедр](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="0015c-188">В файле *DepartmentsController.cs* измените все четыре экземпляра "FirstMidName" на "FullName", чтобы раскрывающиеся списки для администратора кафедры содержали полное имя преподавателя, а не только фамилию.</span><span class="sxs-lookup"><span data-stu-id="0015c-188">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-the-departments-index-view"></a><span data-ttu-id="0015c-189">Изменение представления индекса кафедр</span><span class="sxs-lookup"><span data-stu-id="0015c-189">Update the Departments Index view</span></span>

<span data-ttu-id="0015c-190">Подсистема формирования шаблонов создала столбец RowVersion для представления индекса, однако это поле не должно отображаться.</span><span class="sxs-lookup"><span data-stu-id="0015c-190">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="0015c-191">Замените код в файле *Views/Departments/Index.cshtml* на приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="0015c-191">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="0015c-192">Он изменяет заголовок на "Departments" (Кафедры), удаляет столбец RowVersion и отображает администратору имя и фамилию, а не только имя.</span><span class="sxs-lookup"><span data-stu-id="0015c-192">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-the-edit-methods-in-the-departments-controller"></a><span data-ttu-id="0015c-193">Обновление методов Edit в контроллере кафедр</span><span class="sxs-lookup"><span data-stu-id="0015c-193">Update the Edit methods in the Departments controller</span></span>

<span data-ttu-id="0015c-194">Добавьте `AsNoTracking` в оба метода — HttpGet `Edit` и `Details`.</span><span class="sxs-lookup"><span data-stu-id="0015c-194">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="0015c-195">В методе HttpGet `Edit` добавьте безотложную загрузку для администратора.</span><span class="sxs-lookup"><span data-stu-id="0015c-195">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading&highlight=2,3)]

<span data-ttu-id="0015c-196">Замените существующий код в методе HttpPost`Edit` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="0015c-196">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="0015c-197">Код начинает пытаться считать кафедру для обновления.</span><span class="sxs-lookup"><span data-stu-id="0015c-197">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="0015c-198">Если метод `SingleOrDefaultAsync` возвращает значение null, кафедра была удалена другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="0015c-198">If the `SingleOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="0015c-199">В этом случае код использует переданные значения из формы для создания сущности кафедры, чтобы страницу "Edit" (Редактирование) можно было отобразить повторно с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="0015c-199">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="0015c-200">Кроме того, повторно создать сущность кафедры не нужно, если вы выводите только сообщение об ошибке без повторного отображения полей кафедры.</span><span class="sxs-lookup"><span data-stu-id="0015c-200">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="0015c-201">Представление сохраняет исходное значение `RowVersion` в скрытом поле, а данный метод получает это значение в параметре `rowVersion`.</span><span class="sxs-lookup"><span data-stu-id="0015c-201">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="0015c-202">Перед вызовом `SaveChanges` нужно поместить это исходное значение свойства `RowVersion` в коллекцию `OriginalValues` для сущности.</span><span class="sxs-lookup"><span data-stu-id="0015c-202">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="0015c-203">Когда позднее Entity Framework создает команду SQL UPDATE, она будет содержать предложение WHERE, которое ищет строку с исходным значением `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="0015c-203">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="0015c-204">Если команда UPDATE не затрагивает никакие строки (нет строк, имеющих исходное значение `RowVersion`), Entity Framework выдает исключение `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="0015c-204">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="0015c-205">Код в блоке catch для этого исключения возвращает затронутую сущность Department, имеющую обновленные значения из свойства `Entries` в объекте исключения.</span><span class="sxs-lookup"><span data-stu-id="0015c-205">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="0015c-206">Коллекция `Entries` будет иметь всего один объект `EntityEntry`.</span><span class="sxs-lookup"><span data-stu-id="0015c-206">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="0015c-207">Вы можете использовать его для получения новых значений, введенных пользователем, и текущих значений в базе данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-207">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="0015c-208">Код добавляет настраиваемое сообщение об ошибке для каждого столбца, для которого значения базы данных отличаются от введенных пользователем на странице "Edit" (Редактирование) (здесь для краткости показано всего одно поле).</span><span class="sxs-lookup"><span data-stu-id="0015c-208">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="0015c-209">Наконец, код задает для `RowVersion` объекта `departmentToUpdate` новое значение, полученное из базы данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-209">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="0015c-210">Это новое значение `RowVersion` будет сохранено в скрытом поле при повторном отображении страницы "Edit" (Редактирование). Когда пользователь в следующий раз нажимает кнопку **Save** (Сохранить), перехватываются только те ошибки параллелизма, которые возникли с момента повторного отображения страницы "Edit" (Редактирование).</span><span class="sxs-lookup"><span data-stu-id="0015c-210">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="0015c-211">Оператор `ModelState.Remove` является обязательным, так как `ModelState` имеет старое значение `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="0015c-211">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="0015c-212">На представлении значение `ModelState` для поля имеет приоритет над значениями свойств модели, если они присутствуют вместе.</span><span class="sxs-lookup"><span data-stu-id="0015c-212">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-department-edit-view"></a><span data-ttu-id="0015c-213">Обновление представления редактирования кафедры</span><span class="sxs-lookup"><span data-stu-id="0015c-213">Update the Department Edit view</span></span>

<span data-ttu-id="0015c-214">Внесите следующие изменения в файл *Views/Departments/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="0015c-214">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="0015c-215">Добавьте скрытое поле для сохранения значения свойства `RowVersion` сразу после скрытого поля для свойства `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="0015c-215">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="0015c-216">Добавьте пункт "Select Administrator" (Выбрать администратора) в раскрывающийся список.</span><span class="sxs-lookup"><span data-stu-id="0015c-216">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts-in-the-edit-page"></a><span data-ttu-id="0015c-217">Тестирование конфликтов параллелизма на странице "Edit" (Редактирование)</span><span class="sxs-lookup"><span data-stu-id="0015c-217">Test concurrency conflicts in the Edit page</span></span>

<span data-ttu-id="0015c-218">Запустите приложение и перейдите на страницу индекса кафедр.</span><span class="sxs-lookup"><span data-stu-id="0015c-218">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="0015c-219">Щелкните правой кнопкой мыши гиперссылку **Edit** (Изменить) для кафедры английского языка и выберите пункт **Открыть на новой вкладке**, а затем щелкните гиперссылку **Edit** (Изменить) для этой кафедры.</span><span class="sxs-lookup"><span data-stu-id="0015c-219">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="0015c-220">Теперь на обеих вкладках браузера отображаются одинаковые сведения.</span><span class="sxs-lookup"><span data-stu-id="0015c-220">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="0015c-221">Измените поле на первой вкладке браузера и нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="0015c-221">Change a field in the first browser tab and click **Save**.</span></span>

![Страница "Edit" (Редактирование) кафедры 1 после изменения](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="0015c-223">В браузере отображается страница индекса с измененным значением.</span><span class="sxs-lookup"><span data-stu-id="0015c-223">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="0015c-224">Измените поле на второй вкладке браузера.</span><span class="sxs-lookup"><span data-stu-id="0015c-224">Change a field in the second browser tab.</span></span>

![Страница "Edit" (Редактирование) кафедры 2 после изменения](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="0015c-226">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="0015c-226">Click **Save**.</span></span> <span data-ttu-id="0015c-227">Отображается сообщение об ошибке:</span><span class="sxs-lookup"><span data-stu-id="0015c-227">You see an error message:</span></span>

![Сообщение об ошибке для страницы "Edit" (Редактирование) кафедры](concurrency/_static/edit-error.png)

<span data-ttu-id="0015c-229">Снова нажмите кнопку **Save** (Сохранить).</span><span class="sxs-lookup"><span data-stu-id="0015c-229">Click **Save** again.</span></span> <span data-ttu-id="0015c-230">Сохраняется значение, введенное на второй вкладке браузера.</span><span class="sxs-lookup"><span data-stu-id="0015c-230">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="0015c-231">Сохраненные значения отображаются при открытии страницы индекса.</span><span class="sxs-lookup"><span data-stu-id="0015c-231">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="0015c-232">Обновление страницы удаления</span><span class="sxs-lookup"><span data-stu-id="0015c-232">Update the Delete page</span></span>

<span data-ttu-id="0015c-233">Для страницы "Delete" (Удаление) платформа Entity Framework обнаруживает конфликты параллелизма, вызванные схожим изменением кафедры.</span><span class="sxs-lookup"><span data-stu-id="0015c-233">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="0015c-234">Когда метод HttpGet `Delete` отображает представление подтверждения, оно содержит исходное значение `RowVersion` в скрытом поле.</span><span class="sxs-lookup"><span data-stu-id="0015c-234">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="0015c-235">Затем это значение становится доступным для метода HttpPost `Delete`, вызываемого при подтверждении удаления пользователем.</span><span class="sxs-lookup"><span data-stu-id="0015c-235">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="0015c-236">Когда Entity Framework создает команду SQL DELETE, она включает предложение WHERE с исходным значением `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="0015c-236">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="0015c-237">Если команда не затрагивает ни одной строки (подразумевается изменение строки после отображения страницы подтверждения удаления), возникает исключение параллелизма и вызывается метод HttpGet `Delete`, у которого для флага ошибки установлено значение true, чтобы повторно отобразить страницу подтверждения с сообщением об ошибке.</span><span class="sxs-lookup"><span data-stu-id="0015c-237">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="0015c-238">Отсутствие затронутых строк также может быть вызвано тем, что строка была удалена другим пользователем, и в этом случае сообщение об ошибке не отображается.</span><span class="sxs-lookup"><span data-stu-id="0015c-238">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="0015c-239">Обновление методов Delete в контроллере кафедр</span><span class="sxs-lookup"><span data-stu-id="0015c-239">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="0015c-240">В файле *DepartmentsController.cs* замените код метода HttpGet `Delete` приведенным ниже кодом:</span><span class="sxs-lookup"><span data-stu-id="0015c-240">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="0015c-241">Этот метод принимает необязательный параметр, который указывает, отображается ли страница повторно после ошибки параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-241">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="0015c-242">Если этот флаг имеет значение true, а указанная кафедра больше не существует, она была удалена другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="0015c-242">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="0015c-243">В этом случае код выполняет перенаправление на страницу индекса.</span><span class="sxs-lookup"><span data-stu-id="0015c-243">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="0015c-244">Если этот флаг имеет значение true, а кафедра существует, она была изменена другим пользователем.</span><span class="sxs-lookup"><span data-stu-id="0015c-244">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="0015c-245">В этом случае код отправляет сообщение об ошибке в представление, используя `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="0015c-245">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="0015c-246">Замените код в методе HttpPost`Delete` (с именем `DeleteConfirmed`) следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="0015c-246">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="0015c-247">В шаблонном коде, который вы только что заменили, этот метод принимал только идентификатор записи:</span><span class="sxs-lookup"><span data-stu-id="0015c-247">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="0015c-248">Вы изменили этот параметр на экземпляр Department, созданный связывателем модели.</span><span class="sxs-lookup"><span data-stu-id="0015c-248">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="0015c-249">Это предоставляет EF доступ к значению свойства RowVersion в дополнение к ключу записи.</span><span class="sxs-lookup"><span data-stu-id="0015c-249">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="0015c-250">Вы также изменили имя метода действия с `DeleteConfirmed` на `Delete`.</span><span class="sxs-lookup"><span data-stu-id="0015c-250">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="0015c-251">Шаблонный код использовал имя `DeleteConfirmed`, чтобы предоставить методу HttpPost уникальную сигнатуру.</span><span class="sxs-lookup"><span data-stu-id="0015c-251">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="0015c-252">(Среда CLR требует, чтобы перегруженные методы имели разные параметры метода.) Теперь, когда сигнатуры являются уникальными, можно придерживаться соглашения MVC и использовать одинаковое имя для методов delete HttpGet и HttpPost.</span><span class="sxs-lookup"><span data-stu-id="0015c-252">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="0015c-253">Если кафедра уже удалена, метод `AnyAsync` возвращает значение false, а приложение просто возвращается к методу Index.</span><span class="sxs-lookup"><span data-stu-id="0015c-253">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="0015c-254">При перехвате ошибки параллелизма код повторно отображает страницу подтверждения удаления и предоставляет флаг, указывающий, что нужно отобразить сообщение об ошибке параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-254">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="0015c-255">Обновление представления удаления</span><span class="sxs-lookup"><span data-stu-id="0015c-255">Update the Delete view</span></span>

<span data-ttu-id="0015c-256">В файле *Views/Departments/Delete.cshtml* замените шаблонный код приведенным ниже кодом, который добавляет поле сообщения об ошибке и скрытые поля для свойств DepartmentID и RowVersion.</span><span class="sxs-lookup"><span data-stu-id="0015c-256">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="0015c-257">Изменения выделены.</span><span class="sxs-lookup"><span data-stu-id="0015c-257">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="0015c-258">Этот код вносит следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="0015c-258">This makes the following changes:</span></span>

* <span data-ttu-id="0015c-259">Добавляет сообщение об ошибке между заголовками `h2` и `h3`.</span><span class="sxs-lookup"><span data-stu-id="0015c-259">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="0015c-260">Заменяет FirstMidName на FullName в поле **Administrator** (Администратор).</span><span class="sxs-lookup"><span data-stu-id="0015c-260">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="0015c-261">Удаляет поле RowVersion.</span><span class="sxs-lookup"><span data-stu-id="0015c-261">Removes the RowVersion field.</span></span>

* <span data-ttu-id="0015c-262">Добавляет скрытое поле для свойства `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="0015c-262">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="0015c-263">Запустите приложение и перейдите на страницу индекса кафедр.</span><span class="sxs-lookup"><span data-stu-id="0015c-263">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="0015c-264">Щелкните правой кнопкой мыши гиперссылку **Delete** (Удалить) для кафедры английского языка и выберите пункт **Открыть на новой вкладке**, а затем на первой вкладке щелкните гиперссылку **Edit** (Изменить) для этой кафедры.</span><span class="sxs-lookup"><span data-stu-id="0015c-264">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="0015c-265">В первом окне измените одно из значений и нажмите кнопку **Save** (Сохранить):</span><span class="sxs-lookup"><span data-stu-id="0015c-265">In the first window, change one of the values, and click **Save**:</span></span>

![Страница "Edit" (Редактирование) кафедры после изменения и до удаления](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="0015c-267">На второй вкладке нажмите кнопку **Delete** (Удалить).</span><span class="sxs-lookup"><span data-stu-id="0015c-267">In the second tab, click **Delete**.</span></span> <span data-ttu-id="0015c-268">Вы видите сообщение об ошибке параллелизма, а значения кафедры обновляются с использованием актуальных сведений из базы данных.</span><span class="sxs-lookup"><span data-stu-id="0015c-268">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Страница подтверждения удаления кафедры с ошибкой параллелизма](concurrency/_static/delete-error.png)

<span data-ttu-id="0015c-270">Если нажать кнопку **Delete** (Удалить) еще раз, вы будете перенаправлены на страницу индекса, которая показывает, что кафедра была удалена.</span><span class="sxs-lookup"><span data-stu-id="0015c-270">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="0015c-271">Обновление представлений Details и Create</span><span class="sxs-lookup"><span data-stu-id="0015c-271">Update Details and Create views</span></span>

<span data-ttu-id="0015c-272">При необходимости вы можете очистить шаблонный код в представлениях Details и Create.</span><span class="sxs-lookup"><span data-stu-id="0015c-272">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="0015c-273">Замените код в *Views/Departments/Details.cshtml*, чтобы удалить столбец RowVersion и отобразить полное имя администратора.</span><span class="sxs-lookup"><span data-stu-id="0015c-273">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="0015c-274">Замените код в *Views/Departments/Create.cshtml*, чтобы добавить параметр "Select" (Выбрать) в раскрывающийся список.</span><span class="sxs-lookup"><span data-stu-id="0015c-274">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="summary"></a><span data-ttu-id="0015c-275">Сводка</span><span class="sxs-lookup"><span data-stu-id="0015c-275">Summary</span></span>

<span data-ttu-id="0015c-276">На этом заканчивается введение в обработку конфликтов параллелизма.</span><span class="sxs-lookup"><span data-stu-id="0015c-276">This completes the introduction to handling concurrency conflicts.</span></span> <span data-ttu-id="0015c-277">Дополнительные сведения об обработке параллелизма в EF Core см. в разделе [Конфликты параллелизма](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="0015c-277">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span> <span data-ttu-id="0015c-278">Следующее руководство описывает, как реализовать наследование "одна таблица на иерархию" для сущностей Instructor и Student.</span><span class="sxs-lookup"><span data-stu-id="0015c-278">The next tutorial shows how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="0015c-279">[Назад](update-related-data.md)
> [Вперед](inheritance.md)</span><span class="sxs-lookup"><span data-stu-id="0015c-279">[Previous](update-related-data.md)
[Next](inheritance.md)</span></span>
