---
title: Azure Cosmos DB アカウントのカスタマー マネージド キーを構成する
description: Azure Key Vault で Azure Cosmos DB アカウントのカスタマー マネージド キーを構成する方法について説明します。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 44bbd7eab80ecb1cbfef9738e42b4070dff31180
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77506050"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Azure Key Vault で Azure Cosmos アカウントのカスタマー マネージド キーを構成する

> [!NOTE]
> 現時点では、この機能を使用するにはアクセスを要求する必要があります。 それを行うには、[azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) にお問い合わせください。

Azure Cosmos アカウントに格納されているデータは、自動的かつシームレスに暗号化されます。 Azure Cosmos DB には、保存データの暗号化に使用されるキーを管理するための次の 2 つのオプションが用意されています。

- **サービスが管理するキー**: 既定では、お使いの Azure Cosmos アカウントのデータの暗号化に使用するキーは Microsoft が管理します。

- **カスタマー マネージド キー (CMK)** :必要に応じ、お使いのご自分のキーに 2 番目の暗号化レイヤーの追加を選択できます。

カスタマー マネージド キーは [Azure Key Vault](../key-vault/key-vault-overview.md) に格納し、カスタマー マネージド キーが有効になっている Azure Cosmos アカウントごとにキーを指定する必要があります。 このキーは、そのアカウントに格納されているすべてのデータを暗号化するために使用されます。

> [!NOTE]
> 現在、カスタマー マネージド キーは新しい Azure Cosmos アカウントでのみ使用できます。 これらは、アカウントの作成時に構成します。

## <a id="register-resource-provider"></a> Azure サブスクリプション用の Azure Cosmos DB リソース プロバイダーを登録する

