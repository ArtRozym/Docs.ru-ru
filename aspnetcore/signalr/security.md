---
title: Вопросы безопасности в ASP.NET Core SignalR
author: tdykstra
description: Узнайте, как использовать проверку подлинности и авторизации в ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 06/29/2018
uid: signalr/security
ms.openlocfilehash: 98b5eb7be87920aacf7a941f76ff652ae7905303
ms.sourcegitcommit: f43f430a166a7ec137fcad12ded0372747227498
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49391262"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Вопросы безопасности в ASP.NET Core SignalR

По [Andrew Stanton медсестра](https://twitter.com/anurse)

## <a name="overview"></a>Обзор

SignalR обеспечивает ряд мер по обеспечению безопасности по умолчанию. Важно понять, как настроить эти средства защиты.

### <a name="cross-origin-resource-sharing"></a>Общий доступ к ресурсам независимо от источника

[Кросс-совместного использования ресурсов (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) может использоваться для подключений SignalR независимо от источника в браузере. Если код JavaScript размещается в другое доменное имя из приложения SignalR, необходимо включить [по промежуточного слоя ASP.NET Core CORS](xref:security/cors) чтобы разрешить подключение. Как правило позволяют запросов о происхождении только из доменов, контролируемых вами. Например, если ваш сайт размещается по `http://www.example.com` и SignalR приложение размещено на `http://signalr.example.com`, настраивайте CORS в приложении SignalR, разрешая только источник `www.example.com`.

Дополнительные сведения о настройке CORS см. в разделе [в документации по ASP.NET Core CORS](xref:security/cors). SignalR требуются следующие политики CORS для правильной работы:

* Политика должна разрешать определенных источников, ожидается, или разрешить любые источники (не рекомендуется).
* Методы HTTP `GET` и `POST` должны быть разрешены.
* Необходимо включить учетные данные, даже в том случае, если вы не используете проверку подлинности.

Например, следующая политика CORS позволяет клиенту браузера SignalR, размещенных на `http://example.com` для доступа к приложению SignalR:

```csharp
public void Configure(IApplicationBuilder app)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder => {
        builder.WithOrigins("http://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...

    app.UseSignalR();

    // ... other middleware ...
}
```

> [!NOTE]
> SignalR не совместим с встроенной функции CORS в службе приложений Azure.

### <a name="websocket-origin-restriction"></a>Ограничение WebSocket Origin

Защиты, предоставляемые CORS не применяются к WebSockets. Браузеры не требуют предварительных запросов CORS, а также они учитывают ограничений, указанных в `Access-Control` заголовки при составлении запросов WebSocket. Однако браузеры отправляют `Origin` заголовка при выдаче запросов WebSocket. Следует настроить приложение для проверки этих заголовков, чтобы убедиться, что только WebSockets, поступающие от источники, которые предполагается, что разрешены.

В ASP.NET Core 2.1, это можно сделать с помощью пользовательского по промежуточного слоя, можно поместить **выше `UseSignalR`и любое по промежуточного слоя проверки подлинности** в вашей `Configure` метод:

```csharp
// In your Startup class, add a static field listing the allowed Origin values:
private static readonly HashSet<string> _allowedOrigins = new HashSet<string>()
{
    // Add allowed origins here. For example:
    "http://www.mysite.com",
    "http://mysite.com",
};

// In your Configure method:
public void Configure(IApplicationBuilder app)
{
    // ... other middleware ...

    // Validate Origin header on WebSocket requests to prevent unexpected cross-site WebSocket requests
    app.Use((context, next) =>
    {
        // Check for a WebSocket request.
        if(string.Equals(context.Request.Headers["Upgrade"], "websocket"))
        {
            var origin = context.Request.Headers["Origin"];

            // If there is no origin header, or if the origin header doesn't match an allowed value:
            if(string.IsNullOrEmpty(origin) && !_allowedOrigins.Contains(origin))
            {
                // The origin is not allowed, reject the request
                context.Response.StatusCode = StatusCodes.Status400BadRequest;
                return Task.CompletedTask;
            }
        }

        // The request is not a WebSocket request or is a valid Origin, so let it continue
        return next();
    });

    // ... other middleware ...

    app.UseSignalR();

    // ... other middleware ...
}
```

> [!NOTE]
> `Origin` Заголовка полностью контролируется с помощью клиента и, подобно `Referer` заголовка, можно подделать. Эти заголовки никогда не должен использоваться как механизм проверки подлинности.

### <a name="access-token-logging"></a>Ведение журнала для маркера доступа

При использовании WebSockets или Server-Sent события, браузер клиент отправляет маркер доступа в строке запроса. Это обычно так безопасен, как с помощью стандарта `Authorization` заголовка, однако многие веб-серверы входа URL-адрес для каждого запроса, включая строку запроса. Это означает, что маркер доступа, которые могут быть включены в журналах. Просмотрите параметры ведения журнала веб сервера во избежание ведение журнала этой информации.

### <a name="exceptions"></a>Исключения

Сообщения об исключениях, как правило, являются конфиденциальных данных, которые не следует раскрывать для клиента. По умолчанию SignalR не отправляет сведения о исключения, вызванного метода концентратора клиенту. Вместо этого клиент получает универсальное сообщение, указывающее, что произошла ошибка. Это поведение можно переопределить, задав [ `EnableDetailedErrors` ](xref:signalr/configuration#configure-server-options) параметр.

### <a name="buffer-management"></a>Управление буферами

SignalR использует буферы подключения для управления входящих и исходящих сообщений. По умолчанию SignalR ограничивает эти буферы, до 32 КБ. Это означает, что наибольшее возможные сообщения, которое клиент или сервер может отправлять составляет 32 КБ. Это также означает, что максимальный объем памяти, занимаемой подключение для сообщений составляет 32 КБ. Если вы знаете, что сообщения всегда меньше, чем это ограничение, можно уменьшить этот размер, чтобы клиент не сможет отправить сообщение из большего размера и заставить сервер для выделения памяти, чтобы принять его. Аналогично Если известно, что сообщения превышают этот предел, может быть увеличен. Однако имейте в виду, что увеличение этого значения означает, что клиент может вызвать сервер должен выделить дополнительную память и может сократить число одновременных подключений, которые приложение сможет обработать.

Существуют отдельные ограничения для входящих и исходящих сообщений, как можно настроить на [ `HttpConnectionDispatcherOptions` ](xref:signalr/configuration#configure-server-options) объект, настроенный в `MapHub`:

* `ApplicationMaxBufferSize` Представляет максимальное число байтов от клиента, буферы сервера. Если клиент пытается отправить сообщение, размер которых превышает этот предел, соединение может быть закрыт.
* `TransportMaxBufferSize` Представляет максимальное число байтов, которые сервер может отправлять. Если сервер предпринимает попытку отправки сообщения (включая возвращаемые значения методов концентратора) превышает этот предел, будет вызвано исключение.

Если установленное `0` полностью отключает ограничение. Тем не менее это должен сделать очень осторожно. Удаление ограничения позволяет клиенту отправлять сообщения из любого размера. Это может использоваться со стороны вредоносного клиента заставить памяти для выделения, что может значительно снизить число одновременных подключений, которые ваше приложение может поддерживать.
