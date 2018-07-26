---
title: Azure Cosmos DB Table API .NET SDK とリソース | Microsoft Docs
description: リリース日、提供終了日、各バージョン間で行われた変更など、Azure Cosmos DB Table API に関するすべてについて説明します。
services: cosmos-db
author: rnagpal
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/26/2018
ms.author: rnagpal
ms.openlocfilehash: 2fba67b247ad0b53e11ca012969163a68013e82f
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126713"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: ダウンロードおよびリリース ノート
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK のダウンロード**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](https://aka.ms/acdbtableapiref)|
|**クイックスタート**|[Azure Cosmos DB: .NET と Table API を使用したアプリのビルド](create-table-dotnet.md)|
|**チュートリアル**|[Azure Cosmos DB: .NET での Table API を使用した開発](tutorial-develop-table-dotnet.md)|
|**現在サポートされているフレームワーク**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> プレビュー中にテーブル API アカウントを作成した場合、一般公開されたテーブル API SDK を使うには[新しいテーブル API アカウント](create-table-dotnet.md#create-a-database-account)を作成してください。
>

## <a name="release-notes"></a>リリース ノート

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3
* Microsoft.Azure.Storage.Common と Microsoft.Azure.DocumentDB の Nuget パッケージの依存関係が修正されました。
* JsonConvert.DefaultSettings が構成されたときにのテーブルのシリアル化のバグ修正があります。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 直接モードで無効な ETAG の検証が追加されました。
* ゲートウェイ モードで LINQ クエリ バグが修正されました。
* 同期 API が SynchronizationContext によってスレッド プールで実行されるようになりました。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* TableQueryMaxItemCount、TableQueryEnableScan、TableQueryMaxDegreeOfParallelism、および TableQueryContinuationTokenLimitInKb を TableRequestOptions に追加
* バグの修正

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 一般公開リリース

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0 - プレビュー
* 初期プレビュー リリース

## <a name="release-and-retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

[WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) プレビュー パッケージは非推奨となっており、[Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) パッケージに置き換えられます。 WindowsAzure.Storage PremiumTable SDK は、2018 年 11 月 15 日に提供終了されます。その時点で提供終了された SDK への要求は許可されなくなります。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了になった SDK を使用した Azure Cosmos DB への要求は、サービスによって拒否されます。
<br/>

| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.1.3](#1.1.3) |2018 年 7 月 17 日|--- |
| [1.1.1](#1.1.1) |2018 年 3 月 26 日|--- |
| [1.1.0](#1.1.0) |2018 年 2 月 21 日|--- |
| [1.0.0](#1.0.0) |2017 年 11 月 15 日|--- |
| [0.9.0 - プレビュー](#0.9.0-preview) |2017 年 11 月 11 日 |--- |

## <a name="troubleshooting"></a>トラブルシューティング

エラーが発生した場合 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Microsoft.Azure.CosmosDB.Table NuGet パッケージの使用を試している場合は、問題を解決する 2 つのオプションがあります。

* Package Manage Console を使用して Microsoft.Azure.CosmosDB.Table パッケージとその依存関係をインストールします。 これを行うには、ソリューションの Package Manager Console に以下を入力します。 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Nuget パッケージ管理ツールを使用して、Microsoft.Azure.CosmosDB.Table をインストールする前に、Microsoft.Azure.Storage.Common Nuget パッケージをインストールします。

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Azure Cosmos DB Table API の詳細については、「[Introduction to Azure Cosmos DB Table API (Azure Cosmos DB Table API の概要)](table-introduction.md)」を参照してください。 
