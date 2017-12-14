---
title: "Azure CosmosDB Graph API .NET SDK とリソース | Microsoft Docs"
description: "リリース日、提供終了日、各バージョン間で行われた変更など、Azure CosmosDB Graph API に関するあらゆる情報を提供します。"
services: cosmos-db
documentationcenter: .net
author: luisbosquez
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: 28f926d8d1f4c6006a348ba9c8289b885aef7641
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Azure Cosmos DB Graph .NET API: ダウンロードおよびリリース ノート

|   |   |
|---|---|
|**SDK のダウンロード**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](https://aka.ms/acdbgraphapiref)|
|**クイックスタート**|[Azure Cosmos DB: .NET と Graph API を使用したグラフ アプリの作成](create-graph-dotnet.md)|
|**チュートリアル**|[Azure CosmosDB: Graph API を使用したコンテナーの作成](tutorial-develop-graph-dotnet.md)|
|**現在サポートされているフレームワーク**| [Microsoft .NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>リリース ノート

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-preview

#### <a name="bug-fixes"></a>バグの修正
* 必要に応じて `appsettings.json` (`netstandard1.6`) を読み込むように修正しました

#### <a name="whats-new"></a>新機能
* Microsoft.Azure.Graphs のターゲット プラットフォーム AnyCPU への切り替え。
* `net461` パッケージ マニフェストからの Mono アセンブリの削除。

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>新機能
* `.netstandard 1.6` のサポートを追加しました
  * `Microsoft.Azure.DocumentDB.Core >= 1.5.1` が必要です
* 既存のパーサーを置き換える新しい `gremlin-groovy` パーサーを追加しました。 このパーサーは、Tinkerpop の `gremlin-groovy` 構文のサブセットをサポートし、以下が改善点があります。
  * 解析のパフォーマンスを 2 倍向上しました。
  * 古いパーサーの文字列に含まれる文字のエスケープ処理、誤って処理されていたリテラル値、他の不規則な問題に関係する複数の問題を解決しました。
* エッジ述語を使用したトラバーサルの最適化を追加しました。
  *  フィルターを使用したトラバーサル ホップでは、この改善を確認できます。次に例を示します。`g.V('1').outE().has('name', 'marko').inV()`
* `limit()` ステップを使用したトラバーサルの最適化を追加しました。

#### <a name="breaking-changes"></a>重大な変更
* .NET Framework 4.5.1 のサポートが廃止されました

* 新しいパーサーは、`gremlin-groovy` の文法に従っています。 そのため、以前は動作していた式でも、新しいパーサーではあいまいになる可能性があります。 注意が必要な例を 1 つ挙げます。
  * `in` および `as` は `gremlin-groovy` で予約済みのキーワードなので、構文エラーを防ぐために、`.in()` または `.as()` を使用してこれらのステップを修飾する必要があります。 例: `g.V().repeat(in()).times(2)` -> _構文エラーがスローされます_  
 `g.V().repeat(__.in()).times(2)` -> _成功します_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* 初期プレビュー リリース。

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了の SDK を使用した Azure Cosmos DB への要求は、サービスによって拒否されます。

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [0.3.1-preview](#0.3.1-preview) |2017 年 10 月 17 日 |--- |
| [0.3.0-preview](#0.3.0-preview) |2017 年 10 月 2 日 |--- |
| [0.2.4-preview](#0.2.4-preview) |2017 年 8 月 4 日 |--- |
| [0.2.2-preview](#0.2.2-preview) |2017 年 6 月 23 日 |--- |
| [0.2.1-preview](#0.2.2-preview) |2017 年 6 月 8 日 |--- |
| [0.2.0-preview](#0.2.2-preview) |2017 年 5 月 10 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2017 年 5 月 8 日 |--- |

## <a name="see-also"></a>関連項目
Azure Cosmos DB Graph API の詳細については、「[Azure Cosmos DB の概要: Graph API](graph-introduction.md)」をご覧ください。 
