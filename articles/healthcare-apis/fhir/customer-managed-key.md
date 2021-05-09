---
title: Azure API for FHIR のカスタマー マネージド キーを構成する
description: Azure API for FHIR で Cosmos DB を通じてサポートされる Bring Your Own Key 機能
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 05/04/2021
ms.author: ginle
ms.openlocfilehash: e1f5159ae192d4be7aa683b68c6a994725089a7f
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2021
ms.locfileid: "108756865"
---
# <a name="configure-customer-managed-keys-at-rest"></a>保存状態のカスタマー マネージド キーを構成する

新しい Azure API for FHIR アカウントを作成すると、既定では、Microsoft マネージド キーを使用してデータが暗号化されます。 これで、独自のキーを使用して第 2 のデータ暗号化レイヤーを追加できるようになります。このキーは、自分で選んで管理することが可能です。

Azure では通常、これを実現するために、お客様の Azure Key Vault にある暗号化キーが使用されます。 現在、その機能が提供される例として、Azure SQL、Azure Storage、Cosmos DB があります。 Azure API for FHIR は、このサポートを Cosmos DB から利用します。 アカウントを作成するときに、Azure Key Vault キーの URI を指定することもできます。 このキーは、DB アカウントのプロビジョニング時に、Cosmos DB に渡されます。 FHIR 要求が送信されると、Cosmos DB はキーを取得し、それを使用してデータの暗号化または暗号化解除を実行します。 

開始するには、次のリンクを参照してください。

- [Azure サブスクリプション用の Azure Cosmos DB リソース プロバイダーを登録する](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Azure Key Vault インスタンスを構成する](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Azure Key Vault インスタンスにアクセス ポリシーを追加する](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)
- [Azure Key Vault でキーを生成する](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Azure Portal の使用

Azure portal で FHIR アカウント用の Azure API を作成すると、[**追加の設定**] タブの [**データベースの設定**] の [データの **暗号化**] 構成オプションが表示されます。既定では、[サービスによって管理されるキー] オプションが選択されます。

> [!Important]
> Data encryption オプションは、FHIR 用の Azure API が作成された場合にのみ使用でき、その後は変更できません。 ただし、[ **顧客が管理するキー** ] オプションが選択されている場合は、暗号化キーを表示および更新できます。 


キーの選択から、自分のキーを選択することもできます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="キーの選択":::

また、[ **ユーザーが管理するキー** ] オプションを選択して、Azure Key Vault キーを指定することもできます。
 
キー URI は次のように入力することもできます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API for FHIR の作成":::

> [!Important]
> Azure Key Vault のすべてのアクセス許可が適切に設定されていることを確認します。 詳細については、「 [アクセスポリシーを Azure Key Vault インスタンスに追加する](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#add-access-policy)」を参照してください。 また、Key Vault のプロパティで、論理的な削除が有効になっていることを確認します。 これらの手順を完了しないと、デプロイエラーが発生します。 詳細については、「 [キーコンテナーで論理的な削除が有効になっていることを確認し、論理削除を有効にする](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal#verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete)」を参照してください。

既存の FHIR アカウントの場合は、次に示すように、[**データベース**] ブレードでキー暗号化の選択 (**サービス管理キー** または **顧客管理キー**) を表示できます。 選択した構成オプションは変更できません。 ただし、キーを変更したり更新したりすることはできます。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="[データベース]":::

さらに、指定したキーの新しいバージョンを作成することができます。その後、サービスが中断されることなく、既存のデータはその新しいバージョンで暗号化されます。 また、キーへのアクセス権を削除することで、データへのアクセス権を削除することもできます。 キーが無効になると、クエリがエラーになります。 キーが再度有効になると、クエリが再び正常に実行されるようになります。

## <a name="using-azure-powershell"></a>Azure PowerShell の使用

Azure Key Vault キー URI を使用して、次の PowerShell コマンドを実行することで、CMK を構成できます。

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Azure CLI の使用

PowerShell の方法と同様に、`key-vault-key-uri` パラメーターで Azure Key Vault キー URI を渡し、次の CLI コマンドを実行することで、CMK を構成できます。 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

**properties** オブジェクトの **keyVaultKeyUri** プロパティで Azure Key Vault キー URI を渡すことで、CMK を構成できます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

次の PowerShell スクリプトを使用して、テンプレートをデプロイできます。

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

## <a name="next-steps"></a>次のステップ

この記事では、Azure portal、PowerShell、CLI、および Resource Manager テンプレートを使用して、保存時にお客様が管理するキーを構成する方法について説明しました。 詳細については、Azure Cosmos DB FAQ のセクションを参照してください。 
 
>[!div class="nextstepaction"]
>[Cosmos DB: カスタマー マネージド キーを設定する方法](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)