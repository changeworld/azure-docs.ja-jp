---
title: Azure Cosmos DB Cassandra API 用の Azure PowerShell サンプル
description: Azure Cosmos DB Cassandra API で一般的なタスクを実行するための Azure PowerShell サンプルを入手します
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 4f3166693de0365af387f4b6005a4a309f17bb5b
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342285"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 用の Azure PowerShell サンプル
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

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

## <a name="cassandra-api-samples"></a>Cassandra API サンプル

|タスク | 説明 |
|---|---|
|[アカウント、キースペース、およびテーブルの作成](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、キースペース、およびテーブルを作成します。 |
|[アカウント、キースペース、自動スケーリングするテーブルを作成する](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、キースペース、自動スケーリングするテーブルを作成します。 |
|[キースペースまたはテーブルの一覧表示または取得](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| キースペースまたはテーブルを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| キースペースまたはテーブルに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||
