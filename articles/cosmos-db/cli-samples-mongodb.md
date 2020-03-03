---
title: Azure Cosmos DB MongoDB API 用の Azure CLI サンプル
description: Azure Cosmos DB MongoDB API 用の Azure CLI サンプル
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524565"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB MongoDB API 用の Azure CLI サンプル

次の表には、Azure Cosmos DB MongoDB API 用の Azure CLI サンプル スクリプトへのリンクが含まれています。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI リファレンス](/cli/azure/cosmosdb)で確認できます。 Azure Cosmos DB CLI のすべてのサンプル スクリプトについては、[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)をご覧ください。

> [!NOTE]
> 現在、PowerShell、CLI、および Resource Manager テンプレートを使用して、MongoDB アカウント用の Azure Cosmos DB の API の 3.2 バージョン (つまり、`*.documents.azure.com` 形式のエンドポイントを使用するアカウント) のみを作成できます。 アカウントの 3.6 バージョンを作成するには、代わりに Azure portal を使用します。

| |  |
|---|---|
| [Azure Cosmos アカウント、データベース、およびコレクションを作成する](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| MongoDB API 用の Azure Cosmos DB アカウント、データベース、およびコレクションを作成します。 |
| [スループットを変更する](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | データベースとコレクションの RU/秒を更新します。|
| [リージョンを追加またはフェールオーバーする](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | リージョンの追加、フェールオーバー優先度の変更、手動フェールオーバーのトリガーを行います。|
| [アカウント キーと接続文字列](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | アカウント キーと読み取り専用キーの表示、キーの再生成、および接続文字列の表示を行います。|
| [IP ファイアウォールを使用してセキュリティ保護する](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| IP ファイアウォールが構成された Cosmos アカウントを作成します。|
| [サービス エンドポイントを使用して新しいアカウントをセキュリティ保護する](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Cosmos アカウントを作成し、サービス エンドポイントを使用してセキュリティ保護します。|
| [サービス エンドポイントを使用して既存のアカウントをセキュリティ保護する](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| サブネットが最終的に構成されるときに、サービス エンドポイントを使用してセキュリティ保護されるように Cosmos アカウントを更新します。|
|||
