---
uid: mvc/overview/older-versions-1/contact-manager/iteration-1-create-the-application-cs
title: 'Итерация #1 – Создание приложения (C#) | Документация Майкрософт'
author: microsoft
description: 'В первой итерации мы создадим Contact Manager простейшим способом невозможно. Мы добавили поддержку для основных операций базы данных: создание, чтение, обновление и D....'
ms.author: riande
ms.date: 02/20/2009
ms.assetid: db0f160b-901c-46d3-865e-7ab6cd4ed68d
msc.legacyurl: /mvc/overview/older-versions-1/contact-manager/iteration-1-create-the-application-cs
msc.type: authoredcontent
ms.openlocfilehash: 78b488263fbb0c646d9bf6ee8c4ace2ff63ccf9d
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41824038"
---
<a name="iteration-1--create-the-application-c"></a>Итерация #1 – Создание приложения (C#)
====================
по [Microsoft](https://github.com/microsoft)

[Скачать код](iteration-1-create-the-application-cs/_static/contactmanager_1_cs1.zip)

> В первой итерации мы создадим Contact Manager простейшим способом невозможно. Мы добавили поддержку для основных операций базы данных: создание, чтение, обновление и удаление (CRUD).


## <a name="building-a-contact-management-aspnet-mvc-application-vb"></a>Создание приложения управления контактами ASP.NET MVC (VB)

В этой серии руководств мы создаем всего приложения управления контактами от начала до конца. Приложение диспетчера контактов позволяет хранить контактные данные - имена, номера телефонов и адреса электронной почты — список людей.

Мы создаем приложение через несколько итераций. С каждой итерацией мы постепенно улучшить приложение. Этот подход с несколькими итерации предназначена для того, чтобы можно было понять причину для каждого изменения.

- Итерация #1 - Создание приложения. В первой итерации мы создадим Contact Manager простейшим способом невозможно. Мы добавили поддержку для основных операций базы данных: создание, чтение, обновление и удаление (CRUD).

- #2 - итерация приложения красиво выглядеть. В этой итерации мы улучшить внешний вид приложения, изменив значение по умолчанию master страница представления ASP.NET MVC и каскадные таблицы стилей.

- Итерации #3 - Добавление проверки форм. В третьей итерации мы добавим проверки базовой форме. Мы запретить Отправка формы без завершения обязательные поля. Кроме того, мы проверяем, адреса электронной почты и номера телефонов.

- Итерация #4 - Создание слабых связей в приложении. В этой четвертой итерации мы воспользоваться преимуществами нескольких шаблонов дизайна программного обеспечения, чтобы упростить обслуживании и изменении приложения диспетчера контактов. Например мы выполнили рефакторинг наше приложение, чтобы использовать шаблон репозитория и шаблон внедрения зависимостей.

- Итерации #5 - Создание модульных тестов. В пятой итерации мы вам наше приложение проще обслуживании и изменении путем добавления модульных тестов. Мы макетирование наших классов модели данных и создания модульных тестов для контроллеров и логику проверки.

- Итерация #6 - использование управляемой тестами разработки. В этой шестой итерации мы добавляем новые функциональные возможности в наше приложение, сначала написание модульных тестов и написании кода в отношении модульные тесты. В этой итерации мы добавляем групп контактов.

- Итерации #7 - Добавление функций Ajax. В седьмой итерации мы повысить скорость реагирования и производительность приложения, добавляя поддержку Ajax.

## <a name="this-iteration"></a>Эта итерация

В этой первой итерации мы создаем базовое приложение. Целью является создание Contact Manager быстрым и простым способом. В последующих итерациях нам совершенствовать проект приложения.

Приложение диспетчера контактов является простое приложение на основе базы данных. Приложение можно использовать для создания новых контактов, редактировать существующие контакты и удалять контакты.

В этой итерации мы выполните следующие действия:

1. Приложения ASP.NET MVC
2. Создание базы данных для хранения наших контактов
3. Создать класс модели для нашей базы данных с помощью Microsoft Entity Framework
4. Создание действия контроллера и представление, которое позволяет нам для получения списка всех контактов в базе данных
5. Создание действий контроллера и представление, которое позволяет нам для создания нового контакта в базе данных
6. Создание действий контроллера и представление, которое дает возможность изменения существующей базы данных
7. Создание действий контроллера и представление, которое позволяет нам для удаления существующего контакта в базе данных

## <a name="software-prerequisites"></a>Требования к программному обеспечению

В приложениях ASP.NET MVC необходимо иметь Visual Studio 2008 или Visual Web Developer 2008, установленной на компьютере (Visual Web Developer — это бесплатная версия Visual Studio, которая содержит не все расширенные возможности Visual Studio). Можно загрузить пробную версию Visual Studio 2008 или Visual Web Developer следующий адрес:

[https://www.asp.net/downloads/essential/](https://www.asp.net/downloads/essential)

> [!NOTE] 
> 
> Для приложений ASP.NET MVC с помощью Visual Web Developer необходимо иметь Visual Web Developer установленного пакета обновления 1. Без пакета обновления 1 не удается создать проекты веб-приложений.


Платформа ASP.NET MVC. Платформа ASP.NET MVC можно загрузить по следующему адресу:

[https://www.asp.net/mvc](../../../index.md)

В этом руководстве мы используем Microsoft Entity Framework для доступа к базе данных. Платформа Entity Framework входит в состав .NET Framework 3.5 с пакетом обновления 1. Этот пакет обновления можно загрузить из следующего расположения:

[https://www.microsoft.com/downloads/details.aspx?familyid=ab99342f-5d1a-413d-8319-81da479ab0d7&amp; displaylang = en](https://www.microsoft.com/downloads/details.aspx?familyid=ab99342f-5d1a-413d-8319-81da479ab0d7&amp;displaylang=en)

В качестве альтернативы для выполнения каждого из этих загрузок по одному можно воспользоваться преимуществами установщика веб-платформы (Web PI). Web PI можно загрузить по следующему адресу:

[https://www.asp.net/downloads/essential/](https://www.asp.net/downloads/essential)

## <a name="aspnet-mvc-project"></a>Проект ASP.NET MVC

Проект веб-приложения ASP.NET MVC. Запустите Visual Studio и выберите пункт меню **файл, создать проект**. **Новый проект** откроется диалоговое окно (см. рис. 1). Выберите **Web** тип проекта и **веб-приложение ASP.NET MVC** шаблона. Имя нового проекта *ContactManager* и нажмите кнопку "ОК".


Убедитесь, что у вас есть .NET Framework 3.5, выбранные из раскрывающегося списка в верхней правой части **новый проект** диалоговое окно. В противном случае шаблон веб-приложение ASP.NET MVC, выиграл t отображаться.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image1.jpg)](iteration-1-create-the-application-cs/_static/image1.png)

**Рис 01**: диалоговое окно Новый проект ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image2.png))


Приложения ASP.NET MVC, **Создание проекта модульных тестов** откроется диалоговое окно. Это диалоговое окно можно использовать для указания, что вы хотите создать и добавить в решение проект модульного теста при создании приложения ASP.NET MVC. Несмотря на то, что мы выиграли t создании модульных тестов в этой итерации, следует выбрать параметр **Да, создать проект модульного теста** так, как мы планируем добавить модульные тесты в более поздней итерации. Добавление тестового проекта, при создании нового проекта ASP.NET MVC является гораздо проще, чем добавления проекта теста, после создания проекта ASP.NET MVC.

> [!NOTE] 
> 
> Поскольку Visual Web Developer не поддерживает тестовых проектов, вы не получите диалоговое окно создания проекта модульного теста при использовании Visual Web Developer.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image2.jpg)](iteration-1-create-the-application-cs/_static/image3.png)

