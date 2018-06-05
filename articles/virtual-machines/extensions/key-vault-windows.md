---
title: Windows の Key Vault VM 拡張機能 | Microsoft Docs
description: 仮想マシンの拡張機能を使用して、仮想マシンに Key Vault シークレットの自動更新を実行するエージェントをデプロイします。
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944754"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Windows 用の Key Vault 仮想マシン拡張機能

## <a name="overview"></a>概要

Key Vault VM 拡張機能は、Azure Key Vault に保存されているシークレットの自動更新を行います。 この拡張機能は、具体的には、Key Vault に保存されている観察対象の証明書の一覧を監視し、変更の検出時に取得し、対応する証明書をインストールします。 Key Vault VM 拡張機能は、現時点では Windows VM で、Microsoft によって公開されサポートされます。 このドキュメントでは、Windows 用の Key Vault VM 拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。 

## <a name="prerequisites"></a>前提条件

Key Vault VM 拡張機能は、Key Vault サービスに対して自身を認証するために、マネージド サービス ID の VM 拡張機能に依存します。 詳細については、MSI VM 拡張機能のドキュメントをご覧ください。

### <a name="operating-system"></a>オペレーティング システム

Key Vault VM 拡張機能は、現在はすべての Windows バージョンをサポートしています。

| ディストリビューション | バージョン |
|---|---|
| Windows | コア |

### <a name="internet-connectivity"></a>インターネット接続

Windows 用の Key Vault VM 拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Key Vault VM 拡張機能のスキーマを示しています。 この拡張機能では、保護された設定は必要ありません。すべての設定がセキュリティに影響を与えない情報と見なされます。 この拡張機能には、監視対象のシークレット、ポーリング頻度、および宛先の証明書ストアの一覧が必要です。 具体的には次の処理が行われます。  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | date |
| publisher | Microsoft.Azure.KeyVault.Edp | 文字列 |
| type | KeyVaultForWindows | 文字列 |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | 文字列 |
| certificateStoreLocation  | LOCAL_MACHINE | 文字列 |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | 文字列配列


## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の証明書の更新が必要な仮想マシンを 1 つ以上デプロイするときに最適です。 この拡張機能は、個々 の VM または VM スケールセットにデプロイできます。 スキーマと構成は、両方のテンプレートの種類に共通です。 

仮想マシン拡張機能の JSON の構成は、テンプレートの仮想マシン リソースのフラグメント (具体的には、仮想マシンの `"resources": []` オブジェクト) 内でネストする必要があります。

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Azure PowerShell でのデプロイ

Azure PowerShell を使用すると、Key Vault VM 拡張機能を既存の仮想マシンまたは仮想マシンのスケールセットにデプロイすることができます。 

* VM 拡張機能をデプロイするには、次の手順を実行します。
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 拡張機能を、たとえば Service Fabric クラスターの一部として VM スケールセットにデプロイする場合:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

次の制限/要件に注意してください。
- デプロイは米国中南部リージョンで行う必要があります
- Key Vault の制限:
    - デプロイ時に存在している必要があります 
    - デプロイと同じリージョンおよびリソース グループに存在する必要があります
    - デプロイとテンプレートのデプロイが有効になっている必要があります

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI での Key Vault VM 拡張機能のデプロイのサンプルは、まもなく利用できるようになります。 

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell を使用します。 特定の VM での拡張機能のデプロイ状態を確認するには、Azure PowerShell を使用して次のコマンドを実行します。

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
