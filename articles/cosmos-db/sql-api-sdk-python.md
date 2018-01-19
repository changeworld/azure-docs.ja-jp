---
title: "Azure Cosmos DB: SQL Python API、SDK、およびリソース | Microsoft Docs"
description: "リリース日、提供終了日、Azure Cosmos DB Python SDK の各バージョン間の変更など、SQL Python API と SDK に関するあらゆる詳細を提供します。"
services: cosmos-db
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 1/4/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6801c5b62be08e4dcb32ad342b15e9ad3f3e20a8
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2018
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用 Azure Cosmos DB Python SDK: リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

<table>

<tr><td>**SDK のダウンロード**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**API ドキュメント**</td><td>[Python の API リファレンス ドキュメント](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**SDK のインストール手順**</td><td>[Python SDK のインストール手順](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**SDK への協力**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**作業開始**</td><td>[Python SDK の開始](sql-api-python-application.md)</td></tr>

<tr><td>**現在サポートされているプラットフォーム**</td><td>[Python 2.7](https://www.python.org/downloads/) と [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート
### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Azure DocumentDB ではなく Azure Cosmos DB を参照するように更新されたドキュメントです。

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* この SDK バージョンでは、https://aka.ms/cosmosdb-emulator からダウンロードできる Azure Cosmos DB エミュレーターの最新バージョンが必要です。

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* 集計辞書のバグを修正しました。
* リソース リンクのスラッシュをトリムするときのバグを修正しました。
* Unicode エンコーディングのテストを追加しました。

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* ConsistentPrefix と呼ばれている新しい一貫性レベルに対応するようになりました。


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。
* Azure Cosmos DB Emulator に対しての実行時に、SSL 検証を無効にするためのオプションを追加しました。
* 依存する要求モジュールが必ず 2.10.0 でなければならないという制限をなくしました。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。
* ストアド プロシージャの実行時にスクリプトのログ記録を有効することができるようになりました。
* REST API バージョンが、このリリースで "2017-01-19" に上がりました。

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* ドキュメントのコメントを編集しました。

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Python 3.5 のサポートを追加しました。
* 要求モジュールを使用する接続プールのサポートを追加しました。
* Session 一貫性のサポートを追加しました。
* パーティション分割コレクションの TOP/ORDERBY クエリのサポートを追加しました。

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* スロットルされた要求のための再試行ポリシー サポートを追加しました  (スロットルされた要求は、要求レートが大きすぎるという例外をエラー コード 429 で受信します)。既定では、Azure Cosmos DB は、エラー コード 429 が発生した場合に、応答ヘッダーの retryAfter 回数に従って要求ごとに 9 回再試行します。 再試行の間にサーバーによって返される retryAfter 回数を無視する場合、固定の再試行間隔の回数を、ConnectionPolicy オブジェクトの RetryOptions プロパティの一部としてここで設定できます。 Azure Cosmos DB では、(再試行の回数に関係なく) スロットルされる要求ごとに最大 30 秒待機できるようになり、エラー コード 429 と共に応答を返すようになりました。 この回数は、ConnectionPolicy オブジェクトの RetryOptions プロパティでオーバーライドすることもできます。
* Cosmos DB は、スロットルの再試行の回数と再試行の間の要求の累積待機時間を表すために、すべての要求の応答ヘッダーとして x-ms-throttle-retry-count と x-ms-throttle-retry-wait-time-ms を返すようになりました。
* document_client クラスで公開されている RetryPolicy クラスとそれに対応するプロパティ (retry_policy) が削除され、代わりに、既定の一部の再試行オプションをオーバーライドするために使用できる ConnectionPolicy クラスの RetryOptions プロパティを公開する RetryOptions クラスが導入されました。

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* 複数リージョンのデータベース アカウントのサポートを追加しました。

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* ドキュメントの有効期限 (TTL) 機能のサポートを追加しました。

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* パーティション キーのパスでの特殊文字の使用を許可するためのサーバー側のパーティション分割に関連するバグの修正。

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* [パーティション分割コレクション](partition-data.md)と[ユーザー定義のパフォーマンス レベル](performance-levels.md)を実装しました。 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* ハッシュおよび範囲パーティション リゾルバーを追加して、複数のパーティションにわたってシャーディング アプリケーションを支援します。

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Upsert を実装します。 Upsert 機能をサポートするために新しい UpsertXXX メソッドが追加されました。
* ID ベースのルーティングを実装します。 パブリック API の変更なし、すべて内部の変更。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 地理空間インデックスをサポートします
* すべてのリソースの id プロパティを検証します。 リソースの ID には ?、/、#、\, 文字を使えず、終わりの文字をスペースにできません。
* ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2 インデックス作成ポリシーを実装します。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* プロキシ接続をサポートします。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK。

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了の SDK を使用した Cosmos DB への要求は、サービスによって拒否されます。

> [!WARNING]
> バージョン **1.0.0** 以前のすべてのバージョンの Azure SQL SDK for Python は、**2016 年 2 月 29 日**で提供が終了します。 
> 
> 

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [2.3.1](#2.3.1) |2017 年 12 月 21 日 |--- |
| [2.3.0](#2.3.0) |2017 年 11 月 10 日 |--- |
| [2.2.1](#2.2.1) |2017 年 9 月 29 日 |--- |
| [2.2.0](#2.2.0) |2017 年 5 月 10 日 |--- |
| [2.1.0](#2.1.0) |2017 年 5 月 1 日 |--- |
| [2.0.1](#2.0.1) |2016 年 10 月 30 日 |--- |
| [2.0.0](#2.0.0) |2016 年 9 月 29 日 |--- |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |--- |
| [1.6.1](#1.6.1) |2016 年 4 月 8 日 |--- |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1.5.0](#1.5.0) |2016 年 1 月 3 日 |--- |
| [1.4.2](#1.4.2) |2015 年 10 月 6 日 |--- |
| [1.4.1](#1.4.1) |2015 年 10 月 6 日 |--- |
| [1.2.0](#1.2.0) |2015 年 8 月 6 日 |--- |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1.0.1](#1.0.1) |2015 年 5 月 25 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |--- |
| 0.9.4-prelease |2015 年 1 月 14 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2014 年 12 月 9 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 11 月 25 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 9 月 23 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 8 月 21 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページをご覧ください。 

