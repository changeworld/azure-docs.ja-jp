---
title: Azure Cosmos DB のロールベースのアクセス制御
description: Azure Cosmos DB が Active Directory 統合 (RBAC) を使用してデータベース保護を提供する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4e028e7a5e7e7b8f747d7a1cfb36c553a8113544
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583726"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB のロールベースのアクセス制御

Azure Cosmos DB には、Azure Cosmos DB の一般的な管理シナリオに対応するロールベースのアクセス制御 (RBAC) が組み込まれています。 Azure Active Directory にプロファイルを持つ個人は、これらの RBAC ロールをユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てて、Azure Cosmos DB リソース上のリソースと操作へのアクセスを許可または拒否できます。 ロールの割り当ては、Azure Cosmos アカウント、データベース、コンテナー、およびオファー (スループット) へのアクセスを含む、コントロールプレーン アクセスのみに限定されています。

## <a name="built-in-roles"></a>組み込みのロール

Azure Cosmos DB でサポートされている組み込みのロールは次のとおりです。

|**組み込みのロール**  |**説明**  |
|---------|---------|
|[DocumentDB Account Contributor](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB アカウントを管理できます。|
|[Cosmos DB アカウント閲覧者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Cosmos DB アカウントのデータを読み取ることができます。|
|[Cosmos バックアップ オペレーター](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Azure Cosmos データベースまたはコンテナーの復元要求を送信できます。|
|[Cosmos DB オペレーター](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Azure Cosmos アカウント、データベース、およびコンテナーをプロビジョニングできますが、データへのアクセスに必要なキーにはアクセスできません。|

> [!IMPORTANT]
> Azure Cosmos DB の RBAC サポートは、コントロール プレーン操作にのみ適用されます。 データ プレーン操作は、マスター キーまたはリソース トークンを使用してセキュリティで保護されています。 詳細については、「[Azure Cosmos DB のデータへのアクセスをセキュリティで保護する](secure-access-to-data.md)」を参照してください。

## <a name="identity-and-access-management-iam"></a>ID およびアクセス管理 (IAM)

Azure portal の **[アクセス制御 (IAM)]** ウィンドウは、Azure Cosmos リソースに対してロールベースのアクセス制御を構成するために使用されます。 ロールは、Active Directory 内のユーザー、グループ、サービス プリンシパル、およびマネージド ID に適用されます。 組み込みのロールまたはカスタム ロールは、個人とグループに使用できます。 次のスクリーンショットは、Azure portal のアクセス制御 (IAM) を使用した Active Directory 統合 (RBAC) を示しています。

![Azure Portal でのアクセス制御 (IAM) - データベースのセキュリティ](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>カスタム ロール

組み込みのロールに加えて、ユーザーは Azure で[カスタム ロール](../role-based-access-control/custom-roles.md)を作成し、それらのロールを Active Directory テナント内のすべてのサブスクリプションにわたるサービス プリンシパルに適用することもできます。 カスタム ロールによって、ユーザーは、カスタムのリソース プロバイダー操作セットを使用して RBAC ロール定義を作成できるようになります。 Azure Cosmos DB のカスタム ロールを構築するために使用できる操作の詳細については、「[Azure Cosmos DB のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)」を参照してください。

## <a name="preventing-changes-from-cosmos-sdk"></a>Cosmos SDK からの変更の防止

Cosmos リソース プロバイダーをロック ダウンして、Cosmos アカウント、データベース、コンテナー、スループットなどのリソースが、アカウント キー (Cosmos SDK 経由で接続するアプリケーションなど) を介して接続している任意のクライアントから変更されないようにすることができます。 設定されている場合、任意のリソースに対する変更は、適切な RBAC ロールと資格情報を持つユーザーからの変更である必要があります。 この機能は、Cosmos リソース プロバイダーの `disableKeyBasedMetadataWriteAccess` プロパティ値で設定されます。 このプロパティ設定を含む Azure Resource Manager テンプレートの例は、次のとおりです。

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```
既存の Resource Manager テンプレートをエクスポートしてこのプロパティで更新すると、テンプレートの機能を完全に置き換えることができます。 そのため、すべての値が含まれていない場合は既定値にリセットされます。 キーベースのメタデータの書き込みアクセスを無効にするもう 1 つの方法は、次のコマンドに示すように、Azure CLI を使用することです。

```cli
az cosmosdb update  --name CosmosDBAccountName --resource-group ResourceGroupName  --disable-key-based-metadata-write-access true

```

## <a name="next-steps"></a>次のステップ

- [Azure リソースのロールベースのアクセス制御 (RBAC) の概要](../role-based-access-control/overview.md)
- [Azure リソースのカスタム ロール](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB のデータへのアクセスをセキュリティで保護する](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