**Рис. 02**: диалоговое окно создания проекта модульного теста ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image4.png))


ASP.NET MVC приложение появится в окне обозревателя решений Visual Studio (см. рис. 3). Если Дон t отображается окно обозревателя решений, то это окно можно открыть, выбрав пункт меню **вид, обозреватель решений**. Обратите внимание на то, что решение содержит два проекта: проект ASP.NET MVC и тестовый проект. Проект ASP.NET MVC имеет имя ContactManager и тестовый проект называется ContactManager.Tests.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image3.jpg)](iteration-1-create-the-application-cs/_static/image5.png)

**Рис 03**: окно обозревателя решений ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image6.png))


## <a name="deleting-the-project-sample-files"></a>Удалить файлы примера проекта

Шаблон проекта ASP.NET MVC включает в себя примеры файлов для контроллеров и представлений. Прежде чем создавать новое приложение ASP.NET MVC, следует удалить эти файлы. Можно удалить, щелкнув правой кнопкой мыши файл или папку и выбрав пункт меню файлов и папок в окне обозревателя решений **удалить**.

Вам потребуется удалить следующие файлы из проекта ASP.NET MVC.

- \Controllers\HomeController.cs

- \Views\Home\About.aspx

- \Views\Home\Index.aspx

И вам нужно удалить следующий файл из тестового проекта:

