---
title: Справочник по конфигурации модуля ASP.NET Core
author: guardrex
description: Сведения о настройке модуля ASP.NET Core для размещения приложений ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2018
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 5a3fd9c3453c07ee550c7de0333c9a49d5d5d1af
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450662"
---
# <a name="aspnet-core-module-configuration-reference"></a>Справочник по конфигурации модуля ASP.NET Core

Авторы [Люк Латэм](https://github.com/guardrex) (Luke Latham), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Сураб Ширхатти](https://twitter.com/sshirhatti) (Sourabh Shirhatti) и [Джастин Коталик](https://github.com/jkotalik) (Justin Kotalik)

Этот документ содержит инструкции о том, как настроить модуль ASP.NET Core для размещения приложений ASP.NET Core. Для ознакомления с модулем ASP.NET Core и инструкциями по установке см. [Обзор модуля ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).

::: moniker range=">= aspnetcore-2.2"

## <a name="hosting-model"></a>Модель размещения

Для приложений, выполняющихся на .NET Core 2.2 или более поздней версии, модуль поддерживает модель внутрипроцессного размещения для повышения производительности по сравнению с размещением на обратном прокси-сервере (вне процесса). Дополнительные сведения см. в разделе <xref:fundamentals/servers/aspnet-core-module#aspnet-core-module-description>.

Внутрипроцессное размещение необходимо явно выбирать в существующих приложениях, но в шаблонах [dotnet new](/dotnet/core/tools/dotnet-new) оно включено по умолчанию для всех сценариев IIS и IIS Express.

Чтобы настроить приложение для внутрипроцессного размещения, добавьте свойство `<AspNetCoreHostingModel>` в файл проекта приложения (например, *MyApp.csproj*) со значением `inprocess` (размещение вне процесса имеет значение `outofprocess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>inprocess</AspNetCoreHostingModel>
</PropertyGroup>
```

При внутрипроцессном размещении применимы следующие характеристики:

* [Сервер Kestrel](xref:fundamentals/servers/kestrel) не используется. Пользовательская реализация <xref:Microsoft.AspNetCore.Hosting.Server.IServer> `IISHttpServer` выступает в качестве сервера приложения.

* [Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.

* Совместное использование пула приложений среди приложений не поддерживается. Используйте один пул приложений для каждого приложения.

* При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла app_offline.htm в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения. Например, подключение websocket может задерживать завершение работы приложения.

* Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.

* Если вы настраиваете узел приложения вручную с помощью `WebHostBuilder` (не используя [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) и приложение запускается напрямую на сервере Kestrel (с локальным размещением), вызывайте `UseKestrel` перед вызовом `UseIISIntegration`. Если изменить порядок, узел не запустится.

* Обнаружены отключения клиентов. При отключении клиента происходит отмена токена отмены [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*).

* `Directory.GetCurrentDirectory()` возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, *C:\Windows\System32\inetsrv* для *w3wp.exe*).

### <a name="hosting-model-changes"></a>Изменения модели размещения

Если параметр `hostingModel` изменяется в файле *web.config* (как описано в разделе [Конфигурация с помощью web.config](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.

Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express. Следующий запрос для приложения порождает новый процесс IIS Express.

### <a name="process-name"></a>Имя процесса

`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).

::: moniker-end

## <a name="configuration-with-webconfig"></a>Конфигурация с помощью файла web.config

Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.

Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet" 
                  arguments=".\MyApp.dll" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" 
                arguments=".\MyApp.dll" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [\<расположение>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`. Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.

Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Атрибуты элемента aspNetCore

::: moniker range=">= aspnetcore-2.2"

| Атрибут | Описание: | Значение по умолчанию |
| --------- | ----------- | :-----: |
| `arguments` | <p>Необязательный строковый атрибут.</p><p>Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса. Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</p> | `true` |
| `hostingModel` | <p>Необязательный строковый атрибут.</p><p>Указывает модель размещения — внутри процесса (`inprocess`) или вне процесса (`outofprocess`).</p> | `outofprocess` |
| `processesPerApplication` | <p>Необязательный целочисленный атрибут.</p><p>Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</p><p>&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</p> | По умолчанию: `1`<br>Минимум: `1`<br>Максимум: `100`&dagger; |
| `processPath` | <p>Обязательный строковый атрибут.</p><p>Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов. Поддерживаются относительные пути. Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</p> | |
| `rapidFailsPerMinute` | <p>Необязательный целочисленный атрибут.</p><p>Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**. Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</p><p>Не поддерживается для внутрипроцессного размещения.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `100` |
| `requestTimeout` | <p>Необязательный атрибут timespan.</p><p>Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</p><p>В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</p><p>Не применяется к внутрипроцессному размещению. Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</p> | По умолчанию: `00:02:00`<br>Минимум: `00:00:00`<br>Максимум: `360:00:00` |
| `shutdownTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `600` |
| `startupTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла. Если этот предел превышен, модуль завершает процесс. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</p><p>Значение 0 (ноль) **не** считается бесконечным временем ожидания.</p> | По умолчанию: `120`<br>Минимум: `0`<br>Максимум: `3600` |
| `stdoutLogEnabled` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Необязательный строковый атрибут.</p><p>Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**. Относительные пути задаются относительно корневого каталога веб-сайта. Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути. Все папки, указанные в пути, должны существовать, чтобы модуль мог создать файл журнала. С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла (*.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**. Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| Атрибут | Описание: | Значение по умолчанию |
| --------- | ----------- | :-----: |
| `arguments` | <p>Необязательный строковый атрибут.</p><p>Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса. Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</p> | `true` |
| `processesPerApplication` | <p>Необязательный целочисленный атрибут.</p><p>Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</p> | По умолчанию: `1`<br>Минимум: `1`<br>Максимум: `100` |
| `processPath` | <p>Обязательный строковый атрибут.</p><p>Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов. Поддерживаются относительные пути. Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</p> | |
| `rapidFailsPerMinute` | <p>Необязательный целочисленный атрибут.</p><p>Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**. Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `100` |
| `requestTimeout` | <p>Необязательный атрибут timespan.</p><p>Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</p><p>В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</p> | По умолчанию: `00:02:00`<br>Минимум: `00:00:00`<br>Максимум: `360:00:00` |
| `shutdownTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `600` |
| `startupTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла. Если этот предел превышен, модуль завершает процесс. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</p><p>Значение 0 (ноль) **не** считается бесконечным временем ожидания.</p> | По умолчанию: `120`<br>Минимум: `0`<br>Максимум: `3600` |
| `stdoutLogEnabled` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Необязательный строковый атрибут.</p><p>Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**. Относительные пути задаются относительно корневого каталога веб-сайта. Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути. Все папки, указанные в пути, должны существовать, чтобы модуль мог создать файл журнала. С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла (*.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**. Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| Атрибут | Описание: | Значение по умолчанию |
| --------- | ----------- | :-----: |
| `arguments` | <p>Необязательный строковый атрибут.</p><p>Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса. Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</p> | `true` |
| `processesPerApplication` | <p>Необязательный целочисленный атрибут.</p><p>Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</p> | По умолчанию: `1`<br>Минимум: `1`<br>Максимум: `100` |
| `processPath` | <p>Обязательный строковый атрибут.</p><p>Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов. Поддерживаются относительные пути. Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</p> | |
| `rapidFailsPerMinute` | <p>Необязательный целочисленный атрибут.</p><p>Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**. Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `100` |
| `requestTimeout` | <p>Необязательный атрибут timespan.</p><p>Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</p><p>В версиях модуля ASP.NET Core, поставляемых вместе с выпуском ASP.NET Core 2.0 или более ранними версиями, атрибут `requestTimeout` должен был быть задан в целых минутах (в противном случае для него по умолчанию задано значение 2 минуты).</p> | По умолчанию: `00:02:00`<br>Минимум: `00:00:00`<br>Максимум: `360:00:00` |
| `shutdownTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `600` |
| `startupTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла. Если этот предел превышен, модуль завершает процесс. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</p> | По умолчанию: `120`<br>Минимум: `0`<br>Максимум: `3600` |
| `stdoutLogEnabled` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Необязательный строковый атрибут.</p><p>Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**. Относительные пути задаются относительно корневого каталога веб-сайта. Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути. Все папки, указанные в пути, должны существовать, чтобы модуль мог создать файл журнала. С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла (*.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**. Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a>Настройка переменных среды

Переменные среды для процесса можно указать в атрибуте `processPath`. Укажите переменную среды с дочерним элементом `environmentVariable` элемента коллекции `environmentVariables`. Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.

В следующем примере устанавливаются две переменные среды. `ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`. Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения. `CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

> [!WARNING]
> Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.

## <a name="appofflinehtm"></a>App_offline.htm

Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов. Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.

Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*. Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.

::: moniker range=">= aspnetcore-2.2"

При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения. Например, подключение websocket может задерживать завершение работы приложения.

::: moniker-end

## <a name="start-up-error-page"></a>Страница ошибок запуска

::: moniker range=">= aspnetcore-2.2"

Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.

Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.

Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.

Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.

Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`. Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Если модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*. Чтобы подавить эту страницу и вернуться к странице IIS кода состояния 502 по умолчанию, используйте атрибут `disableStartUpErrorPage`. Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

![Страница кода состояния "502.5 — ошибка процесса"](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a>Создание и перенаправление журнала

Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`. Чтобы модуль мог создать файл журнала, все папки в пути `stdoutLogFile` должны существовать. Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).

Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки. Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.

Использование журнала stdout рекомендуется только для устранения неполадок при запуске приложений. Не используйте журнал stdout для ведения общего журнала приложений. Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов. Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).

При создании файла журнала автоматически добавляются отметка времени и расширение файла. Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла (*.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей. Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.

::: moniker range=">= aspnetcore-2.2"

Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ). После запуска все дополнительные журналы удаляются.

::: moniker-end

В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout для приложения, размещенного в службе приложений Azure. Для локального ведения журнала допустим локальный или общий сетевой путь. Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a>Расширенные журналы диагностики

Модуль ASP.NET Core предоставляет возможности настройки, позволяющие работать с расширенными журналами диагностики. Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в файле *web.config*. Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.

Уровни (в порядке возрастания степени детализации):

* ОШИБКА
* ПРЕДУПРЕЖДЕНИЕ
* ИНФОРМАЦИЯ
* TRACE

Расположения (допускаются несколько расположений):

* CONSOLE
* EVENTLOG
* ФАЙЛ

Параметры обработчика могут быть указаны с помощью переменных среды:

* `ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Путь к файлу журнала отладки. (По умолчанию: *aspnetcore debug.log*)
* `ASPNETCORE_MODULE_DEBUG` &ndash; Параметр уровня отладки.

> [!WARNING]
> **Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы. Размер журнала не ограничен. Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.

::: moniker-end

См. пример элемента `aspNetCore` в файле *web.config* в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Конфигурация прокси-сервера использует протокол HTTP и токен связывания

::: moniker range=">= aspnetcore-2.2"

*Применяется только к размещению вне процесса.*

::: moniker-end

Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP. Использование HTTP позволяет оптимизировать производительность, так как трафик между модулем и Kestrel передается на петлевой адрес в сетевом интерфейсе. Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.

Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника. Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`). Он также задается в заголовке (`MSAspNetCoreToken`) каждого запроса, переданного через прокси-сервер. ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды. Если значения токена не совпадают, запрос заносится в журнал и отклоняется. Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере. Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Модуль ASP.NET Core с общей конфигурацией IIS

Программа установки модуля ASP.NET Core запускается с правами **системной** учетной записи. Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки. При использовании общей конфигурации IIS выполните следующие действия.

1. Отключите общую конфигурацию IIS.
1. Запустите установщик.
1. Экспортируйте обновленный файл *applicationHost.config* в общую папку.
1. Повторно включите общую конфигурацию IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Версия модуля и журналы установщика хостинга Bundle

Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.

1. В системе размещения перейдите к папке *%windir%\System32\inetsrv*.
1. Найдите файл *aspnetcore.dll*.
1. Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.
1. Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.

Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Модуль, схемы и расположение файлов конфигурации

### <a name="module"></a>Module

**IIS (x86/amd64):**

   * %windir%\System32\inetsrv\aspnetcore.dll

   * %windir%\SysWOW64\inetsrv\aspnetcore.dll

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

   * %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

::: moniker-end

**IIS Express (x86/amd64):**

   * %ProgramFiles%\IIS Express\aspnetcore.dll

   * %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

   * %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

::: moniker-end

### <a name="schema"></a>Схема

**Службы IIS**

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

::: moniker range=">= aspnetcore-2.2"

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

::: moniker-end
**IIS Express**

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

::: moniker range=">= aspnetcore-2.2"

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

::: moniker-end

### <a name="configuration"></a>Конфигурация

**Службы IIS**

   * %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

   * %ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config

Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.
