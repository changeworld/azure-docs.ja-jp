---
title: "DocumentDB .NET Core API と SDK | Microsoft Docs"
description: "リリース日、提供終了日、DocumentDB .NET Core SDK の各バージョン間の変更など、.NET Core API と SDK に関するあらゆる詳細を提供します。"
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/23/2016
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: 8222574f5cb4c79390fa68f61bcda9f943384c49
ms.openlocfilehash: 1f18b90df96d91f8897c6b5570341a3c3459594a


---
# <a name="documentdb-apis-and-sdks"></a>DocumentDB API と SDK
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST ()](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-net-core-api-and-sdk"></a>DocumentDB .NET Core API と SDK
<table>

<tr><td>**SDK のダウンロード**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API ドキュメント**</td><td>[.NET API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**サンプル**</td><td>[.NET コード サンプル](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**作業開始**</td><td>[DocumentDB .NET Core SDK の使用](documentdb-dotnetcore-get-started.md)</td></tr>

<tr><td>**Web アプリ チュートリアル**</td><td>[DocumentDB による Web アプリケーション開発](documentdb-dotnet-application.md)</td></tr>

<tr><td>**現在サポートされているフレームワーク**</td><td>[.NET Standard 1.6](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

### <a name="a-name010-preview010-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentdbcore010-preview"></a><a name="0.1.0-preview"/>[0.1.0-preview](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/0.1.0-preview)

DocumentDB .NET Core Preview SDK を使用すると、Windows、Mac、Linux で実行できるクロスプラットフォーム対応の高速な [ASP.NET Core](https://www.asp.net/core) アプリと [.NET Core](https://www.microsoft.com/net/core#windows) アプリを構築できるようになります。

DocumentDB .NET Core Preview SDK には最新バージョンの [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) と同等の機能が備わっており、次の機能がサポートされます。
* すべての[接続モード](documentdb-performance-tips.md#networking): ゲートウェイ モード、ダイレクト TCP、ダイレクト HTTP。 
* すべての[一貫性レベル](documentdb-consistency-levels.md): 強固、セッション、有界整合性制約、最終的。
* [パーティション分割コレクション](documentdb-partition-data.md)。 
* [複数リージョンのデータベース アカウントと geo レプリケーション](documentdb-distribute-data-globally.md)。

この SDK に関する質問がある場合は、[StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) か [MSDN フォーラム](http://go.microsoft.com/fwlink/?LinkId=631655)に投稿してください。または、[askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) に電子メールでお問い合わせください。 [GitHub リポジトリ](https://github.com/Azure/azure-documentdb-dotnet/issues)で問題を報告することもできます。 

## <a name="release--retirement-dates"></a>リリース日と提供終了日

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [0.1.0-preview](#0.1.0-preview) |2016 年 11 月 15 日 |--- |

## <a name="see-also"></a>関連項目
DocumentDB に関する詳細は、 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) サービス ページを参照してください。 




<!--HONumber=Nov16_HO4-->