\Controllers\HomeControllerTest.cs

## <a name="creating-the-database"></a>Создание базы данных

Приложение диспетчера контактов является основанных на базах данных веб-приложения. Мы используем базу данных для хранения сведений о контактах.

Платформа ASP.NET MVC с любой современной базы данных, включая базы данных Microsoft SQL Server, Oracle, MySQL и IBM DB2. В этом руководстве мы используем базу данных Microsoft SQL Server. При установке Visual Studio, вам предоставляется возможность установки Microsoft SQL Server Express — бесплатной версии базы данных Microsoft SQL Server.

Создать новую базу данных, щелкните правой кнопкой мыши приложение\_папка данных в окне обозревателя решений и выбрав пункт меню **добавить, новый элемент**. В **Добавление нового элемента** диалоговом окне выберите **данных** категории и **базы данных SQL Server** шаблона (см. рис. 4). Имя новой базы данных ContactManagerDB.mdf и нажмите кнопку "ОК".


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image4.jpg)](iteration-1-create-the-application-cs/_static/image7.png)

**Рис. 04**: Создание новой базы данных Microsoft SQL Server Express ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image8.png))


После создания новой базы данных, база данных отображается в приложении\_папка данных в окне обозревателя решений. Дважды щелкните файл ContactManager.mdf, чтобы открыть окно обозревателя сервера и подключения к базе данных.

> [!NOTE] 
> 
> Окно обозревателя сервера называется окна обозревателя базы данных в случае с Microsoft Visual Web Developer.


Окно обозревателя сервера можно использовать для создания новых объектов базы данных, таких как таблицы базы данных, представления, триггеры и хранимые процедуры. Щелкните правой кнопкой мыши папку «таблицы» и выберите пункт меню **добавить новую таблицу**. Конструктор таблиц базы данных отображается (см. рис. 5).


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image5.jpg)](iteration-1-create-the-application-cs/_static/image9.png)

**05 рис**: конструктора таблиц базы данных ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image10.png))


Нам нужно создать таблицу, которая содержит следующие столбцы:

<a id="0.1_table01"></a>


| **Имя столбца** | **Тип данных** | **Разрешить значения NULL** |
| --- | --- | --- |
| Идентификатор | int | False |
| FirstName | nvarchar(50) | False |
| LastName | nvarchar(50) | False |
| Номер телефона | nvarchar(50) | False |
| Адрес эл. почты | nvarchar(255) | False |


Первый столбец, столбец идентификатора, специальные. Необходимо пометить как столбец идентификаторов и первичный ключевой столбец со столбцом идентификаторов. Можно указать, что столбец является столбцом идентификаторов, развернув столбец свойств (найдите в нижней части рис. 6) и прокрутку вниз до свойства Спецификация идентификации. Задайте **(Is Identity)** в соответствии с значением **Да**.

Столбец помечается как столбец первичного ключа, выбрав столбец и нажав кнопку со значком ключа. После некоторый столбец помечен как столбец первичного ключа, появится значок ключа рядом со столбцом (см. рис. 6).

