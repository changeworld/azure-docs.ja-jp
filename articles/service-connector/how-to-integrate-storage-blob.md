---
title: Azure Blob Storage を Service Connector と統合する
description: Service Connector を使用してアプリケーションに Azure Blob Storage を統合する
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b0fa624b21125ec6cb6a319e9526f1787565a7f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089749"
---
# <a name="integrate-azure-blob-storage-with-service-connector"></a>Azure Blob Storage を Service Connector と統合する

このページには、Service Connector を使用した Azure Blob Storage のサポートされている認証の種類とクライアントの種類が表示されます。 Service Connector を使用しなくても、他のプログラミング言語で Azure Blob Storage に接続できる可能性があります。 このページには、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) も示されています。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .NET | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Java | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Java - Spring Boot | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Node.js | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Python | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="net-java-nodejs-python"></a>.NET、Java、Node.JS、Python

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_STORAGEBLOB_CONNECTIONSTRING | Blob ストレージの接続文字列 | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |

**システム割り当てマネージド ID**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Blob ストレージエンドポイント | `https://{storageAccountName}.blob.core.windows.net/` |

**ユーザー割り当てマネージド ID**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Blob ストレージエンドポイント | `https://{storageAccountName}.blob.core.windows.net/` |
| AZURE_STORAGEBLOB_CLIENTID | クライアント ID | `{yourClientID}` |

**サービス プリンシパル**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_STORAGEBLOB_RESOURCEENDPOINT | Blob ストレージエンドポイント | `https://{storageAccountName}.blob.core.windows.net/` |
| AZURE_STORAGEBLOB_CLIENTID | クライアント ID | `{yourClientID}` |
| AZURE_STORAGEBLOB_CLIENTSECRET | クライアント シークレット | `{yourClientSecret}` |
| AZURE_STORAGEBLOB_TENANTID | テナント ID | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java - Spring Boot

**Secret/ConnectionString**

| Application properties | 説明 | 値の例 |
| --- | --- | --- |
| azure.storage.account-name | Blob ストレージアカウント名 | `{storageAccountName}` |
| azure.storage.account-key | Blob ストレージアカウントキー | `{yourSecret}` |
| azure.storage.blob-endpoint | Blob storage エンドポイント | `https://{storageAccountName}.blob.core.windows.net/` |

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
