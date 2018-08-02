---
title: Размещение ASP.NET Core в операционной системе Linux с Apache
description: Процедура настройки Apache в качестве обратного прокси-сервера в CentOS для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое в Kestrel.
author: spboyer
ms.author: spboyer
ms.custom: mvc
ms.date: 03/13/2018
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 2431e989d6fc2cf83bca47aaa41a2bf686c0ab54
ms.sourcegitcommit: 8f8924ce4eb9effeaf489f177fb01b66867da16f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39219359"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="82adb-103">Размещение ASP.NET Core в операционной системе Linux с Apache</span><span class="sxs-lookup"><span data-stu-id="82adb-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="82adb-104">Автор: [Шейн Бойер (Shayne Boyer)](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="82adb-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="82adb-105">Из этого руководства вы узнаете, как настроить [Apache](https://httpd.apache.org/) в качестве обратного прокси-сервера в [CentOS 7](https://www.centos.org/) для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое в [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="82adb-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="82adb-106">[Расширение mod_proxy](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) и связанные с ним модули создают обратный прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="82adb-106">The [mod_proxy extension](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="82adb-107">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="82adb-107">Prerequisites</span></span>

1. <span data-ttu-id="82adb-108">Сервер под управлением CentOS 7 и учетная запись обычного пользователя с правами sudo.</span><span class="sxs-lookup"><span data-stu-id="82adb-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="82adb-109">Установите среду выполнения .NET Core на сервере.</span><span class="sxs-lookup"><span data-stu-id="82adb-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="82adb-110">Перейдите на [страницу всех загрузок .NET Core](https://www.microsoft.com/net/download/all).</span><span class="sxs-lookup"><span data-stu-id="82adb-110">Visit the [.NET Core All Downloads page](https://www.microsoft.com/net/download/all).</span></span>
   1. <span data-ttu-id="82adb-111">Выберите последнюю не предварительную версию среды выполнения из списка под заголовком **Среда выполнения**.</span><span class="sxs-lookup"><span data-stu-id="82adb-111">Select the latest non-preview runtime from the list under **Runtime**.</span></span>
   1. <span data-ttu-id="82adb-112">Сделайте выбор и следуйте инструкциям для CentOS/Oracle.</span><span class="sxs-lookup"><span data-stu-id="82adb-112">Select and follow the instructions for CentOS/Oracle.</span></span>
1. <span data-ttu-id="82adb-113">Существующее приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82adb-113">An existing ASP.NET Core app.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="82adb-114">Публикация и копирование приложения</span><span class="sxs-lookup"><span data-stu-id="82adb-114">Publish and copy over the app</span></span>

<span data-ttu-id="82adb-115">Настройте приложение, чтобы [его развертывание зависело от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="82adb-115">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="82adb-116">Запустите [dotnet publish](/dotnet/core/tools/dotnet-publish) в среде разработки, чтобы упаковать приложение в каталог (например, *bin/Release/&lt;target_framework_moniker&gt;/publish*), который может выполняться на сервере:</span><span class="sxs-lookup"><span data-stu-id="82adb-116">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="82adb-117">Приложение может быть опубликовано как [автономное развертывание](/dotnet/core/deploying/#self-contained-deployments-scd), если вы предпочитаете не сохранять среду выполнения .NET Core на сервере.</span><span class="sxs-lookup"><span data-stu-id="82adb-117">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="82adb-118">Скопируйте приложение ASP.NET Core на сервер с помощью инструмента, интегрированного в ваш рабочий процесс (например, SCP или SFTP).</span><span class="sxs-lookup"><span data-stu-id="82adb-118">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="82adb-119">Обычно веб-приложения находятся в каталоге *var* (например, *var/aspnetcore/hellomvc*).</span><span class="sxs-lookup"><span data-stu-id="82adb-119">It's common to locate web apps under the *var* directory (for example, *var/aspnetcore/hellomvc*).</span></span>

> [!NOTE]
> <span data-ttu-id="82adb-120">Если развертывание выполняется в рабочей среде, рабочий процесс непрерывной интеграции автоматически опубликует приложение и скопирует его ресурсы на сервер.</span><span class="sxs-lookup"><span data-stu-id="82adb-120">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="82adb-121">Настройка прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="82adb-121">Configure a proxy server</span></span>

<span data-ttu-id="82adb-122">Обратный прокси-сервер — это стандартный вариант настройки для обслуживания динамических веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="82adb-122">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="82adb-123">Обратный прокси-сервер завершает HTTP-запрос и перенаправляет его в приложение ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="82adb-123">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="82adb-124">Прокси-сервер перенаправляет запросы клиента на другой сервер, а не обрабатывает их самостоятельно.</span><span class="sxs-lookup"><span data-stu-id="82adb-124">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="82adb-125">Обратный прокси-сервер перенаправляет запросы в фиксированное назначение обычно от имени клиентов.</span><span class="sxs-lookup"><span data-stu-id="82adb-125">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="82adb-126">При работе с этим руководством мы настроим Apache в качестве обратного прокси-сервера, который работает на том же сервере, где Kestrel предоставляет приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82adb-126">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="82adb-127">Так как запросы перенаправляются обратным прокси-сервером, используйте [ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer), которое входит в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/).</span><span class="sxs-lookup"><span data-stu-id="82adb-127">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="82adb-128">Это ПО обновляет `Request.Scheme`, используя заголовок `X-Forwarded-Proto`, что обеспечивает правильную работу URI перенаправления и других политик безопасности.</span><span class="sxs-lookup"><span data-stu-id="82adb-128">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="82adb-129">Любой компонент, который зависит от схемы, например проверка подлинности, генерация ссылок, перенаправление и геолокация, должен находиться после вызова ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="82adb-129">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="82adb-130">Как правило, ПО промежуточного слоя перенаправления заголовков должно выполняться до остального ПО промежуточного слоя, за исключением ПО промежуточного слоя для диагностики и обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="82adb-130">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="82adb-131">Такой порядок гарантирует, что ПО промежуточного слоя, полагающееся на сведения о перенаправленных заголовках, может использовать значения заголовков для обработки.</span><span class="sxs-lookup"><span data-stu-id="82adb-131">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

::: moniker range=">= aspnetcore-2.0"
> [!NOTE]
> <span data-ttu-id="82adb-132">Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является допустимой и поддерживаемой для размещения основных компонентов приложений ASP.NET версии 2.0 и выше.</span><span class="sxs-lookup"><span data-stu-id="82adb-132">Either configuration&mdash;with or without a reverse proxy server&mdash;is a valid and supported hosting configuration for ASP.NET Core 2.0 or later apps.</span></span> <span data-ttu-id="82adb-133">Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="82adb-133">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="82adb-134">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="82adb-134">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="82adb-135">Вызовите метод [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) в `Startup.Configure`, прежде чем вызвать [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication) или другое ПО промежуточного слоя для проверки подлинности по аналогичной схеме.</span><span class="sxs-lookup"><span data-stu-id="82adb-135">Invoke the [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) method in `Startup.Configure` before calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication) or similar authentication scheme middleware.</span></span> <span data-ttu-id="82adb-136">В ПО промежуточного слоя настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="82adb-136">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="82adb-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="82adb-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="82adb-138">Вызывайте метод [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) в `Startup.Configure`, прежде чем вызвать [UseIdentity](/dotnet/api/microsoft.aspnetcore.builder.builderextensions.useidentity), [UseFacebookAuthentication](/dotnet/api/microsoft.aspnetcore.builder.facebookappbuilderextensions.usefacebookauthentication) или другое ПО промежуточного слоя для проверки подлинности по аналогичной схеме.</span><span class="sxs-lookup"><span data-stu-id="82adb-138">Invoke the [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) method in `Startup.Configure` before calling [UseIdentity](/dotnet/api/microsoft.aspnetcore.builder.builderextensions.useidentity) and [UseFacebookAuthentication](/dotnet/api/microsoft.aspnetcore.builder.facebookappbuilderextensions.usefacebookauthentication) or similar authentication scheme middleware.</span></span> <span data-ttu-id="82adb-139">В ПО промежуточного слоя настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="82adb-139">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

<span data-ttu-id="82adb-140">Если параметр [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) не задан для ПО промежуточного слоя, по умолчанию перенаправляются заголовки `None`.</span><span class="sxs-lookup"><span data-stu-id="82adb-140">If no [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="82adb-141">Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="82adb-141">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="82adb-142">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="82adb-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="install-apache"></a><span data-ttu-id="82adb-143">Установка Apache</span><span class="sxs-lookup"><span data-stu-id="82adb-143">Install Apache</span></span>

<span data-ttu-id="82adb-144">Обновите пакеты CentOS до последних стабильных версий:</span><span class="sxs-lookup"><span data-stu-id="82adb-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="82adb-145">Установите веб-сервер Apache на CentOS, выполнив одну команду `yum`:</span><span class="sxs-lookup"><span data-stu-id="82adb-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="82adb-146">Пример выходных данных этой команды:</span><span class="sxs-lookup"><span data-stu-id="82adb-146">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="82adb-147">В нашем примере выходные данные содержат строку httpd.86_64, так как используется 64-разрядная версия CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="82adb-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="82adb-148">Чтобы проверить, где установлен Apache, выполните `whereis httpd` из командной строки.</span><span class="sxs-lookup"><span data-stu-id="82adb-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="82adb-149">Настройка Apache</span><span class="sxs-lookup"><span data-stu-id="82adb-149">Configure Apache</span></span>

<span data-ttu-id="82adb-150">Файлы конфигурации для Apache находятся в каталоге `/etc/httpd/conf.d/`.</span><span class="sxs-lookup"><span data-stu-id="82adb-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="82adb-151">В алфавитном порядке обрабатываются все файлы с расширением *.conf*, а также файлы конфигурации модуля из папки `/etc/httpd/conf.modules.d/`, где содержатся файлы конфигурации, необходимые для загрузки модулей.</span><span class="sxs-lookup"><span data-stu-id="82adb-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="82adb-152">Создайте для приложения файл конфигурации с именем *hellomvc.conf*:</span><span class="sxs-lookup"><span data-stu-id="82adb-152">Create a configuration file, named *hellomvc.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}hellomvc-error.log
    CustomLog ${APACHE_LOG_DIR}hellomvc-access.log common
</VirtualHost>
```

<span data-ttu-id="82adb-153">Блок `VirtualHost` может встречаться несколько раз в одном или нескольких файлах на сервере.</span><span class="sxs-lookup"><span data-stu-id="82adb-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="82adb-154">С представленным выше файлом конфигурации Apache принимает трафик от любого источника через порт 80.</span><span class="sxs-lookup"><span data-stu-id="82adb-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="82adb-155">Он обслуживает домен `www.example.com`, а псевдоним `*.example.com` указывает на тот же веб-сайт.</span><span class="sxs-lookup"><span data-stu-id="82adb-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="82adb-156">Дополнительные сведения см. в статье [о поддержке виртуальных узлов на основе имен](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span><span class="sxs-lookup"><span data-stu-id="82adb-156">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="82adb-157">Запросы к корневому каталогу перенаправляются на порт 5000 того же сервера по адресу 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="82adb-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="82adb-158">Для двусторонней связи требуются `ProxyPass` и `ProxyPassReverse`.</span><span class="sxs-lookup"><span data-stu-id="82adb-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="82adb-159">Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="82adb-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="82adb-160">Если не удастся указать правильную [директиву ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) в блоке **VirtualHost**, приложение будет иметь значительные уязвимости.</span><span class="sxs-lookup"><span data-stu-id="82adb-160">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="82adb-161">Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.example.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость).</span><span class="sxs-lookup"><span data-stu-id="82adb-161">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="82adb-162">Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="82adb-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="82adb-163">Ведение журнала можно настроить отдельно для каждого `VirtualHost` с помощью директив `ErrorLog` и `CustomLog`.</span><span class="sxs-lookup"><span data-stu-id="82adb-163">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="82adb-164">Журналы ошибок сохраняются в расположение `ErrorLog`, а параметр `CustomLog` задает имя и формат для файла журнала.</span><span class="sxs-lookup"><span data-stu-id="82adb-164">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="82adb-165">В нашем примере здесь фиксируются сведения о запросах.</span><span class="sxs-lookup"><span data-stu-id="82adb-165">In this case, this is where request information is logged.</span></span> <span data-ttu-id="82adb-166">Для каждого запроса создается одна строка.</span><span class="sxs-lookup"><span data-stu-id="82adb-166">There's one line for each request.</span></span>

<span data-ttu-id="82adb-167">Сохраните файл и протестируйте конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="82adb-167">Save the file and test the configuration.</span></span> <span data-ttu-id="82adb-168">Если проверка выполнена успешно, ответ должен быть `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="82adb-168">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="82adb-169">Перезапустите Apache.</span><span class="sxs-lookup"><span data-stu-id="82adb-169">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitoring-the-app"></a><span data-ttu-id="82adb-170">Мониторинг приложения</span><span class="sxs-lookup"><span data-stu-id="82adb-170">Monitoring the app</span></span>

<span data-ttu-id="82adb-171">Теперь Apache настроен на перенаправление запросов к `http://localhost:80` в приложение ASP.NET Core, выполняемое в Kestrel по адресу `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="82adb-171">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span>  <span data-ttu-id="82adb-172">Но Apache не настроен для управления процессом Kestrel.</span><span class="sxs-lookup"><span data-stu-id="82adb-172">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="82adb-173">Для запуска и мониторинга базового веб-приложения используйте *systemd* и создайте файл службы.</span><span class="sxs-lookup"><span data-stu-id="82adb-173">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="82adb-174">*systemd* — это система инициализации, предоставляющая различные функции для запуска и остановки процессов, а также управления ими.</span><span class="sxs-lookup"><span data-stu-id="82adb-174">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="82adb-175">Создание файла службы</span><span class="sxs-lookup"><span data-stu-id="82adb-175">Create the service file</span></span>

<span data-ttu-id="82adb-176">Создайте файл определения службы.</span><span class="sxs-lookup"><span data-stu-id="82adb-176">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

<span data-ttu-id="82adb-177">Пример файла службы для приложения:</span><span class="sxs-lookup"><span data-stu-id="82adb-177">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

> [!NOTE]
> <span data-ttu-id="82adb-178">**Пользователь.** Если пользователь *apache* в вашей конфигурации еще не используется, его необходимо создать и правильно предоставить ему права владения для соответствующих файлов.</span><span class="sxs-lookup"><span data-stu-id="82adb-178">**User** &mdash; If the user *apache* isn't used by the configuration, the user must be created first and given proper ownership for files.</span></span>

> [!NOTE]
> <span data-ttu-id="82adb-179">Некоторые значения (например, строки подключения SQL) необходимо экранировать, чтобы поставщики конфигурации могли считать переменные среды.</span><span class="sxs-lookup"><span data-stu-id="82adb-179">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="82adb-180">Используйте следующую команду, чтобы создать правильно экранированное значение для файла конфигурации:</span><span class="sxs-lookup"><span data-stu-id="82adb-180">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>
>
> ```console
> systemd-escape "<value-to-escape>"
> ```

<span data-ttu-id="82adb-181">Сохраните файл и включите службу.</span><span class="sxs-lookup"><span data-stu-id="82adb-181">Save the file and enable the service:</span></span>

```bash
systemctl enable kestrel-hellomvc.service
```

<span data-ttu-id="82adb-182">Запустите службу и убедитесь, что она работает.</span><span class="sxs-lookup"><span data-stu-id="82adb-182">Start the service and verify that it's running:</span></span>

```bash
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

<span data-ttu-id="82adb-183">Теперь, когда обратный прокси-сервер настроен и *systemd* управляет процессом Kestrel, веб-приложение можно считать полностью настроенным и вы можете обратиться к нему по адресу `http://localhost` из браузера на локальном компьютере.</span><span class="sxs-lookup"><span data-stu-id="82adb-183">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="82adb-184">Заголовок **Server** в ответе подтверждает, что приложение ASP.NET Core обслуживается Kestrel.</span><span class="sxs-lookup"><span data-stu-id="82adb-184">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a><span data-ttu-id="82adb-185">Просмотр журналов</span><span class="sxs-lookup"><span data-stu-id="82adb-185">Viewing logs</span></span>

<span data-ttu-id="82adb-186">Так как веб-приложением, использующим Kestrel, управляет *systemd*, все события и процессы регистрируются в централизованном журнале.</span><span class="sxs-lookup"><span data-stu-id="82adb-186">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="82adb-187">Но этот журнал содержит все записи обо всех службах и процессах, управляемых *systemd*.</span><span class="sxs-lookup"><span data-stu-id="82adb-187">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="82adb-188">Чтобы просмотреть элементы, связанные с `kestrel-hellomvc.service`, используйте следующую команду.</span><span class="sxs-lookup"><span data-stu-id="82adb-188">To view the `kestrel-hellomvc.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

<span data-ttu-id="82adb-189">Чтобы отфильтровать элементы по времени, укажите в команде параметры времени.</span><span class="sxs-lookup"><span data-stu-id="82adb-189">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="82adb-190">Например, `--since today` позволяет отфильтровать данные за текущий день, а `--until 1 hour ago` — просмотреть записи за предыдущий час.</span><span class="sxs-lookup"><span data-stu-id="82adb-190">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="82adb-191">Дополнительные сведения см. на странице руководства о команде [journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="82adb-191">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="82adb-192">Защита данных</span><span class="sxs-lookup"><span data-stu-id="82adb-192">Data protection</span></span>

<span data-ttu-id="82adb-193">[Стек защиты данных в ASP.NET Core](xref:security/data-protection/index) используется определенным [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая промежуточное ПО для проверки подлинности (например, промежуточное ПО файлов cookie) и средствами защиты от подделки межсайтовых запросов.</span><span class="sxs-lookup"><span data-stu-id="82adb-193">The [ASP.NET Core Data Protection stack](xref:security/data-protection/index) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="82adb-194">Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, необходимо настроить защиту данных для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="82adb-194">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="82adb-195">Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="82adb-195">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="82adb-196">Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="82adb-196">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="82adb-197">Все токены аутентификации, использующие файлы cookie, становятся недействительными.</span><span class="sxs-lookup"><span data-stu-id="82adb-197">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="82adb-198">При выполнении следующего запроса пользователю требуется выполнить вход снова.</span><span class="sxs-lookup"><span data-stu-id="82adb-198">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="82adb-199">Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="82adb-199">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="82adb-200">Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="82adb-200">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="82adb-201">Сведения о настройке защиты данных для хранения и шифрования набора ключей см. в приведенных ниже статьях.</span><span class="sxs-lookup"><span data-stu-id="82adb-201">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="securing-the-app"></a><span data-ttu-id="82adb-202">Защита приложения</span><span class="sxs-lookup"><span data-stu-id="82adb-202">Securing the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="82adb-203">Настройка брандмауэра</span><span class="sxs-lookup"><span data-stu-id="82adb-203">Configure firewall</span></span>

<span data-ttu-id="82adb-204">*Firewalld* — это динамическая управляющая программа брандмауэра, которая поддерживает зоны сети.</span><span class="sxs-lookup"><span data-stu-id="82adb-204">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="82adb-205">Фильтрацию портов и пакетов можно по-прежнему осуществлять через iptables.</span><span class="sxs-lookup"><span data-stu-id="82adb-205">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="82adb-206">*Firewalld* обычно устанавливается в системе по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="82adb-206">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="82adb-207">`yum` позволяет установить этот пакет или убедиться, что он установлен.</span><span class="sxs-lookup"><span data-stu-id="82adb-207">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="82adb-208">С помощью `firewalld` вы можете открыть только те порты, которые необходимые для работы приложения.</span><span class="sxs-lookup"><span data-stu-id="82adb-208">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="82adb-209">В этом случае используются порты 80 и 443.</span><span class="sxs-lookup"><span data-stu-id="82adb-209">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="82adb-210">Следующие команды назначают порты 80 и 443 постоянно открытыми.</span><span class="sxs-lookup"><span data-stu-id="82adb-210">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="82adb-211">Обновите параметры брандмауэра.</span><span class="sxs-lookup"><span data-stu-id="82adb-211">Reload the firewall settings.</span></span> <span data-ttu-id="82adb-212">Проверьте, что доступные службы и порты находятся в зоне по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="82adb-212">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="82adb-213">Эти параметры можно просмотреть с помощью `firewall-cmd -h`.</span><span class="sxs-lookup"><span data-stu-id="82adb-213">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="ssl-configuration"></a><span data-ttu-id="82adb-214">Конфигурация SSL</span><span class="sxs-lookup"><span data-stu-id="82adb-214">SSL configuration</span></span>

<span data-ttu-id="82adb-215">Apache для SSL настраивается с помощью модуля *mod_ssl*.</span><span class="sxs-lookup"><span data-stu-id="82adb-215">To configure Apache for SSL, the *mod_ssl* module is used.</span></span> <span data-ttu-id="82adb-216">При установке модуля *httpd* автоматически добавляется и модуль *mod_ssl*.</span><span class="sxs-lookup"><span data-stu-id="82adb-216">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="82adb-217">Если он по каким-то причинам отсутствует, добавьте его в систему с помощью `yum`.</span><span class="sxs-lookup"><span data-stu-id="82adb-217">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="82adb-218">Чтобы принудительно использовать SSL, установите модуль `mod_rewrite` для перезаписи URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="82adb-218">To enforce SSL, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="82adb-219">Измените файл *hellomvc.conf*, чтобы разрешить перезапись URL-адресов и безопасный обмен данными через порт 443.</span><span class="sxs-lookup"><span data-stu-id="82adb-219">Modify the *hellomvc.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="82adb-220">В этом примере используется локально созданный сертификат.</span><span class="sxs-lookup"><span data-stu-id="82adb-220">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="82adb-221">В **SSLCertificateFile** должен быть указан основной файл сертификата для доменного имени.</span><span class="sxs-lookup"><span data-stu-id="82adb-221">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="82adb-222">В **SSLCertificateKeyFile** должен быть указан файл ключа, сформированный при создании CSR.</span><span class="sxs-lookup"><span data-stu-id="82adb-222">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="82adb-223">В **SSLCertificateChainFile** должен быть указан файл промежуточного сертификата (если он существует), предоставленный центром сертификации.</span><span class="sxs-lookup"><span data-stu-id="82adb-223">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="82adb-224">Сохраните файл и протестируйте конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="82adb-224">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="82adb-225">Перезапустите Apache.</span><span class="sxs-lookup"><span data-stu-id="82adb-225">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="82adb-226">Дополнительные предложения Apache</span><span class="sxs-lookup"><span data-stu-id="82adb-226">Additional Apache suggestions</span></span>

### <a name="additional-headers"></a><span data-ttu-id="82adb-227">Дополнительные заголовки</span><span class="sxs-lookup"><span data-stu-id="82adb-227">Additional headers</span></span>

<span data-ttu-id="82adb-228">Для защиты от вредоносных атак следует изменить или добавить несколько заголовков.</span><span class="sxs-lookup"><span data-stu-id="82adb-228">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="82adb-229">Убедитесь, что модуль `mod_headers` установлен.</span><span class="sxs-lookup"><span data-stu-id="82adb-229">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="82adb-230">Защита Apache от атак кликджекинга</span><span class="sxs-lookup"><span data-stu-id="82adb-230">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="82adb-231">[Кликджекинг](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger) (или *атака с подменой пользовательского интерфейса*) является вредоносной атакой, при которой посетителя сайта обманным путем вынуждают щелкнуть ссылку или нажать кнопку не той страницы, на которой он находится.</span><span class="sxs-lookup"><span data-stu-id="82adb-231">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="82adb-232">Используйте `X-FRAME-OPTIONS` для защиты сайта.</span><span class="sxs-lookup"><span data-stu-id="82adb-232">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="82adb-233">Измените файл *httpd.conf*.</span><span class="sxs-lookup"><span data-stu-id="82adb-233">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="82adb-234">Добавьте строку `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="82adb-234">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span> <span data-ttu-id="82adb-235">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="82adb-235">Save the file.</span></span> <span data-ttu-id="82adb-236">Перезапустите Apache.</span><span class="sxs-lookup"><span data-stu-id="82adb-236">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="82adb-237">Сканирование типа MIME</span><span class="sxs-lookup"><span data-stu-id="82adb-237">MIME-type sniffing</span></span>

<span data-ttu-id="82adb-238">Заголовок `X-Content-Type-Options` защищает Internet Explorer от *сканирования MIME* (определения `Content-Type` для файла по его содержимому).</span><span class="sxs-lookup"><span data-stu-id="82adb-238">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="82adb-239">Если сервер задает заголовок `Content-Type` со значением `text/html`, и при этом установлен параметр `nosniff`, Internet Explorer отображает содержимое как `text/html` независимо от содержимого файла.</span><span class="sxs-lookup"><span data-stu-id="82adb-239">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="82adb-240">Измените файл *httpd.conf*.</span><span class="sxs-lookup"><span data-stu-id="82adb-240">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="82adb-241">Добавьте строку `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="82adb-241">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="82adb-242">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="82adb-242">Save the file.</span></span> <span data-ttu-id="82adb-243">Перезапустите Apache.</span><span class="sxs-lookup"><span data-stu-id="82adb-243">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="82adb-244">Балансировка нагрузки</span><span class="sxs-lookup"><span data-stu-id="82adb-244">Load Balancing</span></span>

<span data-ttu-id="82adb-245">В этом примере показано, как установить и настроить Apache в CentOS 7 и Kestrel на том же компьютере.</span><span class="sxs-lookup"><span data-stu-id="82adb-245">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="82adb-246">Чтобы устранить единую точку отказа, воспользуйтесь *mod_proxy_balancer* и измените значение **VirtualHost** для управления несколькими экземплярами веб-приложений за прокси-сервером Apache.</span><span class="sxs-lookup"><span data-stu-id="82adb-246">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="82adb-247">В представленном ниже файле конфигурации настроен дополнительный экземпляр приложения `hellomvc`, работающий на порту 5001.</span><span class="sxs-lookup"><span data-stu-id="82adb-247">In the configuration file shown below, an additional instance of the `hellomvc` app is setup to run on port 5001.</span></span> <span data-ttu-id="82adb-248">В разделе *Proxy* настраивается конфигурация подсистемы балансировки нагрузки с двумя членами для распределения нагрузки методом *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="82adb-248">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="82adb-249">Ограничения скорости</span><span class="sxs-lookup"><span data-stu-id="82adb-249">Rate Limits</span></span>

<span data-ttu-id="82adb-250">С помощью элемента *mod_ratelimit*, который входит в модуль *httpd*, можно ограничить пропускную способность для клиентов:</span><span class="sxs-lookup"><span data-stu-id="82adb-250">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```
<span data-ttu-id="82adb-251">В примере файла пропускная способность составляет 600 Кбит/с в корневой папке.</span><span class="sxs-lookup"><span data-stu-id="82adb-251">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

## <a name="additional-resources"></a><span data-ttu-id="82adb-252">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="82adb-252">Additional resources</span></span>

* [<span data-ttu-id="82adb-253">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="82adb-253">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)