После создания таблицы, нажмите кнопку Сохранить (кнопка со значком гибкого), чтобы сохранить новую таблицу. Присвойте имя новой таблицы *контакты*.

После завершения создания таблицы базы данных контактов следует добавить некоторые записи в таблицу. Щелкните правой кнопкой мыши таблицу контактов в окно обозревателя сервера и выберите пункт меню **Показать таблицу данных**. Введите один или несколько контактов в появившейся сетке.

## <a name="creating-the-data-model"></a>Создание модели данных

Приложение ASP.NET MVC состоит из модели, представления и контроллеры. Мы начнем с создания модели класс, представляющий таблицу контактов, который мы создали в предыдущем разделе.

В этом руководстве мы используем Microsoft Entity Framework, чтобы автоматически создать класс модели из базы данных.

> [!NOTE] 
> 
> Платформа ASP.NET MVC не привязан к Microsoft Entity Framework любым способом. Можно использовать ASP.NET MVC с помощью технологий доступа к альтернативной базы данных, включая NHibernate, LINQ to SQL или ADO.NET.


Выполните следующие действия для создания классов модели данных.

1. Щелкните правой кнопкой мыши папку Models в окне обозревателя решений и выберите **добавить, новый элемент**. **Добавление нового элемента** откроется диалоговое окно (см. рис. 6).
2. Выберите **данных** категории и **ADO.NET Entity Data Model** шаблона. Имя модели данных *ContactManagerModel.edmx* и нажмите кнопку **добавить** кнопки. Появится мастер модели EDM, в (см. рис. 7).
3. В **Выбор содержимого модели** выберите **создать из базы данных** (см. рис. 7).
4. В **Выбор подключения к данным** шаг, выберите базу данных ContactManagerDB.mdf и введите имя *ContactManagerDBEntities* параметров подключения сущности (см. рис. 8).
5. В **Choose Your Database Objects** шаг, установите флажок таблиц (см. рис. 9). Модель данных будет включать всех таблиц, содержащихся в базе данных (он только один, таблицу контактов). Введите пространство имен *моделей*. Нажмите кнопку "Готово", чтобы завершить работу мастера.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image6.jpg)](iteration-1-create-the-application-cs/_static/image11.png)

**Рис 06**: диалоговое окно Add New Item ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image12.png))


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image7.jpg)](iteration-1-create-the-application-cs/_static/image13.png)

**Рис 07**: Выбор содержимого модели ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image14.png))


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image8.jpg)](iteration-1-create-the-application-cs/_static/image15.png)

**Рис 08**: Выбор подключения к данным ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image16.png))


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image9.jpg)](iteration-1-create-the-application-cs/_static/image17.png)

**Рис 09**: Выбор объектов базы данных ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image18.png))


После завершения мастера модели EDM, отображается в конструкторе моделей EDM. Конструктор отображает класс, соответствующий в каждую таблицу моделируемой. Вы увидите один класс с именем контактов.

Мастер модели EDM создает имена классов на основе имен таблиц базы данных. Почти всегда необходимо изменить имя класса, созданного с помощью мастера. Щелкните правой кнопкой мыши класс контакты в конструкторе и выберите пункт меню **Переименовать**. Измените имя класса из контактов (во множественном числе) для контакта (единственное). После изменения имени класса, класс должен выглядеть следующим образом рис. 10.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image10.jpg)](iteration-1-create-the-application-cs/_static/image19.png)

**Рис. 10**: класс Contact ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image20.png))


На этом этапе мы создали модель нашей базы данных. Мы можно использовать класс Contact, представляющий запись определенного контакта в нашей базе данных.

## <a name="creating-the-home-controller"></a>Создание контроллера Home

Следующим шагом является создание нашего контроллера Home. Контроллер Home является по умолчанию вызывается в приложении ASP.NET MVC.

