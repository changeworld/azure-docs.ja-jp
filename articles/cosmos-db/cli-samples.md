---
title: Azure Cosmos DB の Azure CLI サンプル | Microsoft Docs
description: Azure CLI サンプル - Azure Cosmos DB のアカウント、データベース、コンテナー、リージョン、およびファイアウォールを作成し、管理します。
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/29/2017
ms.author: sngun
ms.openlocfilehash: 42cfe71210b95732b4b69f7ca21a8b647e187a38
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858862"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure Cosmos DB の Azure CLI サンプル

次の表には、Azure Cosmos DB の Azure CLI スクリプトのサンプルへのリンクが含まれています。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI 2.0 リファレンス](https://docs.microsoft.com/cli/azure/cosmosdb)のページで確認できます。

| |  |
|---|---|
|**Azure Cosmos DB のアカウント、データベース、およびコンテナーの作成**||
|[SQL API アカウントの作成](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| SQL API で使う Azure Cosmos DB API の単一のアカウント、データベース、およびコンテナーを作成します。 |
| [MongoDB API アカウントの作成](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB MongoDB API の単一のアカウント、データベース、およびコレクションを作成します。 |
| [Gremlin API アカウントの作成](scripts/create-graph-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Cosmos DB Gremlin API の単一のアカウント、データベース、およびコンテナーを作成します。 |
|**Azure Cosmos DB のスケーリング**||
| [コンテナーのスループットのスケーリング](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | コンテナーのプロビジョニング済みスループットを変更します。|
|[複数リージョンへの Azure Cosmos DB データベース アカウントのレプリケートと、フェールオーバーの優先順位の構成](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|指定したフェールオーバー優先順位で、複数リージョンにグローバルにアカウント データをレプリケートします。|
|**Azure Cosmos DB のセキュリティ保護**||
| [アカウント キーの取得](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | アカウントのプライマリおよびセカンダリのマスター書き込みキーと、プライマリおよびセカンダリの読み取り専用キーを取得します。|
| [MongoDB 接続文字列の取得](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | MongoDB アプリを Azure Cosmos DB アカウントに接続する接続文字列を取得します。|
|[アカウント キーの再生成](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|アカウントのマスター キーまたは読み取り専用キーを再生成します。|
|[ファイアウォールの作成](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 承認した一連のコンピューターやクラウド サービスからアカウントへのアクセスを制限する着信 IP アクセス制御ポリシーを作成します。|
|**高可用性、ディザスター リカバリー、バックアップと復元**||
|[フェールオーバー ポリシーの構成](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|アカウントのレプリケート先の各リージョンのフェールオーバー優先順位を設定します。|
|**Azure Cosmos DB とリソースの接続**||
|[Web アプリと Azure Cosmos DB の接続](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Azure Cosmos DB データベースと Azure Web アプリを作成し、接続します。|
|||
