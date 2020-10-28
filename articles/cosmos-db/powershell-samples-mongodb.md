---
title: MongoDB 用 Azure Cosmos DB APIの Azure PowerShell サンプル
description: MongoDB 用 Azure Cosmos DB API で一般的なタスクを実行するための Azure PowerShell サンプルを入手します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68c4c2236fa5bd595a64525672809a146edbdc64
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282941"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>MongoDB 用 Azure Cosmos DB APIの Azure PowerShell サンプル

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

## <a name="mongo-db-api-samples"></a>Mongo DB API サンプル

|タスク | 説明 |
|---|---|
|[アカウント、データベース、およびコレクションの作成](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、データベース、およびコレクションを作成します。 |
|[アカウント、データベース、自動スケーリングするコレクションを作成する](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、データベース、自動スケーリングするコレクションを作成します。 |
|[データベースまたはコレクションの一覧表示または取得](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコレクションを一覧表示または取得します。 |
|[スループット操作](scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコレクションに対するスループット操作には、取得、更新、および自動スケーリングと標準スループット間の移行が含まれます。 |
|[リソースが削除されないようにロックする](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||
