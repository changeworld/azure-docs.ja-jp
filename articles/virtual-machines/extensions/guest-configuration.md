---
title: Azure Policy ゲスト構成拡張機能
description: 仮想マシン内の設定を監査または構成するために使用される拡張機能について説明します。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368493"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Azure Policy ゲスト構成拡張機能の概要

ゲスト構成拡張機能は、仮想マシン内で監査および構成操作を実行するコンポーネント Azure Policy です。
[Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) および [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) のセキュリティ ベースライン定義などのポリシーでは、拡張機能がインストールされるまで、マシン内の設定を確認することはできません。

## <a name="prerequisites"></a>前提条件

マシンがゲスト構成サービスに対して認証されるためには、マシンに[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) が必要です。
次のプロパティが設定されている場合、仮想マシンの ID 要件が満たされます。

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>オペレーティング システム

ゲスト構成拡張機能のサポートは、[エンドツーエンドのソリューションに関するドキュメントで説明](../../governance/policy/concepts/guest-configuration.md#supported-client-types)されているオペレーティング システムのサポートと同じです。

### <a name="internet-connectivity"></a>インターネット接続

ゲスト構成拡張機能によってインストールされるエージェントは、ゲスト構成の割り当てによってリストされたコンテンツ パッケージに到達し、ゲスト構成サービスに状態を報告できる必要があります。
マシンは、TCP ポート 443 を介した送信方向の HTTPS を使用して、またはプライベート ネットワークを介して接続が提供されている場合に接続できます。
プライベート ネットワーキングの詳細については、次の記事をご覧ください。

- [ゲスト構成、Azure でプライベート リンクを介して通信する](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Azure Storage のプライベート エンドポイントを使用する](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>拡張機能をインストールするにはどうすればよいですか。

ID 要件を含む大規模な最新バージョンの拡張機能をデプロイするには、Azure Policy 「[仮想マシンでゲスト構成ポリシーを有効にするための前提条件をデプロイする](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)」を割り当てます。
個々のマシンの場合、拡張機能は、Azure CLI、PowerShell、Resource Manager テンプレート、またはサードパーティ製のツールを使用して展開できます。

上記で参照されているポリシーにはこれらの特定の文字列が必要なため、拡張機能のインスタンス名は "AzurePolicyforWindows" または "AzurePolicyforLinux" に設定する必要があります。

既定では、すべてのデプロイが最新バージョンに更新されます。 プロパティ _autoUpgradeMinorVersion_ の値は、特に指定がない限り、既定で "true" に設定されます。 拡張機能の新しいバージョンがリリースされても、コードの更新について心配する必要はありません。

### <a name="azure-cli"></a>Azure CLI

Linux 用の拡張機能をデプロイするには、次のようにします。


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

Windows 用の拡張機能をデプロイするには、次のようにします。

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

Linux 用の拡張機能をデプロイするには、次のようにします。

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

Windows 用の拡張機能をデプロイするには、次のようにします。

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Resource Manager テンプレート

Linux 用の拡張機能をデプロイするには、次のようにします。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Windows 用の拡張機能をデプロイするには、次のようにします。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

Linux 用の拡張機能をデプロイするには、次のようにします。

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

Windows 用の拡張機能をデプロイするには、次のようにします。

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>設定

拡張機能に設定や protected-settings プロパティを含める必要はありません。
このような情報はすべて、エージェントによって[ゲスト構成割り当て](/rest/api/guestconfiguration/guestconfigurationassignments)リソースから取得されます。 たとえば、[ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation)、[Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)、および [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) プロパティはそれぞれ、VM 拡張機能ではなく、構成ごとに管理されます。

## <a name="next-steps"></a>次のステップ

* Azure Policy のゲスト構成の詳細については、「[Azure Policy のゲストの構成の理解](../../governance/policy/concepts/guest-configuration.md)」を参照してください。
* Linux エージェントと拡張機能のしくみの詳細については、[Linux 用の Azure VM 拡張機能とその機能](features-linux.md)に関する記事を参照してください。
* Windows ゲスト エージェントと拡張機能のしくみの詳細については、[Windows 用の Azure VM 拡張機能とその機能](features-windows.md)に関する記事を参照してください。  
* Windows ゲスト エージェントをインストールする場合は、[Azure Windows 仮想マシン エージェントの概要](agent-windows.md)に関するページをご覧ください。  
* Linux エージェントをインストールする場合は、[Azure Linux 仮想マシン エージェントの概要](agent-linux.md)に関するページをご覧ください。  