Создать класс контроллера Home, щелкнув правой кнопкой мыши папку Controllers в окне обозревателя решений и выбрав пункт меню **Add, контроллера** (см. рис. 11). Обратите внимание, что флажок **добавить методы действий для сценариев создания, обновления и сведения о**. Убедитесь, что этот флажок установлен, прежде чем нажимать кнопку **добавить** кнопки.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image11.jpg)](iteration-1-create-the-application-cs/_static/image21.png)

**Рис. 11**: Добавление контроллера Home ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image22.png))


При создании контроллера Home, вы получите класс в листинге 1.

**В листинге 1 - Controllers\HomeController.cs**

[!code-csharp[Main](iteration-1-create-the-application-cs/samples/sample1.cs)]

## <a name="listing-the-contacts"></a>Список контактов

Чтобы отобразить записи в таблице базы данных контактов, нам нужно создать действие Index() и представление Index.

Контроллер Home уже содержит действие Index(). Нам нужно изменить этот метод, чтобы он выглядел листинг 2.

**В листинге 2 - Controllers\HomeController.cs**

[!code-csharp[Main](iteration-1-create-the-application-cs/samples/sample2.cs)]

Обратите внимание, что классу контроллера Home в листинге 2 содержит частное поле, именуемое \_сущностей. \_Поле сущности представляет сущностей из модели данных. Мы используем \_поле сущности для обмена данными с базой данных.

Метод Index() возвращает представление, которое представляет все контакты из таблицы базы данных контактов. Выражение \_сущностей. ContactSet.ToList() возвращает список контактов в виде универсального списка.

Теперь, мы ve создании контроллера индекса, Далее необходимо создать представление Index. Перед созданием представления индекса, скомпилировать приложение, выбрав параметр меню **создать, собрать решение**. Всегда следует скомпилировать проект перед добавлением в порядке для списка классов модели для отображения в представлении **Добавление представления** диалоговое окно.

Создание индекса представления, щелкнув правой кнопкой мыши метод Index() и выбрав пункт меню **Добавление представления** (см. рис. 12). Если выбрать этот пункт меню открывает **Добавление представления** диалоговое окно (см. рис. 13).


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image12.jpg)](iteration-1-create-the-application-cs/_static/image23.png)

**Рис. 12**: Добавление представления индекса ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image24.png))


В **Добавление представления** диалоговом окне установите флажок "с меткой" **создать строго типизированное представление**. Выберите класс представления данных ContactManager.Models.Contact и Просмотр списка содержимого. Выбор этих параметров создает представление, отображающее список записей контактов.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image13.jpg)](iteration-1-create-the-application-cs/_static/image25.png)

**Рис. 13**: диалоговое окно Add View ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image26.png))


При нажатии кнопки **добавить** создается кнопка, в представление Index в листинге 3. Обратите внимание, что &lt;% @ Page %&gt; директива, который отображается в верхней части файла. Представление Index наследует от ViewPage&lt;IEnumerable&lt;ContactManager.Models.Contact&gt; &gt; класса. Другими словами класс модели в представлении представляет список сущностей Contact.

Текст в представление Index содержит цикл по каждому элементу, который выполняет итерацию всех контактов, представленный классом модели. Значение каждого свойства класса контакта отображается в HTML-таблицы.

**Листинг 3 - Views\Home\Index.aspx (без изменений)**

[!code-aspx[Main](iteration-1-create-the-application-cs/samples/sample3.aspx)]

Нам нужно внести одно изменение в представление Index. Так как мы не создается представление сведений, мы можем удалить ссылку подробных сведений. Найдите и удалите следующий код из окна просмотра индекса:

{id = элемент. % Идентификатор})&gt;

После внесения изменений в представлении индекса, можно запустить приложение диспетчера контактов. Выберите пункт меню Отладка, начать отладку, или просто нажмите клавишу F5. Первый раз, при запуске приложения, появится диалоговое окно на рис. 14. Выберите параметр **изменить файл Web.config для включения отладки** и нажмите кнопку "ОК".


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image14.jpg)](iteration-1-create-the-application-cs/_static/image27.png)

**Рис. 14**: включение отладки ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image28.png))


По умолчанию возвращается в представление Index. В этом представлении перечислены все данные из таблицы базы данных контактов (см. рис. 15).


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image15.jpg)](iteration-1-create-the-application-cs/_static/image29.png)

