---
title: Azure Cosmos DB 用 Azure PowerShell サンプル - SQL (Core) API
description: Azure Cosmos DB SQL API アカウントにおける各種の一般的タスクを実行する Azure PowerShell サンプルを入手します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366179"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure Cosmos DB 用 Azure PowerShell サンプル - SQL (Core) API

次の表には、Azure Cosmos DB の SQL (Core) API に一般的に使用される Azure PowerShell スクリプトへのリンクが含まれています。 これらの Cosmos DB 用 PowerShell サンプルを GitHub リポジトリからフォークしたい場合は、[GitHub の Cosmos DB PowerShell サンプル](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)にアクセスしてください。

その他の Cosmos DB SQL (Core) API 用 PowerShell サンプルとドキュメントについては、「[PowerShell を使用して Azure Cosmos DB SQL API リソースを管理する](manage-with-powershell.md)」を参照してください。 その他、Cosmos DB の各種 API 向けの PowerShell サンプルについては、[Cassandra API](powershell-samples-cassandra.md)、[MongoDB API](powershell-samples-mongodb.md)、[Gremlin API](powershell-samples-gremlin.md)、[Table API](powershell-samples-table.md) を参照してください。

> [!NOTE]
> このサンプルでは、[Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 管理コマンドレットを使用します。 `Az.CosmosDB` のコマンドレットはまだプレビュー段階であり、リリースまでに変更される可能性があります。 `Az.CosmosDB` の更新プログラムを定期的にチェックしてください。

| | |
|---|---|
|[アカウント、データベース、およびコンテナーの作成](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB アカウント、データベース、コンテナーを作成する |
|[大きいパーティション キーを含むコンテナーの作成](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 大きいパーティション キーを含むコンテナーを作成します。 |
|[データベースまたはコンテナーの一覧表示または取得](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーを一覧表示または取得します。 |
|[RU/秒の取得](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーの RU/秒を取得します。 |
|[RU/秒の更新](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコンテナーの RU/秒を更新します。 |
|[インデックス ポリシーを持たないコンテナーの作成](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | インデックス ポリシーが無効になっている Azure Cosmos コンテナーを作成します。|
|[アカウントの更新](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB アカウントの既定の一貫性レベルを更新します。 |
|[アカウントのリージョンの更新](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB アカウントのリージョンを更新します。 |
|[フェールオーバー優先度の変更またはフェールオーバーのトリガー](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのリージョン内フェールオーバー優先度を変更したり、手動フェールオーバーをトリガーしたりします。 |
|[アカウント キーまたは接続文字列](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| プライマリ キーとセカンダリ キー、接続文字列を取得します。または Azure Cosmos DB アカウントのアカウント キーを再生成します。 |
|[IP ファイアウォールを使用した Cosmos アカウントの作成](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| IP ファイアウォールを有効にして Azure Cosmos DB アカウントを作成します。 |
|||
