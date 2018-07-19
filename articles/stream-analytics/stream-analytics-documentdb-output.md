---
title: Cosmos DB への Azure Stream Analytics の出力
description: この記事では、構造化されていない JSON データに対するデータ アーカイブと待機時間の短いクエリのために、Azure Stream Analytics を使用して、Azure Cosmos DB for JSON 出力に出力を保存する方法について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: ff2071a703b0b5e94cd68122a878b51e9d97669a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112753"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB への Azure Stream Analytics の出力  
Stream Analytics では、 JSON 出力のターゲットを [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) にすることができるため、構造化されていない JSON データに対してデータ アーカイブと待機時間の短いクエリを有効にすることができます。 このドキュメントでは、この構成を実装するためのベスト プラクティスについて説明します。

Cosmos DB を詳しく理解していない場合は、開始する前に [Azure Cosmos DB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)に関するページをご覧ください。 

> [!Note]
> 現時点では、Azure Stream Analytics は、**SQL API** を使用した Azure CosmosDB への接続のみをサポートしています。
> その他の Azure Cosmos DB API は、まだサポートされていません。 Azure Stream Analytics を、その他のAPI で作成した Azure Cosmos DB アカウントへ接続する場合は、データが正しく格納されない可能性があります。 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>出力ターゲットとしての Cosmos DB の基礎
Stream Analytics で Azure Cosmos DB 出力を使用すると、ストリーム処理の結果を JSON 出力として Cosmos DB コレクションに書き込むことができます。 Stream Analytics は、データベース内にコレクションを作成せず、代わりにユーザーが前もってコレクションを作成するように要求します。 これにより、Cosmos DB コレクションの課金によるコストをユーザーが制御できるようになり、[Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用することでコレクションのパフォーマンス、整合性、容量などを直接調整できるようになりました。 

Cosmos DB コレクションの一部のオプションの詳細を以下に示します。

## <a name="tune-consistency-availability-and-latency"></a>調整の整合性、可用性、および待機時間
Azure Cosmos DB では、アプリケーション要件を満たすために、データベースやコレクションを微調整し、整合性、可用性、待機時間の間で妥協点を見つけることができます。 読み取りおよび書き込みの待機時間に対してシナリオで求められる読み取りの一貫性レベルに応じて、データベース アカウントでの一貫性レベルを選択することができます。 また Azure Cosmos DB では、コレクションへの各 CRUD 操作に対する同期インデックス作成も、既定で有効になっています。 この機能も、Azure Cosmos DB で書き込みと読み取りのパフォーマンスを制御するための便利なオプションの 1 つです。 詳しくは、[データベースとクエリの一貫性レベルの変更](../cosmos-db/consistency-levels.md) に関する記事をご覧ください。

## <a name="upserts-from-stream-analytics"></a>Stream Analytics からのアップサート
Stream Analytics を Azure Cosmos DB と統合することで、特定のドキュメント ID 列に基づき、コレクションでレコードを挿入または更新できるようになります。 この機能は、 *アップサート*とも呼ばれています。

Stream Analytics では、オプティミスティック アップサート手法を使用しています。この手法では、ドキュメント ID の競合が原因で挿入に失敗した場合にのみ更新が実行されます。 この更新は PATCH として実行されるため、ドキュメントに対する部分更新が可能になります。つまり、新しいプロパティの追加や既存のプロパティの置き換えは段階的に実行されます。 ただし、JSON ドキュメント内の配列プロパティの値を変更すると、配列全体が上書きされることになるので注意してください。つまり、配列はマージされません。

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB でのデータ パーティション分割
Azure Cosmos DB はワークロードに基づいてパーティションを自動的にスケーリングされるので、データのパーティション分割には[無制限](../cosmos-db/partition-data.md)の Azure Cosmos DB がお勧めです。 無制限コンテナーに書き込む場合、Stream Analytics は以前のクエリ手順または入力のパーティション分割スキームと同数の並列ライターを使用します。

固定の Azure Cosmos DB コレクションの場合、コレクションが一杯になっても Stream Analytics をスケールアップまたはスケールアウトすることができません。 上限は 10 GB と 10,000 RU/秒スループットです。  固定コンテナーから無制限のコンテナー (1,000 RU/秒以上のスループットとパーティション キーを備えたコンテナーなど) にデータを移行するには、[データ移行ツール](../cosmos-db/import-data.md)または[変更フィード ライブラリ](../cosmos-db/change-feed.md)を使用する必要があります。

複数の固定コンテナーへの書き込みは非推奨です。また、Stream Analytics ジョブのスケールアウトに推奨される方法ではありません。 この詳細については、[Cosmos DB でのパーティション分割とスケーリング](../cosmos-db/sql-api-partition-data.md)に関する記事を参照してください。

## <a name="cosmos-db-settings-for-json-output"></a>JSON 出力の Cosmos DB 設定
Stream Analytics で Cosmos DB を出力として作成すると、情報の入力を求めるプロンプトが以下のように表示されます。 このセクションでは、各プロパティの定義について説明します。


![documentdb stream analytics 出力画面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

フィールド           | 説明 
-------------   | -------------
出力のエイリアス    | ASA クエリ内でこの出力を意味するエイリアス。   
アカウント名    | Azure Cosmos DB アカウントの名前またはエンドポイント URI 
アカウント キー     | Azure Cosmos DB アカウントの共有アクセス キー
データベース        | Azure Cosmos DB データベース名
コレクション名 | 使用するコレクションのコレクション名。 `MyCollection` は有効な入力の例です。`MyCollection` という 1 つのコレクションが存在する必要があります。  
ドキュメント ID     | 省略可能。 挿入操作または更新操作の基にする必要がある固有キーとして使用される出力イベント内の列名。 空のままにすると、更新オプションはなく、すべてのイベントが挿入されます。