**Рис. 15**: индекс представления ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image30.png))


Обратите внимание на то, что в представление Index со ссылкой с меткой Create New в нижней части представления. В следующем разделе вы узнаете, как для создания новых контактов.

## <a name="creating-new-contacts"></a>Создание новых контактов

Чтобы пользователи могли создавать новые контакты, нам нужно добавить два действия Create() контроллера Home. Нам нужно создать один Create() действие, которое возвращает форму HTML для создания нового контакта. Нам нужно создать второе действие Create(), выполняющий вставки нового элемента реальной базы данных.

Новые методы Create(), нам нужно добавить в контроллер Home, содержатся в листинге 4.

**Листинг 4 - Controllers\HomeController.cs (с создавать методы)**

[!code-csharp[Main](iteration-1-create-the-application-cs/samples/sample4.cs)]

Первый метод Create() может быть вызван с помощью HTTP GET, во время второй метод Create() может быть вызвана только запрос HTTP POST. Другими словами второй метод Create() может вызываться только в том случае, при отправке формы HTML. Первый метод Create() просто возвращает представление, содержащее форму HTML для создания нового контакта. Второй метод Create() гораздо более интересные: он добавляет новый контакт в базу данных.

Обратите внимание на то, что второй метод Create() был изменен для принятия экземпляра на класс Contact. Формы значения, переданные из формы HTML доступен к этому классу контакт платформа ASP.NET MVC автоматически. Каждого поля формы из формы HTML создайте назначается свойство параметра контакта.

Обратите внимание на то, что параметр контакт дополнен атрибутом [привязки]. Атрибут [привязки] используется для исключения свойство идентификатора контактного лица из привязки. Так как свойство Id представляет свойство Identity, мы кое t, необходимо задать свойство идентификатора.

В теле метода Create() Entity Framework используется для вставки нового контакта в базе данных. Новый контакт добавляется в существующий набор контактов и вызове метода SaveChanges() необходимо передать эти изменения обратно в основную базу данных.

Вы можете создать HTML-формы для создания новых контактов, щелкните правой кнопкой мыши один из двух методов Create() и выбрав пункт меню **Добавление представления** (см. рис. 16).


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image16.jpg)](iteration-1-create-the-application-cs/_static/image31.png)

**Рис. 16**: Добавление представления создания ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image32.png))


В **Добавление представления** диалоговом окне выберите **ContactManager.Models.Contact** класс и **создать** параметр для представления содержимого (см. рис. 17). При нажатии кнопки **добавить** кнопку Create view, создается автоматически.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image17.jpg)](iteration-1-create-the-application-cs/_static/image33.png)

**Рис. 17**: развернуть страница отображается ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image34.png))


Создать представление содержит поля формы для каждого из свойств класса, обратитесь в службу. Код для представления создания включается в листинге 5.

**В листинге 5 - Views\Home\Create.aspx**

[!code-aspx[Main](iteration-1-create-the-application-cs/samples/sample5.aspx)]

После изменения методов Create() и Добавление представления создания, можно запустить приложение диспетчера контактов и создания новых контактов. Нажмите кнопку **Create New** ссылку, которая появляется в представлении индекса, чтобы перейти в представление Create. Вы должны увидеть представление на рис. 18.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image18.jpg)](iteration-1-create-the-application-cs/_static/image35.png)

**Рис. 18**: The Create View ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image36.png))


## <a name="editing-contacts"></a>Изменения контактов

Добавление функциональности для редактирования запись контакта очень сходно с добавлением функциональные возможности для создания новых записей контактов. Во-первых необходимо добавить два новых метода Edit к классу контроллера Home. Эти новые методы Edit() содержатся в листинге 6.

**В листинге 6 - Controllers\HomeController.cs (с помощью методов Edit)**

[!code-csharp[Main](iteration-1-create-the-application-cs/samples/sample6.cs)]

