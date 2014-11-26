<properties title="Azure Storage の使用" pageTitle="Azure Storage の使用" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Getting Started (概要)][Getting Started (概要)]
> -   [変更内容][変更内容]

### <span id="whathappened">プロジェクトの変更点</span>

##### リファレンスの追加

Azure Storage NuGet パッケージが Visual Studio プロジェクトに追加されました。
このパッケージによって次の .NET リファレンスが追加されます。

-   `Microsoft.Data.Edm`
-   `Microsoft.Data.OData`
-   `Microsoft.Data.Services.Client`
-   `Microsoft.WindowsAzure.Configuration`
-   `Microsoft.WindowsAzure.Storage`
-   `Newtonsoft.Json`
-   `System.Data`
-   `System.Spatial`

##### Azure Storage の接続文字列の追加

選択されたストレージ アカウントの接続文字列とキーを使用して、プロジェクトの web.config ファイル内に要素が作成されました。

詳細については、「[ASP.NET][ASP.NET]」を参照してください。

  [Getting Started (概要)]: /documentation/articles/vs-storage-aspnet-getting-started-blobs/
  [変更内容]: /documentation/articles/vs-storage-aspnet-what-happened/
  [ASP.NET]: http://www.asp.net
