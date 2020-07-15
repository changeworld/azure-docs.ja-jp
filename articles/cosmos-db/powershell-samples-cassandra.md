---
title: Azure Cosmos DB の Azure PowerShell サンプル - Cassandra API
description: Azure Cosmos DB Cassandra API アカウントにおける各種の一般的タスクを実行する Azure PowerShell サンプルを入手します
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: fec25bb3043942605895491952d963ee20ac4078
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563889"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---cassandra-api"></a>Azure Cosmos DB の Azure PowerShell サンプル - Cassandra API

次の表には、Azure Cosmos DB for Cassandra API の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

> [!NOTE]
> このサンプルでは、[Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 管理コマンドレットを使用します。 `Az.CosmosDB` の更新プログラムを定期的にチェックしてください。

|タスク | 説明 |
|---|---|
|[アカウント、キースペース、およびテーブルの作成](scripts/powershell/cassandra/ps-cassandra-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、キースペース、およびテーブルを作成します。 |
|[キースペースまたはテーブルの一覧表示または取得](scripts/powershell/cassandra/ps-cassandra-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| キースペースまたはテーブルを一覧表示または取得します。 |
|[RU/秒の取得](scripts/powershell/cassandra/ps-cassandra-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| キースペースまたはテーブルの RU/秒を取得します。 |
|[RU/秒の更新](scripts/powershell/cassandra/ps-cassandra-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| キースペースまたはテーブルの RU/秒を更新します。 |
|[アカウントの更新またはリージョンの追加](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos アカウントにリージョンを追加します。 他のアカウント プロパティを変更する際にも使用できますが、リージョンに対する変更とは別に行う必要があります。 |
|[フェールオーバー優先度の変更またはフェールオーバーのトリガー](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのリージョン内フェールオーバー優先度を変更したり、手動フェールオーバーをトリガーしたりします。 |
|[アカウント キーまたは接続文字列](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| プライマリ キーとセカンダリ キー、接続文字列を取得します。または Azure Cosmos アカウントのアカウント キーを再生成します。 |
|[IP ファイアウォールを使用した Cosmos アカウントの作成](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| IP ファイアウォールを有効にして Azure Cosmos アカウントを作成します。 |
|[リソースが削除されないようにロックする](scripts/powershell/cassandra/powershell-cassandra-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||