Первый метод Edit() вызывается операция HTTP GET. Параметр идентификатора передается этому методу, который представляет идентификатор записи контакта, который редактируется. Платформа Entity Framework используется для получения контакта, который соответствует идентификатору. Представление, содержащее форму HTML для редактирования и возвращается.

Второй метод Edit() выполняет фактического обновления к базе данных. Этот метод принимает экземпляр класса контакта в качестве параметра. Платформа ASP.NET MVC привязывает поля формы из формы редактирования к этому классу автоматически. Обратите внимание на то, что у вас нет t включает атрибут [привязки] при редактировании контакт (требуется значение свойства идентификатора).

Платформа Entity Framework используется для сохранения измененного контакта в базе данных. Исходного контакта необходимо сначала получить из базы данных. Затем метод Entity Framework ApplyPropertyChanges() вызывается для записи изменений контактному лицу. Наконец вызывается метод Entity Framework SaveChanges(), для сохранения изменений в основную базу данных.

Вы можете создать представление, содержащее форму редактирования, щелкните правой кнопкой мыши метод Edit() и выбрав пункт меню Вид Add. В диалоговом окне «Добавление представления» выберите **ContactManager.Models.Contact** класс и **изменить** Просмотр содержимого (см. рис. 19).


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image19.jpg)](iteration-1-create-the-application-cs/_static/image37.png)

**Рис. 19**: Добавление представления редактирования ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image38.png))


При нажатии кнопки «Добавить», автоматически создается новое представление редактирования. HTML-формы, который создается содержит поля, которые соответствуют каждому из свойств на класс Contact (см. Листинг 7).

**Листинг 7 - Views\Home\Edit.aspx**

[!code-aspx[Main](iteration-1-create-the-application-cs/samples/sample7.aspx)]

## <a name="deleting-contacts"></a>Удаление контактов

Если вы хотите удалять контакты, необходимо добавить два действия Delete() к классу контроллера Home. Первое действие Delete() отображает форму подтверждения удаления. Второе действие Delete() выполняет фактическое удаление.

> [!NOTE] 
> 
> Позже в итерации #7, мы изменим Contact Manager так, чтобы она поддерживала один шаг, удалить Ajax.


Два новых метода Delete() содержатся в листинг 8.

**Листинг 8 - Controllers\HomeController.cs (методы удаления)**

[!code-csharp[Main](iteration-1-create-the-application-cs/samples/sample8.cs)]

Первый метод Delete() возвращает форму подтверждения удаления запись контакта из базы данных (см. в разделе Figure20). Второй метод Delete() выполняется фактическая операция удаления в базе данных. После получения исходного контакта из базы данных, эти методы DeleteObject () для Entity Framework и SaveChanges() вызываются выполнение удаления базы данных.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image20.jpg)](iteration-1-create-the-application-cs/_static/image39.png)

**Рис. 20**: представление подтверждения удаления ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image40.png))


Нам нужно изменить представление Index, таким образом, чтобы он содержит ссылки на удаление контактные записи (см. рис. 21). Вам потребуется добавить следующий код в той же ячейке таблицы, содержащий ссылку "Изменить".

Html.ActionLink ({id = элемент. % Идентификатор})&gt;


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image21.jpg)](iteration-1-create-the-application-cs/_static/image41.png)

**Рис. 21**: индексировать представление с ссылку "Изменить" ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image42.png))


Далее нам нужно создать представление подтверждения удаления. Щелкните правой кнопкой мыши метод Delete() в классе контроллера Home и выберите пункт меню Add View. Добавление представления диалогового окна отображается (см. рис. 22).

В отличие от в случае представления списка, Create и Edit, диалоговое окно Добавить представление не содержит параметр для создания представления удаления. Вместо этого выберите **ContactManager.Models.Contact** класс данных и **пустой** просматривать содержимое. Выбор пустое представление, что параметр содержимого потребуется нам для создания представления, сами.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image22.jpg)](iteration-1-create-the-application-cs/_static/image43.png)

**Рис. 22**: Добавление в представление подтверждения удаления ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image44.png))


Содержимое представления удаления содержится в Листинг 9. Это представление содержит форму, подтверждает ли определенный контакт должен быть удален (см. рис. 21).

