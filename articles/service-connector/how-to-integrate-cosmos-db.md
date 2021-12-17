---
title: Azure Cosmos DB と Service Connector を統合する
description: Service Connector を使用して Azure Cosmos DB をアプリケーションに統合する
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 8bdd4a23ac0736f2ff4ddf3095f0d77b2af066ce
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373433"
---
# <a name="integrate-azure-cosmos-db-with-service-connector"></a>Azure Cosmos DB と Service Connector を統合する

このページでは、Service Connector を使用する Azure Cosmos DB でサポートされている認証の種類とクライアントの種類を示します。 Service Connector を使用しなくても、他のプログラミング言語で Azure Cosmos DB に接続できる場合があります。 このページには、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) も示されています。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .NET | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Java  | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Java - Spring Boot | | | ![[はい] アイコン](./media/green-check.png) | |
| Node.js | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Go | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="dotnet-java-nodejs-and-go"></a>Dotnet、Java、Nodejs、Go

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_COSMOS_CONNECTIONSTRING | Cosmos DB 接続文字列の Mango DB | `mongodb://{mango-db-admin-user}:{********}@{mango-db-server}.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@{mango-db-server}@` |

**システム割り当てマネージド ID**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | 接続文字列を取得するための URL | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | マネージド ID の範囲 | `https://management.azure.com/.default` |
| AZURE_COSMOS_RESOURCEENDPOINT | リソース エンドポイント| `https://{your-database-server}.documents.azure.com:443/` |

**ユーザー割り当てマネージド ID**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | 接続文字列を取得するための URL | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | マネージド ID の範囲 | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | クライアント シークレット ID | `{client-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | サブスクリプション ID | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | リソース エンドポイント| `https://{your-database-server}.documents.azure.com:443/` |

**サービス プリンシパル**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | 接続文字列を取得するための URL | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | マネージド ID の範囲 | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | クライアント シークレット ID | `{client-id}` |
| AZURE_COSMOS_CLIENTSECRET | クライアント シークレット | `{client-secret}` |
| AZURE_COSMOS_TENANTID | テナント ID | `{tenant-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | サブスクリプション ID | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | リソース エンドポイント| `https://{your-database-server}.documents.azure.com:443/` |

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
