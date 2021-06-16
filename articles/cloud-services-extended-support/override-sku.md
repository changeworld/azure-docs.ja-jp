---
title: Azure Cloud Services (延長サポート) の CSCFG/CSDEF で SKU 情報をオーバーライドする
description: Azure Cloud Services (延長サポート) の CSCFG/CSDEF で SKU 情報をオーバーライドする
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc973f024ea93b986793a8daff8e0db8caf56e22
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696214"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Cloud Services (延長サポート) で CSCFG/CSDEF で SKU 情報をオーバーライドする 

この機能により、ユーザーは **allowModelOverride** プロパティを使用して Cloud Services のロール サイズとインスタンス数を更新できます。サービス構成ファイルとサービス定義ファイルを更新する必要はありません。これにより、再パッケージ化と再デプロイを行わずにクラウド サービスをスケール アップ/ダウン/イン/アウトできます。

## <a name="set-allowmodeloverride-property"></a>allowModelOverride プロパティを設定する
allowModelOverride プロパティは、次の方法で設定できます。
* allowModelOverride = true の場合、API 呼び出しは、csdef ファイルと cscfg ファイルの値を検証せずに、クラウド サービスのロール サイズとインスタンス数を更新します。 
> [!Note]
> ロール インスタンス数を反映するように cscfg が更新されますが、(cspkg 内の) csdef は古い値を保持します。
* allowModelOverride = false の場合、ロール サイズとインスタンス数の値がそれぞれ csdef ファイルと cscfg ファイルと一致しない場合、API 呼び出しでエラーがスローされます。

既定値は false に設定されています。 プロパティが true から false にリセットされた場合、csdef ファイルと cscfg ファイルの検証が再度確認されます。

PowerShell、テンプレート、および SDK でプロパティを適用するには、以下の例を参照してください。

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
ここでプロパティ "allowModelOverride" = true を設定すると、roleProfile セクションで定義されたロール プロパティを使用してクラウド サービスが更新されます。
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
新しい New-AzCloudService コマンドレットでスイッチ "AllowModelOverride" を設定すると、RoleProfile で定義されている SKU プロパティを使用してクラウド サービスが更新されます。
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
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
変数 AllowModelOverride= true を設定すると、RoleProfile で定義されている SKU プロパティを使用してクラウド サービスが更新されます。

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
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Azure portal
ポータルでは、上記のプロパティで、csdef と cscfg のロール サイズとインスタンス数をオーバーライドすることはできません。 


## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
