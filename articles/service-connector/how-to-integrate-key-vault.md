---
title: Azure Key Vault を Service Connector で統合する
description: Service Connector を使用して、Azure Key Vault をアプリケーションに統合する
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7b46f9c0080fb13f178ec1dca96fb49316c2f617
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089751"
---
# <a name="integrate-azure-key-vault-with-service-connector"></a>Azure Key Vault を Service Connector で統合する

このページでは、Service Connector を使用する Azure Key Vault のサポートされている認証の種類とクライアントの種類を示します。 Service Connector を使用しなくても、他のプログラミング言語で Azure Key Vault に接続できる場合があります。 このページには、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) も示されています。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .NET | | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Java | | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Java - Spring Boot | | | | ![[はい] アイコン](./media/green-check.png) |
| Node.js | | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |
| Python | | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="net-java-nodejs-python"></a>.NET、Java、Node.JS、Python

**システム割り当てマネージド ID**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Azure RBAC スコープ | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Key Vault エンドポイント | `https://{yourKeyVault}.vault.azure.net/` |

**ユーザー割り当てマネージド ID**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Azure RBAC スコープ | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Key Vault エンドポイント | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | クライアント ID | `{yourClientID}` |

**サービス プリンシパル**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_KEYVAULT_SCOPE | Azure RBAC スコープ | `https://management.azure.com/.default` |
| AZURE_KEYVAULT_RESOURCEENDPOINT | Key Vault エンドポイント | `https://{yourKeyVault}.vault.azure.net/` |
| AZURE_KEYVAULT_CLIENTID | クライアント ID | `{yourClientID}` |
| AZURE_KEYVAULT_CLIENTSECRET | クライアント シークレット | `{yourClientSecret}` |
| AZURE_KEYVAULT_TENANTID | テナント ID | `{yourTenantID}` |

### <a name="java---spring-boot"></a>Java - Spring Boot

**サービス プリンシパル**
| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| azure.keyvault.uri | Key Vault エンドポイント URL | `"https://{yourKeyVaultName}.vault.azure.net/"` |
| azure.keyvault.client-id | クライアント ID | `{yourClientID}` |
| azure.keyvault.client-key | クライアント シークレット | `{yourClientSecret}` |
| azure.keyvault.tenant-id |  テナント ID | `{yourTenantID}` |
| azure.keyvault.scope | Azure RBAC スコープ | `https://management.azure.com/.default` |

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
