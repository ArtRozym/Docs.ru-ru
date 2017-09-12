---
title: "Безопасного хранения секрета приложения во время разработка решений в ASP.NET Core"
author: rick-anderson
description: "Показано, как безопасно хранить секреты во время разработки"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/app-secrets
ms.openlocfilehash: 56214c2fbdca84591c5c1a6b7f2451f33ee64ef0
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2017
---
# <a name="safe-storage-of-app-secrets-during-development-in-aspnet-core"></a>Безопасного хранения секрета приложения во время разработки в ASP.NET Core

<a name=security-app-secrets></a>

По [Рик Андерсон](https://twitter.com/RickAndMSFT), [рот Daniel](https://github.com/danroth27), и [Скотт Addie](https://scottaddie.com) 

В этом документе показано, как можно использовать диспетчер секрет при разработке решений для хранить секреты вне кода. Самое главное, никогда не следует хранить пароли и другие конфиденциальные данные в исходном коде и секретные данные в рабочей среде не следует использовать в режиме разработки и тестирования. Вместо этого можно использовать [конфигурации](../fundamentals/configuration.md) системы для считывания этих значений из переменных среды или из значения, сохраненные с помощью диспетчера секрет средства. Секрет диспетчера предотвращает конфиденциальные данные проверяются в систему управления версиями. [Конфигурации](../fundamentals/configuration.md) системы, может прочитать секретные данные, хранимые с помощью средства диспетчера секрет, описанных в этой статье.

Секрет диспетчера используется только при разработке. Можно защитить Azure рабочего и тестового секреты с [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) поставщика конфигурации. В разделе [конфигурации поставщика хранилища ключей Azure](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration) для получения дополнительной информации.

## <a name="environment-variables"></a>Переменные среды

Чтобы избежать хранения секрета приложения в коде или в локальных файлов конфигурации, хранить секреты в переменных среды. Вы можете настроить [конфигурации](../fundamentals/configuration.md) framework для считывания значений из переменных среды, вызвав `AddEnvironmentVariables`. Затем можно использовать переменные среды для переопределения значения конфигурации для всех источников ранее указанной конфигурации.

Например, при создании нового веб-приложения ASP.NET Core для каждой учетной записи, оно добавляет строку подключения по умолчанию для *appsettings.json* файл в проекте с помощью ключа `DefaultConnection`. Строка подключения по умолчанию настроена на использование LocalDB, которая запускается в пользовательском режиме и не требует пароля. При развертывании приложения на тестовом или рабочем сервере, можно переопределить `DefaultConnection` значение ключа с параметром переменной среды, содержащее строку подключения (возможно, с учетом учетные данные) для тестовой или рабочей базы данных сервер.

>[!WARNING]
> Переменные среды обычно хранятся в виде обычного текста и не шифруются. В случае компрометации компьютера или процесс, переменные среды возможен ненадежных сторонами. По-прежнему могут требоваться дополнительные меры, чтобы предотвратить раскрытие секретные данные пользователей.

## <a name="secret-manager"></a>Диспетчер секрет

Секрет диспетчера сохраняет конфиденциальные данные вне вашего дерева проектов для разработки. Секрет диспетчера — это средство проекта, который может использоваться для хранения конфиденциальных данных для [.NET Core](https://www.microsoft.com/net/core) проекта во время разработки. С помощью средства диспетчера секрет можно связать секрета приложения с конкретным проектом и использовать их совместно в нескольких проектах.

>[!WARNING]
> Секрет диспетчера хранимых секретные данные не шифруются и не будет считаться доверенного хранилища. Это только в целях разработки. Ключи и значения хранятся в файле конфигурации JSON в папке профиля пользователя.

### <a name="visual-studio-2017-installing-the-secret-manager-tool"></a>Visual Studio 2017 г.: Секрет диспетчера установки

Щелкните правой кнопкой мыши проект в обозревателе решений и выберите **изменить \<project_name\>.csproj** в контекстном меню. Добавьте в выделенной строке *.csproj* и сохранение файлов для восстановления, связанный с ним пакет NuGet:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=21)]

