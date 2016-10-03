<properties
 pageTitle="Azure CDN で Azure Web Apps/Cloud Services、ASP.NET、IIS コンテンツの有効期限を管理する方法 | Microsoft Azure"
 description="Azure CDN でクラウド サービスのコンテンツの有効期限を管理する方法について説明します"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# Azure CDN で Azure Web Apps/Cloud Services、ASP.NET、IIS コンテンツの有効期限を管理する方法

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services、ASP.NET、または IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure Storage BLOB サービス](cdn-manage-expiration-of-blob-content.md)

パブリックにアクセス可能な任意の配信元 Web サーバーのファイルは、その有効期間 (TTL) が経過するまで、Azure CDN でキャッシュできます。TTL は、配信元サーバーからの HTTP 応答の [*Cache-Control* ヘッダー](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)によって決まります。この記事では、Azure Web Apps、Azure Cloud Services、ASP.NET アプリケーション、インターネット インフォメーション サービス サイトの `Cache-Control` ヘッダーの設定方法について説明します。これらはすべて同様に構成されます。

>[AZURE.TIP] ファイルに TTL を設定しなくてもかまいません。その場合は、Azure CDN が既定の 7 日間の TTL を自動的に適用します。
>
>ファイルとその他のリソースへのアクセスを高速化する Azure CDN のしくみの詳細については、[Azure CDN の概要](./cdn-overview.md)に関するページを参照してください。

## 構成での Cache-Control ヘッダーの設定

画像やスタイル シートなどの静的コンテンツの場合は、Web アプリケーションの **applicationHost.config** ファイルか **web.config** ファイルを変更することで、更新頻度を制御できます。構成ファイル内の **system.webServer\\staticContent\\clientCache** 要素が、コンテンツの `Cache-Control` ヘッダーを設定します。**web.config** の構成設定は、サブフォルダー レベルでオーバーライドされていない限り、フォルダーとすべてのサブフォルダー内にあるすべてのものに影響を与えます。たとえば、静的なコンテンツはすべて 3 日間キャッシュされるが、可変コンテンツが含まれているサブフォルダーではキャッシュの設定が 6 時間になるように、ルートに対して既定の有効期限を設定できます。**applicationHost.config** の場合、サイト上のすべてのアプリケーションが影響を受けますが、アプリケーション内の **web.config** ファイルでオーバーライドすることができます。

次の XML は、3 日間の最大有効期間を指定するように **clientCache** を設定する例を示します。

```xml
<configuration>
	<system.webServer>
		<staticContent>
			<clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
		</staticContent>
	</system.webServer>
</configuration>
```

**UseMaxAge** を指定して、`Cache-Control: max-age=<nnn>` ヘッダーを **CacheControlMaxAge** 属性に指定された値に基づいて応答に追加します。**cacheControlMaxAge** 属性の期間の形式は、<days>.<hours>:<min>:<sec> です。**clientCache** ノードの詳細については、[クライアント キャッシュ <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache) のリファレンス ページを参照してください。

## コードでの Cache-Control ヘッダーの設定

ASP.NET アプリケーションの場合、**HttpResponse.Cache** プロパティを設定することにより、CDN のキャッシュ動作をプログラムで設定できます。**HttpResponse.Cache** プロパティの詳細については、[HttpResponse.Cache プロパティ](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)に関するページと [HttpCachePolicy クラス](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)に関するページを参照してください。

ASP.NET のアプリケーション コンテンツをプログラムでキャッシュする場合は、HttpCacheability を *Public* に設定することにより、コンテンツをキャッシュ可能としてマークします。また、キャッシュ検証が設定されていることを確認します。キャッシュ検証では、SetLastModified を呼び出して最終更新のタイムスタンプを設定することや、SetETag を呼び出して etag 値を設定することができます。必要に応じて、SetExpires を呼び出してキャッシュの有効期限を指定できます。また、このドキュメントで既に説明した既定のキャッシュのヒューリスティックに依存することもできます。

たとえば、1 時間のコンテンツをキャッシュするには、次を追加します。

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## 次のステップ

- [**clientCache** 要素の詳細を確認する](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [**HttpResponse.Cache** プロパティのドキュメントを参照する](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)
- [**HttpCachePolicy クラス**のドキュメントを参照する](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)

<!---HONumber=AcomDC_0921_2016-->