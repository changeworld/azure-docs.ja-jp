---
title: Azure Cosmos DB のロールベースのアクセス制御
description: Azure Cosmos DB が Active Directory 統合 (RBAC) を使用してデータベース保護を提供する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: cbb97dd260e5aee53595afc24e577ce08334e2b2
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027020"
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

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Azure portal のアクセス制御 (IAM) - データベース セキュリティを示す":::

## <a name="custom-roles"></a>カスタム ロール

組み込みのロールに加えて、ユーザーは Azure で[カスタム ロール](../role-based-access-control/custom-roles.md)を作成し、それらのロールを Active Directory テナント内のすべてのサブスクリプションにわたるサービス プリンシパルに適用することもできます。 カスタム ロールによって、ユーザーは、カスタムのリソース プロバイダー操作セットを使用して RBAC ロール定義を作成できるようになります。 Azure Cosmos DB のカスタム ロールを構築するために使用できる操作の詳細については、「[Azure Cosmos DB のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)」を参照してください。

## <a name="preventing-changes-from-cosmos-sdk"></a>Cosmos SDK からの変更の防止

> [!WARNING]
> この機能を有効にすると、アプリケーションに危険な影響を与える可能性があります。 この機能を有効にする前に、十分にお読みください。

Azure Cosmos DB リソース プロバイダーをロックして、リソースが、アカウント キーを使用して接続している任意のクライアント (つまり Cosmos SDK 経由で接続しているアプリケーション) から変更されないようにすることができます。 これには、Azure portal からの変更も含まれます。 これは、運用環境でより高いレベルの制御とガバナンスを必要とし、リソース ロックなどの機能を有効にし、コントロール プレーン操作の診断ログを有効にするユーザーに適しています。 Cosmos DB SDK を介して接続しているクライアントは、Cosmos アカウント、データベース、コンテナー、スループットのプロパティを変更できません。 Cosmos コンテナー自体に対するデータの読み取りと書き込みに関連する操作は影響を受けません。

設定した場合、リソースへの変更を行うことができるのは、適切な RBAC ロールを持つユーザーと、管理サービス ID を含む Azure Active Directory 資格情報だけです。

### <a name="check-list-before-enabling"></a>有効にする前のチェック リスト

この設定により、アカウント キーを使用して接続するすべてのクライアント (すべての Cosmos DB SDK を含む)、アカウント キーを介して接続するすべてのツール、または Azure portal から、Cosmos リソースに変更を加えることができなくなります。 この機能を有効にした後にアプリケーションからの問題またはエラーを回避するには、この機能を有効にする前に、アプリケーションまたは Azure portal ユーザーが次の操作を実行するかどうかを確認します。

- プロパティを含めたり、リージョンを追加または削除したりする Cosmos アカウントに対する変更。

- データベースやコンテナーなどの子リソースの作成、削除。 これには、Cassandra、MongoDB、Gremlin、テーブル リソースなど、他の API のリソースが含まれます。

- データベースまたはコンテナー レベルのリソースでのスループットの更新。

- コンテナー プロパティ (インデックス ポリシー、TTL、一意キーなど) の変更。

- ストアド プロシージャ、トリガー、またはユーザー定義関数の変更。

アプリケーションにより (または Azure portal 経由でユーザーにより) これらのいずれかの操作が実行される場合は、[ARM テンプレート](manage-sql-with-resource-manager.md)、[PowerShell](manage-with-powershell.md)、[Azure CLI](manage-with-cli.md)、[REST](/rest/api/cosmos-db-resource-provider/) または [Azure 管理ライブラリ](https://github.com/Azure-Samples/cosmos-management-net)経由で実行されるように移行する必要があります。 Azure の管理は、[複数の言語](https://docs.microsoft.com/azure/?product=featured#languages-and-tools)で使用できることに留意してください。

### <a name="set-via-arm-template"></a>ARM テンプレートを使用して設定する

ARM テンプレートを使用してこのプロパティを設定するには、既存のテンプレートを更新するか、現在のデプロイ用の新しいテンプレートをエクスポートしてから、databaseAccounts リソースのプロパティに `"disableKeyBasedMetadataWriteAccess": true` を含めます。 次に、このプロパティ設定を含む Azure Resource Manager テンプレートの基本的な例を示します。

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
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

> [!IMPORTANT]
> このプロパティを使用して再デプロイする場合は、アカウントと子リソースの他のプロパティを必ず含めてください。 このテンプレートをそのままデプロイしないでください。すべてのアカウント プロパティがリセットされます。

### <a name="set-via-azure-cli"></a>Azure CLI を使用して設定する

Azure CLI を使用して有効にするには、次のコマンドを使用します。

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>PowerShell を使用して設定する

Azure PowerShell を使用して有効にするには、次のコマンドを使用します。

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>次のステップ

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../role-based-access-control/overview.md)
- [Azure リソースのカスタム ロール](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB のデータへのアクセスをセキュリティで保護する](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