**Листинг 9 - Views\Home\Delete.aspx**

[!code-aspx[Main](iteration-1-create-the-application-cs/samples/sample9.aspx)]

## <a name="changing-the-name-of-the-default-controller"></a>Изменение имени контроллера по умолчанию

Он может очень досаждает что имя нашего класса контроллера для работы с контактами называется в класс HomeController. T не может контроллер называться ContactController?

Эта проблема не будет довольно легко исправить. Во-первых нам нужно выполнить рефакторинг имени контроллера Home. Откройте класс HomeController в редакторе кода Visual Studio, щелкните правой кнопкой мыши имя класса и выберите пункт меню **Рефакторинг переименования**. Если выбрать этот пункт меню открывается диалоговое окно переименования.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image23.jpg)](iteration-1-create-the-application-cs/_static/image45.png)

**Рис. 23**: рефакторинг имени контроллера ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image46.png))


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image24.jpg)](iteration-1-create-the-application-cs/_static/image47.png)

**Рис. 24**: с помощью диалогового окна Rename ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image48.png))


При переименовании вашего класса контроллера, Visual Studio будет обновить имя папки в папке Views. Visual Studio будет переименовать папку \Views\Home \Views\Contact папку.

После внесения этого изменения приложение больше не будет иметь контроллера Home. Когда вы запустите приложение, вы получите страницы ошибки на рис. 25.


[![В диалоговом окне нового проекта](iteration-1-create-the-application-cs/_static/image25.jpg)](iteration-1-create-the-application-cs/_static/image49.png)

**Рис. 25**: без контроллера по умолчанию ([Просмотр полноразмерного изображения](iteration-1-create-the-application-cs/_static/image50.png))


Необходимо обновить маршрут по умолчанию в файле Global.asax, чтобы использовать контакт контроллер вместо контроллера Home. Откройте файл Global.asax и изменение контроллера по умолчанию, используемый в маршруте по умолчанию (см. Листинг 10).

**Листинг 10 - Global.asax.cs**

[!code-csharp[Main](iteration-1-create-the-application-cs/samples/sample10.cs)]

После внесения этих изменений Диспетчер контактов будет работать неправильно. Теперь он будет использовать класс контроллера Contact как контроллер по умолчанию.

## <a name="summary"></a>Сводка

В этой первой итерации мы создали простое приложение диспетчера контактов в самым быстрым способом невозможно. Мы воспользовались преимуществами Visual Studio, чтобы автоматически создать исходный код для контроллеров и представлений. Кроме того, мы воспользовались преимуществами Entity Framework, чтобы автоматически создать классах модели базы данных.

В настоящее время мы можно список, создать, изменить и удалить контакты с приложением диспетчера контактов. Другими словами мы все можно выполнять основные операции, необходимые для основанных на базах данных веб-приложения.

К сожалению наше приложение имеет некоторые проблемы. Первый и я колебаний должен признать, это, приложение диспетчера контактов не является наиболее привлекательных приложений. Она должна некоторую работу конструктора. В следующей итерации мы рассмотрим, как мы можем изменить главной страницы представления по умолчанию и каскадные таблицы стилей, чтобы улучшить внешний вид приложения.

Во-вторых мы не реализовали все проверки формы. Например нет ничего для предотвращения отправки формы контактов создать без ввода значений для полей формы. Кроме того можно ввести недопустимый телефон номеров и адресов электронной почты. Мы начнем разобраться с проблемой проверки формы в итерации #3.

Наконец и что самое важное текущую итерацию приложения диспетчера контактов легко изменения или обслуживание. Например логика доступа к базе данных вмурованы справа в действий контроллера. Это означает, что мы не может изменить наш код доступа к данным без изменения наш контроллеров. В последующих итерациях мы рассмотрим принципы разработки программного обеспечения, которые мы можем реализовать в диспетчере контактов повысить сопротивляемость для изменения.

> [!div class="step-by-step"]
> [Вперед](iteration-2-make-the-application-look-nice-cs.md)