Щелкните правой кнопкой мыши проект в обозревателе решений и выберите **управление секретами пользователя** в контекстном меню. Это жест добавляет новый `UserSecretsId` узла в `PropertyGroup` из *.csproj* файла. Также открывает `secrets.json` файл в текстовом редакторе.

Добавьте следующий код в файл `secrets.json`:

```json
{
    "MySecret": "ValueOfMySecret"
}
```

### <a name="visual-studio-2015-installing-the-secret-manager-tool"></a>Visual Studio 2015: Секрет диспетчера установки

Откройте проект `project.json` файла. Добавьте ссылку на `Microsoft.Extensions.SecretManager.Tools` в `tools` свойство и сохранить, чтобы восстановить связанный с ним пакет NuGet:

```json
"tools": {
    "Microsoft.Extensions.SecretManager.Tools": "1.0.0-preview2-final",
    "Microsoft.AspNetCore.Server.IISIntegration.Tools": "1.0.0-preview2-final"
},
```

Щелкните правой кнопкой мыши проект в обозревателе решений и выберите **управление секретами пользователя** в контекстном меню. Это жест добавляет новый `userSecretsId` свойства `project.json`. Также открывает `secrets.json` файл в текстовом редакторе.

Добавьте следующий код в файл `secrets.json`:

```json
{
    "MySecret": "ValueOfMySecret"
}
```

### <a name="visual-studio-code-or-command-line-installing-the-secret-manager-tool"></a>Visual Studio Code или командной строки: Установка средства диспетчера секрет

Добавить `Microsoft.Extensions.SecretManager.Tools` для *.csproj* файл и запустите `dotnet restore`.

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=21)]

Тестирование диспетчера секрет, выполнив следующую команду:

```console
dotnet user-secrets -h
```

Диспетчер секрет выводится использования, параметры и справки.

> [!NOTE]
> Должен быть в том же каталоге, что *.csproj* файла для запуска средства, предназначенные для *.csproj* файла `DotNetCliToolReference` узлов.

Секрет диспетчера обрабатывает параметры конфигурации для конкретного проекта, которые хранятся в профиле пользователя. Чтобы использовать секретные данные пользователей, необходимо указать проект `UserSecretsId` значение в его *.csproj* файла. Значение `UserSecretsId` может быть произвольным, но уникален в проект. Разработчики обычно создать GUID для `UserSecretsId`.

Добавить `UserSecretsId` проекта в *.csproj* файла:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?range=7-9&highlight=2)]

Использование диспетчера секрет, чтобы задать секрет. Например в окно командной строки от каталога проекта, введите следующую команду:

```console
dotnet user-secrets set MySecret ValueOfMySecret
```

Запустить диспетчер секрет из других каталогов, но необходимо использовать `--project` параметр для передачи в пути к *.csproj* файла:
 
```console
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
```

Также можно использовать средство диспетчера секрет для перечисления, удаления и очистите секреты приложения.

## <a name="accessing-user-secrets-via-configuration"></a>Доступ к секретной информации пользователя через конфигурацию

Секреты Manager секрет доступ через систему конфигурации. Добавить `Microsoft.Extensions.Configuration.UserSecrets` упаковка и запуск `dotnet restore`.

Добавить источник конфигурации секретные данные пользователя для `Startup` метод:

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]

Вы можете использовать секретные данные пользователей через API-Интерфейс настройки:

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]

## <a name="how-the-secret-manager-tool-works"></a>Как работает диспетчер секрет

Секрет диспетчера абстрагирует детали реализации, например, где и как значения сохраняются. Можно использовать средство, не зная эти детали реализации. В текущей версии значения хранятся в [JSON](http://json.org/) файл конфигурации в папке профиля пользователя:

* Windows:`%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`

* Linux:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

* MAC:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

Значение `userSecretsId` берется из значения, указанного в *.csproj* файла.

Не следует писать код, зависящий от расположение или формат данных, сохраненных с помощью диспетчера секрет средства, как эти сведения реализации может измениться. Например, в настоящее время являются значения секрета *не* сегодня зашифрованы, но может быть когда-нибудь.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Конфигурация](../fundamentals/configuration.md)