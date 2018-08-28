---
title: Azure Cosmos DB の Azure PowerShell サンプル | Microsoft Docs
description: Azure PowerShell サンプル - Azure Cosmos DB アカウントの作成と管理で役立つスクリプト。
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 9ee5c7a008f375beffd6bbdf00cca8b28752b1fb
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "41924773"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure Cosmos DB 用 Azure PowerShell サンプル

次の表には、Azure Cosmos DB の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。 現時点では、PowerShell 経由で Azure Cosmos DB アカウントのみを管理できます。データベースやコンテナーなどの他のリソースは、PowerShell 経由では管理できません。

| |  |
|---|---|
|**Azure Cosmos DB アカウントの作成**||
|[SQL API アカウントの作成](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| SQL API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|[MongoDB API アカウントの作成](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| MongoDB API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|[Gremlin API アカウントの作成](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Gremlin API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|[Cassandra API アカウントの作成](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cassandra API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|[Table API アカウントの作成](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Table API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|**Azure Cosmos DB のスケーリング**||
|[複数リージョンへの Azure Cosmos DB アカウントのレプリケートと、フェールオーバーの優先順位の構成](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|指定したフェールオーバー優先順位で、複数リージョンにグローバルにアカウント データをレプリケートします。|
|**Azure Cosmos DB のセキュリティ保護**||
| [アカウント キーの取得](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | アカウントのプライマリおよびセカンダリのマスター書き込みキーと、プライマリおよびセカンダリの読み取り専用キーを取得します。|
| [MongoDB 接続文字列の取得](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | MongoDB アプリを Azure Cosmos DB アカウントに接続する接続文字列を取得します。|
|[アカウント キーの再生成](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|アカウントのマスター キーまたは読み取り専用キーを再生成します。|
|[ファイアウォールの作成](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 承認した一連のコンピューターやクラウド サービスからアカウントへのアクセスを制限する着信 IP アクセス制御ポリシーを作成します。|
|**高可用性、ディザスター リカバリー、バックアップと復元**||
|[フェールオーバー ポリシーの構成](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|アカウントのレプリケート先の各リージョンのフェールオーバー優先順位を設定します。|
|||
