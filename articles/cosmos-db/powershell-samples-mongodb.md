---
title: Azure Cosmos DB 用 Azure PowerShell サンプル - MongoDB API
description: Azure Cosmos DB の MongoDB 用 API における各種の一般的タスクを実行する Azure PowerShell サンプルを入手します
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 697dc68018ced08e22efdc179f84a2c968f953a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563859"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB 用 Azure PowerShell サンプル: MongoDB API

次の表には、Azure Cosmos DB for MongoDB API の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。

> [!NOTE]
> 現在、PowerShell、CLI、および Resource Manager テンプレートを使用して、MongoDB アカウント用の Azure Cosmos DB の API の 3.2 バージョン (つまり、`*.documents.azure.com` 形式のエンドポイントを使用するアカウント) のみを作成できます。 アカウントの 3.6 バージョンを作成するには、代わりに Azure portal を使用します。

> [!NOTE]
> このサンプルでは、[Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) 管理コマンドレットを使用します。 `Az.CosmosDB` の更新プログラムを定期的にチェックしてください。

|タスク | 説明 |
|---|---|
|[アカウント、データベース、およびコレクションの作成](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウント、データベース、およびコレクションを作成します。 |
|[データベースまたはコレクションの一覧表示または取得](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコレクションを一覧表示または取得します。 |
|[RU/秒の取得](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコレクションの RU/秒を取得します。 |
|[RU/秒の更新](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| データベースまたはコレクションの RU/秒を更新します。 |
|[アカウントの更新またはリージョンの追加](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos アカウントにリージョンを追加します。 他のアカウント プロパティを変更する際にも使用できますが、リージョンに対する変更とは別に行う必要があります。 |
|[フェールオーバー優先度の変更またはフェールオーバーのトリガー](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos アカウントのリージョン内フェールオーバー優先度を変更したり、手動フェールオーバーをトリガーしたりします。 |
|[アカウント キーまたは接続文字列](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| プライマリ キーとセカンダリ キー、接続文字列を取得します。または Azure Cosmos アカウントのアカウント キーを再生成します。 |
|[IP ファイアウォールを使用した Cosmos アカウントの作成](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| IP ファイアウォールを有効にして Azure Cosmos アカウントを作成します。 |
|[リソースが削除されないようにロックする](scripts/powershell/mongodb/powershell-mongodb-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| リソース ロックを使用してリソースが削除されないようにします。 |
|||
