---
title: Azure Cosmos DB の Azure CLI サンプル
description: Azure CLI サンプル - Azure Cosmos DB のアカウント、データベース、コンテナー、リージョン、およびファイアウォールを作成し、管理します。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 48beb93fbc5952951fff1ed31e5f8625faf78ccd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850399"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure CLI サンプル

次の表には、Azure Cosmos DB の Azure CLI スクリプトのサンプルへのリンクが含まれています。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI リファレンス](/cli/azure/cosmosdb)で確認できます。

| |  |
|---|---|
|**Azure Cosmos DB のアカウント、データベース、およびコンテナーの作成**||
| [SQL API アカウントの作成](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Azure Cosmos DB SQL API の単一のアカウント、データベース、およびコンテナーを作成します。 |
| [MongoDB API アカウントの作成](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB MongoDB API の単一のアカウント、データベース、およびコレクションを作成します。 |
| [Gremlin API アカウントの作成](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB Gremlin API の単一のアカウント、データベース、およびグラフを作成します。 |
| [Cassandra API アカウントの作成](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB Cassandra API の単一のアカウントおよびデータベースを作成します。 |
| [Table API アカウントの作成](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB Table API の単一のアカウント、データベース、およびテーブルを作成します。 |
|**Azure Cosmos DB のスケーリング**||
| [コンテナーのスループットのスケーリング](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | コンテナーのプロビジョニング済みスループットを変更します。|
| [複数リージョンへの Azure Cosmos DB データベース アカウントのレプリケートと、フェールオーバーの優先順位の構成](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|指定したフェールオーバー優先順位で、複数リージョンにグローバルにアカウント データをレプリケートします。|
|**Azure Cosmos DB のセキュリティ保護**||
| [アカウント キーの取得](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | アカウントのプライマリおよびセカンダリのマスター書き込みキーと、プライマリおよびセカンダリの読み取り専用キーを取得します。|
| [MongoDB 接続文字列の取得](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | MongoDB アプリを Azure Cosmos DB アカウントに接続する接続文字列を取得します。|
| [アカウント キーの再生成](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|アカウントのキーを再生成します。|
| [ファイアウォールの作成](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 承認した一連のマシンやクラウド サービスからアカウントへのアクセスを制限するインバウンド IP アクセス制御ポリシーを作成します。|
|**高可用性、ディザスター リカバリー、バックアップと復元**||
| [フェールオーバー ポリシーの構成](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|アカウントのレプリケート先の各リージョンのフェールオーバー優先順位を設定します。|
|**Azure Cosmos DB とリソースの接続**||
| [Web アプリと Azure Cosmos DB の接続](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Azure Cosmos DB データベースと Azure Web アプリを作成し、接続します。|
|||
