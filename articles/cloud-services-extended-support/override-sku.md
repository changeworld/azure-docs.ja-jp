---
title: Azure Cloud Services (延長サポート) の CSCFG/CSDEF で SKU 情報をオーバーライドする
description: この記事では、Azure Cloud Services (延長サポート) の .cscfg および .csdef ファイルの SKU 情報をオーバーライドする方法について説明します。
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31ff47cd4e110e62769678836bdd803b71369e0b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437881"
---
# <a name="override-sku-settings-in-cscfg-and-csdef-files-for-cloud-services-extended-support"></a>Cloud Services (延長サポート) の .cscfg および .csdef ファイルの SKU 設定をオーバーライドする

この記事では、**allowModelOverride** プロパティを使用して Azure Cloud Services のロール サイズとインスタンス数を更新する方法について説明します。 このプロパティを使用する場合、サービス構成ファイル (.cscfg) とサービス定義ファイル (.csdef) を更新する必要はありません。 そのため、クラウド サービスを再パッケージ化して再デプロイすることなく、スケールアップ、スケールダウン、スケールイン、またはスケールアウトすることができます。

## <a name="set-the-allowmodeloverride-property"></a>allowModelOverride プロパティを設定する
**allowModelOverride** プロパティを `true` または `false` に設定できます。 
* **allowModelOverride** が `true` に設定されている場合、API 呼び出しにより、.csdef ファイルと .cscfg ファイルの値が検証されることなく、クラウド サービスのロール サイズとインスタンス数が更新されます。 
   > [!Note]
   > .cscfg ファイルは、ロール インスタンス数を反映するように更新されます。 (.cspkg 内に埋め込まれている) .csdef ファイルは、古い値を保持します。

* **allowModelOverride** が `false` に設定されている場合、ロール サイズとインスタンス数の値がそれぞれ .csdef ファイルと .cscfg ファイルの値と一致しないと、API 呼び出しでエラーがスローされます。

既定値は `false` です。 このプロパティが `true` に設定された後に `false` にリセットされた場合は、.csdef ファイルと .cscfg ファイルが再度検証されます。

次のサンプルは、Azure Resource Manager (ARM) テンプレート、PowerShell、または SDK を使用して **allowModelOverride** プロパティを設定する方法を示しています。

### <a name="arm-template"></a>ARM テンプレート
ここで **allowModelOverride** プロパティを `true` に設定すると、`roleProfile` セクションで定義されたロール プロパティを使用してクラウド サービスが更新されます。
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
新しい `New-AzCloudService` コマンドレットで `AllowModelOverride` スイッチを設定すると、ロール プロファイルで定義されている SKU プロパティを使用してクラウド サービスが更新されます。
```powershell
New-AzCloudService ` 
-Name "ContosoCS" ` 
-ResourceGroupName "ContosOrg" ` 
-Location "East US" `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
`AllowModelOverride` 変数を `true` に設定すると、ロール プロファイルで定義されている SKU プロパティを使用してクラウド サービスが更新されます。

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate("ContosOrg", "ContosoCS", cloudService);
```
### <a name="azure-portal"></a>Azure portal
Azure portal では、**allowModelOverride** プロパティを使用して .csdef ファイルと .cscfg ファイルのロール サイズとインスタンス数を上書きすることはできません。 


## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) の[よく寄せられる質問](faq.yml)を確認します。
