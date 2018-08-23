---
title: Размещение ASP.NET Core в службе приложений Azure
author: guardrex
description: Узнайте, как размещать приложения ASP.NET Core в службе приложений Azure, с помощью ссылок на полезные ресурсы.
ms.author: riande
ms.custom: mvc
ms.date: 07/24/2018
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: 9a7d20378cac597b748d8a60eb0f0bf17c9ba082
ms.sourcegitcommit: d27317c16f113e7c111583042ec7e4c5a26adf6f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41746065"
---
# <a name="host-aspnet-core-on-azure-app-service"></a>Размещение ASP.NET Core в службе приложений Azure

[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) — это [платформа облачных вычислений Microsoft](https://azure.microsoft.com/), предназначенная для размещения веб-приложений, включая ASP.NET Core.

## <a name="useful-resources"></a>Полезные ресурсы

[Документация по веб-приложениям](/azure/app-service/) Azure — это место, где хранятся документация, учебники, примеры, руководства и другие ресурсы, связанные с приложениями Azure. Размещению приложений ASP.NET Core посвящены следующие два руководства.

[Краткое руководство. Создание веб-приложения ASP.NET Core в Azure](/azure/app-service/app-service-web-get-started-dotnet)  
Создайте веб-приложение ASP.NET Core и разверните его в службе приложений Azure на базе Windows с помощью Visual Studio.

[Краткое руководство. Создание веб-приложения .NET Core в службе приложений на базе Linux](/azure/app-service/containers/quickstart-dotnetcore)  
Создайте веб-приложение ASP.NET Core и разверните его в службе приложений Azure на базе Linux с помощью командной строки.

Следующие статьи входят в документацию по ASP.NET Core.

[Опубликовать в Azure с помощью Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs)  
Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью Visual Studio.

[Публикация в Azure с инструментами командной строки](xref:tutorials/publish-to-azure-webapp-using-cli)  
Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью клиента командной строки Git.

[Непрерывное развертывание в Azure с помощью Visual Studio и Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)  
Сведения о создании веб-приложения ASP.NET Core с помощью Visual Studio и его развертывании в службе приложений Azure с использованием Git для непрерывного развертывания.

[Непрерывное развертывание в Azure с помощью VSTS](https://www.visualstudio.com/docs/build/aspnet/core/quick-to-azure)  
Сведения о настройке сборки CI для приложения ASP.NET Core и последующем создании выпуска непрерывного развертывания в службе приложений Azure.

[Песочница веб-приложений Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
Сведения об ограничениях среды выполнения службы приложений Azure, создаваемых платформой приложений Azure.

::: moniker range=">= aspnetcore-2.0"

## <a name="application-configuration"></a>Настройка приложения

В ASP.NET Core 2.0 или более поздней версии следующие пакеты NuGet предоставляют возможности автоматического ведения журнала для приложений, развернутых в службе приложений Azure:

* [Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) использует [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) для интеграции подсветки ASP.NET Core в службу приложений Azure. Пакет `Microsoft.AspNetCore.AzureAppServicesIntegration` включает дополнительные функции для ведения журналов.
* [Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) выполняет [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) для добавления поставщиков ведения журналов диагностики из службы приложений Azure в пакет `Microsoft.Extensions.Logging.AzureAppServices`.
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) предоставляет реализации средства ведения журналов в поддержку журналов диагностики службы приложений Azure и функций потоковой передачи журналов.

Если планируется использовать .NET Core и указана ссылка на [метапакет Microsoft.AspNetCore.All](xref:fundamentals/metapackage), пакеты уже включены. Пакеты отсутствуют в более новом [метапакете Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Если планируется использовать .NET Framework или указана ссылка на метапакет `Microsoft.AspNetCore.App`, ссылайтесь на отдельные пакеты ведения журнала.

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

ПО промежуточного слоя для интеграции IIS, которое настраивает ПО промежуточного слоя переадресации заголовков, и модуль ASP.NET Core настраиваются на пересылку схемы (HTTP/HTTPS) и удаленного IP-адреса расположения, где был сформирован запрос. Для приложений, размещенных за дополнительными прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка. Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).

## <a name="monitoring-and-logging"></a>Мониторинг и ведение журналов

Сведения о мониторинге, ведении журналов, а также поиске и устранении неполадок см. в следующих статьях.

[Мониторинг приложений в службе приложений Azure](/azure/app-service/web-sites-monitor)  
Сведения о том, как толковать квоты и параметры для приложений и планы службы приложений.

[Включение функции ведения журналов диагностики для веб-приложений в службе приложений Azure](/azure/app-service/web-sites-enable-diagnostic-log)  
Сведения о том, как включать и где искать функцию ведения журнала диагностики для кодов статуса HTTP, невыполненных запросов и активности веб-сервера.

[Общие сведения об обработке ошибок в ASP.NET Core](xref:fundamentals/error-handling)  
Сведения об основных методах обработки ошибок в приложениях ASP.NET Core.

[Устранение неполадок ASP.NET Core в службе приложений Azure](xref:host-and-deploy/azure-apps/troubleshoot)  
Сведения о диагностике проблем с развертыванием приложений ASP.NET Core в службе приложений Azure.

[Справочник по общим ошибкам для службы приложений Azure и служб IIS с ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference)  
См. распространенные ошибки с конфигурацией развертывания для приложений, размещенных в службе приложений Azure/IIS, и рекомендации по их устранению.

## <a name="data-protection-key-ring-and-deployment-slots"></a>Связка ключей для защиты данных и слоты развертывания

[Ключи для защиты данных](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) хранятся в папке *%HOME%\ASP.NET\DataProtection-Keys*. Эта папка копируется в сетевое хранилище и синхронизируется на всех машинах, где размещается приложение. Во время хранения ключи не защищаются. В этой папке хранится связка ключей для всех экземпляров приложения в одном и том же слоте развертывания. Отдельные слоты развертывания, такие как промежуточное хранение и производство, не используют общую связку ключей.

При переключении между разными слотами развертывания ни одна система с использованием защиты данных не сможет расшифровать хранимые данные, используя связку ключей из предыдущего слота. Для защиты файлов cookie ПО промежуточного слоя ASP.NET Cookie использует защиту данных. В результате пользователей, применяющих ПО промежуточного слоя ASP.NET Cookie, выбрасывает из приложения. Для того чтобы решение связки ключей не зависело от слота, используйте внешнего поставщика связки ключей, например:

* Хранилище больших двоичных объектов Azure;
* Хранилище ключей Azure;
* Хранилище SQL;
* Кэш Redis.

Дополнительные сведения см. в разделе [Поставщики хранилища ключей](xref:security/data-protection/implementation/key-storage-providers).

## <a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>Развертывание предварительной версии ASP.NET Core в службе приложений Azure

Предварительную версию приложения ASP.NET Core можно развернуть в службе приложений Azure, используя следующие методы.

* [Установка расширения сайта предварительной версии](#install-the-preview-site-extension)
<!-- * [Deploy the app self-contained](#deploy-the-app-self-contained) -->
* [Использование Docker с веб-приложениями для контейнеров](#use-docker-with-web-apps-for-containers)

Если у вас возникли проблемы при использовании расширения сайта предварительной версии, сообщите о них на [GitHub](https://github.com/aspnet/azureintegration/issues/new).

### <a name="install-the-preview-site-extension"></a>Установка расширения сайта предварительной версии

1. На портале Azure перейдите к колонке "Служба приложений".
1. Выберите веб-приложение.
1. В поле поиска введите "ex" или прокрутите список панелей управления до пункта **СРЕДСТВА РАЗРАБОТКИ**.
1. Выберите **СРЕДСТВА РАЗРАБОТКИ** > **Расширения**.
1. Нажмите **Добавить**.

   ![Колонка "Приложение Azure" с предыдущими шагами](index/_static/x1.png)

1. Выберите **Расширения ASP.NET Core**.
1. Для принятия условий нажмите кнопку **ОК**.
1. Нажмите **OK**, чтобы установить расширение.

По завершении операций добавления устанавливается последняя предварительная версия .NET Core. Проверьте установку, запустив `dotnet --info` в консоли. В колонке **Служба приложений**.

1. В поле поиска введите "con" или прокрутите список панелей управления до пункта **СРЕДСТВА РАЗРАБОТКИ**.
1. Выберите **СРЕДСТВА РАЗРАБОТКИ** > **Консоль**.
1. Введите `dotnet --info` в консоли.

Если версия `2.1.300-preview1-008174` является последним предварительным выпуском, вы получите следующие выходные данные, выполнив `dotnet --info` в командной строке:

![Колонка "Приложение Azure" с предыдущими шагами](index/_static/cons.png)

В примере на предыдущей иллюстрации показана версия ASP.NET Core `2.1.300-preview1-008174`. Последняя предварительная версия ASP.NET Core во время настройки расширения сайта отображается при выполнении `dotnet --info`.

`dotnet --info` отображает путь к расширению сайта, где установлена предварительная версия. Он показывает, что приложение выполняется из расширения сайта, а не из расположения *ProgramFiles* по умолчанию. Если вы видите *ProgramFiles*, перезапустите сайт и выполните `dotnet --info`.

**Использование расширения сайта предварительной версии с шаблоном ARM**

Если вы используете шаблон ARM для создания и развертывания приложений, можно использовать тип ресурса `siteextensions`, чтобы добавить расширение сайта в веб-приложение. Пример:

[!code-json[Main](index/sample/arm.json?highlight=2)]

<!--
### Deploy the app self-contained

A [self-contained app](/dotnet/core/deploying/#self-contained-deployments-scd) can be deployed that carries the preview runtime in the deployment. When deploying a self-contained app:

* The site doesn't need to be prepared.
* The app must be published differently than when publishing for a framework-dependent deployment with the shared runtime and host on the server.

Self-contained apps are an option for all ASP.NET Core apps.
-->

### <a name="use-docker-with-web-apps-for-containers"></a>Использование Docker с веб-приложениями для контейнеров

[Центр Docker](https://hub.docker.com/r/microsoft/aspnetcore/) содержит образы Docker из последней предварительной версии. Их можно использовать в качестве базового образа. Использование образа и развертывание в веб-приложениях для контейнеров выполняется как обычно.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Общие сведения о веб-приложениях (5-минутное видео)](/azure/app-service/app-service-web-overview)
* [Служба приложений Azure: оптимальное место для размещения приложений .NET (55-минутное видео)](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [Azure, пятница: практики диагностики и устранения неполадок в службе приложений Azure (12-минутное видео)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [Общие сведения о диагностике в службе приложений Azure](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

Служба приложений Azure на Windows Server использует [службы IIS](https://www.iis.net/). Технологии IIS посвящены следующие статьи.

* <xref:host-and-deploy/iis/index>
* <xref:fundamentals/servers/aspnet-core-module>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [Библиотека Microsoft TechNet: Windows Server](/windows-server/windows-server-versions)
