---
title: Azure Cosmos DB 用 Azure PowerShell サンプル
description: Azure Cosmos DB で一般的なタスクを実行するための Azure PowerShell サンプルを入手します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 0c2d13ef49676917bc48dd5a2e682471558567f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842973"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure Cosmos DB 用 Azure PowerShell サンプル

次の表では、Azure Cosmos DB でよく使用される Azure PowerShell スクリプトへのリンクが示されています。 API 固有のサンプルに移動するには、右側のリンクを使用します。 共通サンプルは、すべての API で同じです。 Azure Cosmos DB PowerShell のすべてのコマンドレットのリファレンス ページについては、[Azure PowerShell リファレンス](/powershell/module/az.cosmosdb)を参照してください。 `Az.CosmosDB` の更新プログラムを定期的にチェックしてください。 [GitHub の Cosmos DB PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)の GitHub リポジトリから、これらの Cosmos DB 用 PowerShell サンプルをフォークすることもできます。

## <a name="common-samples"></a>一般的なサンプル

|タスク | 説明 |
|---|---|
|[アカウントの更新](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB アカウントの既定の一貫性レベルを更新します。 |
|[アカウントのリージョンの更新](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB アカウントのリージョンを更新します。 |
|[フェールオーバー優先度の変更またはフェールオーバーのトリガー](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのリージョン内フェールオーバー優先度を変更したり、手動フェールオーバーをトリガーしたりします。 |
|[アカウント キーまたは接続文字列](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| プライマリ キーとセカンダリ キー、接続文字列を取得します。または Azure Cosmos DB アカウントのアカウント キーを再生成します。 |
|[IP ファイアウォールを使用した Cosmos アカウントの作成](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| IP ファイアウォールを有効にして Azure Cosmos DB アカウントを作成します。 |
|||

## <a name="core-sql-api-samples"></a>Core (SQL) API サンプル

|タスク | 説明 |
|---|---|
|[アカウント、データベース、およびコンテナーの作成](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB アカウント、データベース、コンテナーを作成する |
|[アカウント、データベース、自動スケーリングするコンテナーを作成する](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB アカウント、データベース、自動スケーリングするコンテナーを作成します。 |
|[大きいパーティション キーを含むコンテナーの作成](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 大きいパーティション キーを含むコンテナーを作成します。 |
|[インデックス ポリシーを持たないコンテナーの作成](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | インデックス ポリシーが無効になっている Azure Cosmos コンテナーを作成します。|
|[データベースまたはコンテナーの一覧表示または取得](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/sql/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||

## <a name="cassandra-api-samples"></a>Cassandra API サンプル

|タスク | 説明 |
|---|---|
|[アカウント、キースペース、およびテーブルの作成](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、キースペース、およびテーブルを作成します。 |
|[アカウント、キースペース、自動スケーリングするテーブルを作成する](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、キースペース、自動スケーリングするテーブルを作成します。 |
|[キースペースまたはテーブルの一覧表示または取得](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| キースペースまたはテーブルを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| キースペースまたはテーブルに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||

## <a name="mongo-db-api-samples"></a>Mongo DB API サンプル

|タスク | 説明 |
|---|---|
|[アカウント、データベース、およびコレクションの作成](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、データベース、およびコレクションを作成します。 |
|[アカウント、データベース、自動スケーリングするコレクションを作成する](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、データベース、自動スケーリングするコレクションを作成します。 |
|[データベースまたはコレクションの一覧表示または取得](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコレクションを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコレクションに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||

## <a name="gremlin-api-samples"></a>Gremlin API サンプル

|タスク | 説明 |
|---|---|
|[アカウント、データベース、およびグラフの作成](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、データベース、およびグラフを作成します。 |
|[アカウント、データベース、自動スケーリングするグラフを作成する](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、データベース、自動スケーリングするグラフを作成します。 |
|[データベースまたはグラフの一覧表示または取得](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはグラフを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはグラフに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||

## <a name="table-api-samples"></a>Table API サンプル

|タスク | 説明 |
|---|---|
|[アカウントとテーブルの作成](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントとテーブルを作成します。 |
|[アカウントと自動スケーリングするテーブルを作成する](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントと自動スケーリングするテーブルを作成します。 |
|[テーブルの一覧表示または取得](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| テーブルを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| テーブルに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||