1. [Azure portal](https://portal.azure.com/) にサインインし、お使いの Azure サブスクリプションに移動して **[設定]** タブの **[リソース プロバイダー]** を選択します。

   ![左側のメニューの [リソース プロバイダー] エントリ](./media/how-to-setup-cmk/portal-rp.png)

1. **Microsoft.DocumentDB** リソース プロバイダーを検索します。 そのリソース プロバイダーが既に登録済みとしてマークされているどうかを確認します。 そうでない場合は、リソース プロバイダーを選択して **[登録]** を選択します。

   ![Microsoft.DocumentDB リソース プロバイダーの登録](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault インスタンスを構成する

Azure Cosmos DB でカスタマー マネージド キーを使用するには、暗号化キーをホストするために使用しようとしている Azure Key Vault インスタンスで 2 つのプロパティを設定する必要があります。 これらのプロパティには、 **[論理的な削除]** と **[消去しない]** が含まれます。 これらのプロパティは、既定では有効になっていません。 これらは、PowerShell または Azure CLI のいずれかを使用して有効にします。

既存の Azure Key Vault インスタンスでこれらのプロパティを有効にする方法については、次のいずれかの記事の「論理的な削除を有効にする」と「消去保護を有効にする」のセクションを参照してください。

- [PowerShell で論理的な削除を使用する方法](../key-vault/key-vault-soft-delete-powershell.md)
- [Azure CLI で論理的な削除を使用する方法](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure Key Vault インスタンスにアクセス ポリシーを追加する

1. Azure portal から、暗号化キーをホストするために使用しようとしている Azure Key Vault インスタンスに移動します。 左側のメニューの **[アクセス ポリシー]** を選択します。

   ![左側のメニューの [アクセス ポリシー]](./media/how-to-setup-cmk/portal-akv-ap.png)

1. **[+ アクセス ポリシーの追加]** を選択します。

1. **[キーのアクセス許可]** ドロップダウン メニューで、 **[取得]** 、 **[キーの折り返しを解除]** 、および **[キーを折り返す]** アクセス許可を選択します。

   ![適切なアクセス許可の選択](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. **[プリンシパルの選択]** で、 **[選択されていません]** を選択します。 次に、**Azure Cosmos DB** プリンシパルを検索して選択します (検索しやすくするために、Azure Government リージョンを除くすべての Azure リージョンはプリンシパル ID: `a232010e-820c-4083-83bb-3ace5fc29d0b`、Azure Government リージョンはプリンシパル ID: `57506a73-e302-42a9-b869-6f12d9ec29e9` を使用して検索することもできます)。 最後に、一番下の **[選択]** を選択します。 **Azure Cosmos DB** プリンシパルが一覧にない場合は、この記事の[リソース プロバイダーの登録](#register-resource-provider)に関するセクションの説明に従って **Microsoft.DocumentDB** リソース プロバイダーを再登録することが必要になる場合があります。

   ![Azure Cosmos DB プリンシパルを選択する](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. **[追加]** を選択して新しいアクセス ポリシーを追加します。

## <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault でキーを生成する

1. Azure portal から、暗号化キーをホストするために使用しようとしている Azure Key Vault インスタンスに移動します。 次に、左側のメニューの **[キー]** を選択します。

   ![左側のメニューの [キー] エントリ](./media/how-to-setup-cmk/portal-akv-keys.png)

1. **[生成/インポート]** を選択し、新しいキーに名前を付け、RSA キー サイズを選択します。 最高のセキュリティを得るには、最小で 3072 をお勧めします。 次に、 **[作成]** を選択します。

   ![新しいキーを作成する](./media/how-to-setup-cmk/portal-akv-gen.png)

1. キーが作成されたら、新しく作成されたキーを選択し、次にその現在のバージョンを選択します。

1. 最後のスラッシュの後の部分を除き、キーの **[キー識別子]** をコピーします。

   ![キーのキー識別子のコピー](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>新しい Azure Cosmos アカウントを作成する

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure portal から新しい Azure Cosmos DB アカウントを作成する場合は、 **[暗号化]** の手順で **[カスタマー マネージド キー]** を選択します。 **[キー URI]** フィールドで、前の手順でコピーした Azure Key Vault キーの URI/キー識別子を貼り付けます。

![Azure portal での CMK パラメーターの設定](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

PowerShell で新しい Azure Cosmos DB アカウントを作成する場合、次を実行します。

- 前に **PropertyObject** の **keyVaultKeyUri** プロパティでコピーした Azure Key Vault キーの URI を渡します。

- API バージョンとして **2019-12-12** を使用します。

> [!IMPORTANT]
> アカウントがカスタマー マネージド キーで正常に作成されるようにするには、`Location` パラメーターを明示的に設定する必要があります。

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使用して新しい Azure Cosmos アカウントを作成する場合、次を実行します。

- 前に **properties** オブジェクトの **keyVaultKeyUri** プロパティでコピーした Azure Key Vault キーの URI を渡します。

- API バージョンとして **2019-12-12** を使用します。

> [!IMPORTANT]
> アカウントがカスタマー マネージド キーで正常に作成されるようにするには、`Location` パラメーターを明示的に設定する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

次の PowerShell スクリプトを使用してテンプレートをデプロイします。

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>カスタマー マネージド キーを使用する場合、何からの追加料金は発生しますか?

はい。 カスタマー マネージド キーでのデータの暗号化と暗号化の解除の管理で増えるコンピューティング負荷に対応するために、Azure Cosmos アカウントに対して実行されるすべての操作に対し 25% 多く[要求ユニット](./request-units.md)が消費されます。

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>カスタマー マネージド キーでどのようなデータが暗号化されますか?

カスタマー マネージド キーでは、次のメタデータを除き、ご自分の Azure Cosmos アカウントに格納されているすべてのデータが暗号化されます。

- Azure Cosmos DB [アカウント、データベース、およびコンテナー](./account-overview.md#elements-in-an-azure-cosmos-account)の名前

- [ストアド プロシージャ](./stored-procedures-triggers-udfs.md)の名前

- [インデックス作成ポリシー](./index-policy.md)で宣言されているプロパティ パス

- お使いのコンテナーの[パーティション キー](./partitioning-overview.md)の値

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>カスタマー マネージド キーは既存の Azure Cosmos アカウントでサポートされますか?

この機能は現在、新しいアカウントでのみ使用できます。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>アカウント レベルのキーより細かい粒度をサポートする計画はありますか?

現時点ではありませんが、コンテナー レベルのキーが検討されています。

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>カスタマー マネージド キーはバックアップにどのように影響しますか?

Azure Cosmos DB は、アカウントに格納されているデータの[定期的な自動バックアップ](./online-backup-and-restore.md)を取得します。 この操作では、暗号化されたデータがバックアップされます。 復元されたバックアップを使用するには、バックアップの時点で使用していた暗号化キーが必要です。 つまり、失効されておらず、バックアップの時点で使用していたキーのバージョンが依然有効になっている必要があります。

### <a name="how-do-i-revoke-an-encryption-key"></a>暗号化キーを失効させるにはどうすればよいですか?

キーの失効は、そのキーの最新バージョンを無効にすることによって行われます。

![キーのバージョンを無効にする](./media/how-to-setup-cmk/portal-akv-rev2.png)

あるいは、Azure Key Vault インスタンスからすべてのキーを失効させるために、Azure Cosmos DB プリンシパルに付与されているアクセス ポリシーを削除することもできます。

![Azure Cosmos DB プリンシパルのアクセス ポリシーの削除](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>カスタマー マネージド キーが失効した後、どのような操作を使用できますか?

暗号化キーが失効しているときに使用できる唯一の操作はアカウントの削除です。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB でのデータの暗号化](./database-encryption-at-rest.md)について学習します。
- [Cosmos DB 内のデータへのセキュリティで保護されたアクセス](secure-access-to-data.md)の概要を理解します。
