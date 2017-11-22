---
title: "Azure Content Delivery Network で Web コンテンツの有効期限を管理する | Microsoft Docs"
description: "Azure CDN で Azure Web Apps/Cloud Services、ASP.NET、または IIS コンテンツの有効期限を管理する方法について説明します。"
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Azure Content Delivery Network で Web コンテンツの有効期限を管理する
 Azure CDN
> [!div class="op_single_selector"]
> * [Azure Web Apps/Cloud Services、ASP.NET、または IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure BLOB Storage](cdn-manage-expiration-of-blob-content.md)
> 

パブリックにアクセス可能な任意の配信元 Web サーバーのファイルは、それらの有効期間 (TTL) が経過するまで、Azure Content Delivery Network (CDN) でキャッシュできます。 TTL は、配信元サーバーからの HTTP 応答の [`Cache-Control` ヘッダー](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)によって決まります。 この記事では、Microsoft Azure App Service、Azure Cloud Services、ASP.NET アプリケーション、およびインターネット インフォメーション サービス サイトでの Web Apps 機能の `Cache-Control` ヘッダーを設定する方法について説明します。これらはすべて同様に構成されます。

> [!TIP]
> ファイルに TTL を設定しないことも選択できます。 その場合は、Azure CDN が既定の 7 日間の TTL を自動的に適用します。
> 
> ファイルとその他のリソースへのアクセスを高速化する Azure CDN のしくみについて詳しくは、「[Azure Content Delivery Network (CDN) の概要](cdn-overview.md)」をご覧ください。
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>構成ファイルでの Cache-Control ヘッダーの設定
画像やスタイル シートなどの静的コンテンツの場合は、Web アプリケーションの **applicationHost.config** ファイルか **web.config** ファイルを変更することで、更新頻度を制御できます。 構成ファイル内の **system.webServer\staticContent\clientCache** 要素が、コンテンツの `Cache-Control` ヘッダーを設定します。 **web.config** ファイルの構成設定は、サブフォルダー レベルでオーバーライドされない限り、フォルダーとそのすべてのサブフォルダー内にあるすべてのものに影響を与えます。 たとえば、ルート フォルダーにはすべての静的コンテンツを 3 日間キャッシュする既定の TTL を設定し、コンテンツが変わりやすいサブフォルダーにはコンテンツを 6 時間だけキャッシュする TTL を設定できます。 **applicationHost.config** ファイルの設定は、サイト上のすべてのアプリケーションに影響を与えますが、アプリケーション内の既存の **web.config** ファイルの設定によってオーバーライドされます。

次の XML の例は、3 日間の最大有効期間を指定するように **clientCache** を設定する方法を示しています。  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

**UseMaxAge** を指定すると、`Cache-Control: max-age=<nnn>` ヘッダーが **CacheControlMaxAge** 属性に指定された値に基づいて応答に追加されます。 **cacheControlMaxAge** 属性の期間の形式は、`<days>.<hours>:<min>:<sec>` です。 **clientCache** ノードの詳細については、[クライアント キャッシュ<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)に関するページをご覧ください。  

## <a name="setting-cache-control-headers-in-code"></a>コードでの Cache-Control ヘッダーの設定
ASP.NET アプリケーションでは、**HttpResponse.Cache** プロパティを設定することによって、CDN のキャッシュ動作をプログラムで制御できます。 **HttpResponse.Cache** プロパティの詳細については、[HttpResponse.Cache プロパティ](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)に関するページと [HttpCachePolicy クラス](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)に関するページをご覧ください。  

アプリケーションのコンテンツを ASP.NET でプログラムでキャッシュするには、次の手順に従います。
   1. `HttpCacheability` を *Public* に設定することで、コンテンツをキャッシュ可能とマークします。 
   2. 次のいずれかのメソッドを呼び出して、キャッシュ検証を設定します。
      - `SetLastModified` を呼び出して、LastModified タイムスタンプを設定します。
      - `SetETag` を呼び出して、`ETag` 値を設定します。
   3. 必要に応じて、`SetExpires` を呼び出して、キャッシュの有効期間を指定します。 それ以外の場合は、このドキュメントで既に説明した既定のキャッシュのヒューリスティックが適用されます。

たとえば、コンテンツを 1 時間キャッシュするには、次の C# コードを追加します。  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>次のステップ
* [**clientCache** 要素の詳細を確認する](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [**HttpResponse.Cache** プロパティのドキュメントを参照する](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [**HttpCachePolicy クラス**のドキュメントを参照する](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

