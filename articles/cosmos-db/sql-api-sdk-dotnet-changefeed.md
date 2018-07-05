---
title: Azure Cosmos DB .NET Change Feed Processor API、SDK およびリソース | Microsoft Docs
description: リリース日、提供終了日、.NET Change Feed Processor SDK の各バージョン間の変更など、Change Feed Processor API と SDK に関するあらゆる詳細を提供します。
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/21/2018
ms.author: maquaran
ms.openlocfilehash: f69742d111555e776a968454bdc004ba171e6336
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937419"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processor SDK: ダウンロードおよびリリース ノート
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK のダウンロード**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API ドキュメント**|[Change Feed Processor ライブラリ API リファレンス ドキュメント](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**はじめに**|[DocumentDB Change Feed Processor .NET SDK の概要](change-feed.md)|
|**現在サポートされているフレームワーク**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>リリース ノート

### <a name="stable-builds"></a>安定したビルド

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* ログが追加されました。
* 保留中の作業見積もりを複数回呼び出すときの DocumentClient のリークを修正しました。

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* 保留中の作業見積もりを修正しました。

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* 安定性が向上しました。
  * 一部のパーティションでオブザーバーの停止につながる可能性がある取り消されたタスクの問題を処理するために修正しました。
* 手動チェックポイント処理をサポートします。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.21 以降と互換性があります。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* .NET Standard 2.0 のサポートを追加します。 このパッケージで `netstandard2.0`と`net451` フレームワーク モニカーがサポートされるようになりました。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.17.0 以降と互換性があります。
* [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) バージョン 1.5.1 以降と互換性があります。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 変更フィードが空であるか保留中の作業がない場合の、残っている作業の推定量の計算に関する問題を修正しました。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.13.2 以降と互換性があります。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Change Feed に処理が残っている作業の推定量を取得するメソッドが追加されました。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.13.2 以降と互換性があります。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.14.1 以降と互換性があります。

### <a name="pre-release-builds"></a>プレリリース ビルド

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* 次の問題が修正されました。
  * パーティション分割が発生すると、分割前に修正されたドキュメントの処理が重複する可能性があります。
  * リース コレクションにリースが存在しない場合、GetEstimatedRemainingWork API は 0 を返しました。

* 次の例外が公開されました。 IPartitionProcessor を実装する拡張機能では、次の例外をスローすることができます。
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException。 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException。 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException。
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException。 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* マイナーな API の変更:
  * 古いとマークされた ChangeFeedProcessorOptions.IsAutoCheckpointEnabled を削除しました。

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* 安定性の向上:
  * リース ストアの初期化の処理が改善しました。 リース ストアが空の場合、プロセッサの 1 つのインスタンスだけがそれを初期化でき、他は待機します。
  * リースの更新または解放の安定性と効率が向上しました。 1 つのパーティションのリースの更新と解放は、他のパーティションの更新から独立しています。 v1 では、すべてのパーティションに対して順番に行われていました。
* 新しい v2 API:
  * プロセッサーの柔軟な構築に対応したビルダー パターン: ChangeFeedProcessorBuilder クラス。
    * 任意の組み合わせのパラメーターを取得できます。
    * 監視用の DocumentClient インスタンスとリース コレクションの両方またはいずれかを取得できます (v1 では利用できません)。
  * IChangeFeedObserver.ProcessChangesAsync で CancellationToken を取得できるようになりました。
  * IRemainingWorkEstimator - 残存する作業見積もりツールは、プロセッサとは個別に使用できます。
  * 新しい拡張性ポイント:
    * IParitionLoadBalancingStrategy - プロセッサのインスタンス間でパーティションのカスタム負荷分散に使用します。
    * ILease、ILeaseManager - カスタムのリース管理に使用します。
    * IPartitionProcessor - パーティション上にあるカスタム処理の変更に使用します。
* ログの記録 - [LibLog](https://github.com/damianh/LibLog) ライブラリを使用します。
* v1 API との 100% の下位互換性
* 新しいコード ベース。
* [SQL .NET SDK](sql-api-sdk-dotnet.md) バージョン 1.21.1 以降と互換性があります。

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了の SDK を使用した Cosmos DB への要求は、サービスによって拒否されます。

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.3.3](#1.3.3) |2018 年 5 月 8 日 |--- |
| [1.3.2](#1.3.2) |2018 年 4 月 18 日 |--- |
| [1.3.1](#1.3.1) |2018 年 3 月 13 日 |--- |
| [1.2.0](#1.2.0) |2017 年 10 月 31 日 |--- |
| [1.1.1](#1.1.1) |2017 年 8 月 29 日 |--- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページをご覧ください。 

