---
title: Azure Cosmos DB の MongoDB 用 API へのデータ移行の移行前手順
description: このドキュメントでは、MongoDB から Cosmos DB にデータを移行する前提条件の概要について説明します。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445191"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB から Azure Cosmos DB の MongoDB 用 API へのデータ移行の移行前手順

(オンプレミスまたはクラウド (IaaS) 上の) MongoDB から Azure Cosmos DB の MongoDB 用 API にデータを移行する前に、次を実行する必要があります。

1. [Azure Cosmos DB アカウントの作成](#create-account)
2. [ワークロードに必要なスループットの見積もり](#estimate-throughput)
3. [データに最適なパーティション キーの選択](#partitioning)
4. [データに設定できるインデックス作成ポリシーの理解](#indexing)

上記の移行の前提条件が既に完了している場合、実際のデータの移行手順を、[MongoDB データの Azure Cosmos DB の MongoDB 用 API への移行](../dms/tutorial-mongodb-cosmos-db.md)に関するページで参照してください。 まだ行っていない場合、これらの前提条件の実行方法をこのドキュメントで確認してください。 

## <a id="create-account"></a> Azure Cosmos DB アカウントの作成 

移行を開始する前に、[Azure Cosmos DB の MongoDB 用 API を使用して Azure Cosmos アカウントを作成](create-mongodb-dotnet.md)する必要があります。 

アカウントを作成したら、データを[グローバルに分散](distribute-data-globally.md)する設定を選択します。 また、お使いの各リージョンが書き込みリージョンおよび読み取りリージョンの両方になるよう、マルチリージョン書き込み (またはマルチマスター構成) を有効にするオプションがあります。

![アカウントの作成](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> ワークロードに必要なスループットの見積もり

[Database Migration Service (DMS)](../dms/dms-overview.md) を使用して移行を開始する前に、Azure Cosmos データベースおよびコレクションにプロビジョニングするスループットの量を見積もる必要があります。

スループットは、以下のいずれかにプロビジョニングできます。

- コレクション

- データベース

> [!NOTE]
> データベース内の一部のコレクションには専用のスループットをプロビジョニングし、他はそのスループットを共有するように、上記を組み合わせることも可能です。 詳細については、[データベースおよびコンテナーへのスループットの設定](set-throughput.md)に関するページを参照してください。
>

まず、データベースまたはコレクション レベルのスループットをプロビジョニングするか、この両方の組み合わせをプロビジョニングするかを決定する必要があります。 一般的には、コレクション レベルで専用のスループットを構成することが推奨されます。 データベース レベルでスループットをプロビジョニングすると、お使いのデータベース内のコレクションがそのプロビジョニング済みのスループットを共有できるようになります。 ただし、共有のスループットでは、個々のそれぞれのコレクションに特定のスループットが保証されないので、どの特定のコレクションのパフォーマンスも予測できなくなります。

個々のそれぞれのコレクションにどれくらいのスループットを確保すべきかわからない場合、データベースレベルのスループットを選択します。 お使いの Azure Cosmos データベースに構成されたプロビジョニング済みスループットは、MongoDB VM や物理サーバーの計算容量と論理的に同等であると考えることができます。ただし、弾力的にスケーリングでき、よりコスト効率が高いです。 詳細については、[Azure Cosmos コンテナーおよびデータベースへのスループットのプロビジョニング](set-throughput.md)に関するページを参照してください。

スループットをデータベース レベルでプロビジョニングした場合、そのデータベース内に作成したすべてのコレクションは、パーティションとシャード キーを使用して作成する必要があります。 パーティションの詳細については、[Azure Cosmos DB でのパーティション分割と水平スケーリング](partition-data.md)に関するページをご覧ください。 移行中にパーティションまたはシャード キーを指定しない場合、Azure Database Migration Service によって各ドキュメントに自動生成されるシャード キーのフィールドに、 *_id* 属性が自動入力されます。

### <a name="optimal-number-of-request-units-rus-to-provision"></a>プロビジョニングする要求ユニット (RU) の最適な数

Azure Cosmos DB では、スループットは事前にプロビジョニングされ、1 秒あたりの要求ユニット (RU) で測定されます。 VM またはオンプレミスに MongoDB を実行するワークロードがある場合、RU は、VM のサイズ、またはオンプレミス サーバーとそれらが所有しているメモリ、CPU、IOPS などのリソースなど、物理リソースを単純に抽象化したものであると考えてください。 

VM やオンプレミス サーバーとは異なり、RU はいつでも簡単にスケールアップしたりスケールダウンしたりすることができます。 プロビジョニング済みの RU 数は数秒で変更でき、ユーザーは指定した 1 時間の間にプロビジョニングした RU の最大数に対してのみ課金されます。 詳細については、「[Azure Cosmos DB の要求ユニット](request-units.md)」を参照してください。

必要な RU 数に影響する主な要因は、次のとおりです。
- **アイテム (例: ドキュメント) のサイズ**:アイテム/ドキュメントのサイズが増加すると、そのアイテム/ドキュメントの読み書きに消費される RU 数も増加します。
- **アイテム プロパティの数**:すべてのプロパティに[既定でインデックスが作成される](index-overview.md)場合、アイテムのプロパティ数の増加に伴い、アイテムを書き込むために消費される RU 数も増加します。 [インデックス付きのプロパティ数を制限する](index-policy.md)と、書き込み操作で消費される要求ユニットを削減できます。
- **同時実行操作**:消費される要求ユニットの数は、(書き込み、読み取り、更新、削除など) さまざまな CRUD 操作やより複雑なクエリが実行される頻度にも依存します。 [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) を使用すると、ご自分の現在の MongoDB データでのコンカレンシーの必要性を出力できます。
- **クエリのパターン**:クエリが複雑であると、そのクエリで消費される要求ユニット数に影響します。

[mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) を使用して JSON ファイルをエクスポートし、1 秒あたりに実行される書き込み、読み取り、更新および削除の数がわかると、プロビジョニングする RU の最初の数を [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) を使用して見積もることができます。 Capacity Planner では、より複雑なクエリのコストは考慮されません。 そのため、データに対して複雑なクエリがある場合、追加の RU が消費されます。 電卓でも、すべてのフィールドにインデックスが作成され、セッションの整合性が使用されることを想定しています。 クエリのコストを理解する最良の方法は、データ (またはサンプル データ) を Azure Cosmos DB に移行し、[Cosmos DB のエンドポイントに接続](connect-mongodb-account.md)し、`getLastRequestStastistics` コマンドを使用して MongoDB Shell からサンプル クエリを実行して、消費される RU の数を出力する要求の料金を取得することです。

`db.runCommand({getLastRequestStatistics: 1})`

このコマンドでは、以下のような JSON ドキュメントが出力されます。

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

クエリで消費される RU 数とそのクエリでのコンカレンシーの必要性がわかったら、プロビジョニングされている RU 数を調整できます。 RU の調整を行うのは一回だけではありません。負荷の高いワークロードまたはデータのインポートとは対照的に、大量のトラフィックを予期していないかどうかに関わらず、プロビジョニングされている RU は、継続して最適化またはスケールアップする必要があります。

## <a id="partitioning"></a>パーティション キーの選択
Azure Cosmos DB などのグローバルに分散されるデータベースに移行する前に考慮すべき重要な点は、パーティション分割です。 Azure Cosmos DB では、パーティション分割を使用して、データベースの個々のコンテナーをスケーリングし、お使いのアプリケーションの拡張とパフォーマンスに対するニーズを満たしています。 パーティション分割では、コンテナー内の項目は論理パーティションと呼ばれる明確なサブセットに分割されます。 データに適したパーティション キーの選択の詳細および推奨については、[パーティション キーの選択に関するセクション](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)を参照してください。 

## <a id="indexing"></a>データのインデックス作成
Azure Cosmos DB は、既定で、取り込み時にお使いのすべてのデータ フィールドにインデックス作成を行います。 Azure Cosmos DB の[インデックス作成のポリシー](index-policy.md)はいつでも変更できます。 実際、データの移行時にはインデックス作成をオフにして、データが Cosmos DB に入ったらオンに戻すことがしばしば推奨されます。 インデックス作成の詳細については、[Azure Cosmos DB でのインデックス作成](index-overview.md)のセクションを参照してください。 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) では、一意のインデックスがある MongoDB のコレクションは自動的に移行されます。 ただし、一意のインデックスは移行前に作成する必要があります。 Azure Cosmos DB では、コレクションにデータが既にある場合、一意のインデックスの作成をサポートしていません。 詳細については、[Azure Cosmos DB における一意のキー](unique-keys.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
* [Database Migration Service を使用してお使いの MongoDB データを Cosmos DB に移行する。](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos のコンテナーとデータベースにスループットをプロビジョニングする](set-throughput.md)
* [Azure Cosmos DB でのパーティション分割](partition-data.md)
* [Azure Cosmos DB でのグローバル分散](distribute-data-globally.md)
* [Azure Cosmos DB のインデックス作成](index-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
