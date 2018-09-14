---
ms.assetid: ''
title: Azure Key Vault のファイアウォールと仮想ネットワークを構成する
description: Key Vault のファイアウォールと仮想ネットワークを構成する手順
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: 6315434c1e8acc82e02f5c9e5ae8ab2d1cacc887
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302055"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault のファイアウォールと仮想ネットワークを構成する

このガイドでは、キー コンテナーへのアクセスを制限するために、Key Vault のファイアウォールと仮想ネットワークを構成する手順について説明します。 [Key Vault の仮想ネットワーク サービス エンドポイント](key-vault-overview-vnet-service-endpoints.md)を使用すると、キー コンテナーから指定した仮想ネットワークや、一連の IPv4 (インターネット プロトコル バージョン 4) アドレス範囲に対するアクセスを制限することができます。

> [!IMPORTANT]
> ファイアウォールと仮想ネットワークのルールが有効になると、発信元の要求が許可された仮想ネットワークまたは IPV4 アドレス範囲から送信された場合にのみ、すべての Key Vault [データ プレーン](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)の操作が実行されます。 これは、Azure portal からキー コンテナーにアクセスする場合にも適用されます。 ユーザーは Azure portal からキー コンテナーを参照できますが、クライアント マシンが許可リストに登録されていない場合、キー/シークレット/証明書を一覧表示できない場合があります。 これは、他の Azure サービスによる 'Key Vault の選択機能' にも影響します。 ファイアウォール ルールでクライアント マシンが許可されていない場合、ユーザーはキー コンテナーを一覧表示できても、キーは一覧表示できないことがあります。

## <a name="azure-portal"></a>Azure ポータル

1. セキュリティで保護するキー コンテナーに移動します。
2. **[ファイアウォールと仮想ネットワーク]** をクリックします。
3. **[許可するアクセス元]** の **[選択されたネットワーク]** をクリックします。
4. 既存の仮想ネットワークをファイアウォールと仮想ネットワークのルールに追加するには、**[+ 既存の仮想ネットワークを追加]** をクリックします。
5. ポップアップ表示される新しいブレードで、このキー コンテナーへのアクセスを許可するサブスクリプション、仮想ネットワーク、サブネットを選択します。 選択した仮想ネットワークとサブネットで、サービス エンドポイントが有効ではない場合、"The following networks don't have service endpoints enabled..." (次のネットワークには ... で有効にされたサービス エンドポイントがありません) というメッセージが表示されます。 表示されている仮想ネットワークとサブネットのサービス エンドポイントを有効にすることを確認したら、**[有効にする]** をクリックします。 反映されるまでに最大 15 分かかることがあります。
6. **[+ 新しい仮想ネットワークを追加]** をクリックし、プロンプトに従って新しい仮想ネットワークとサブネットを追加し、新しく作成した仮想ネットワークとサブネットのサービス エンドポイントを有効にすることもできます。
7. **[IP ネットワーク]** では、[CIDR (Classless Inter-Domain Routing) 表記](https://tools.ietf.org/html/rfc4632)で IPv4 アドレスの範囲を入力して IPv4 アドレス範囲を追加するか、個々の IP アドレスを追加することができます。
8. **[Save]** をクリックします。

## <a name="azure-cli-20"></a>Azure CLI 2.0

1. [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) をインストールして[ログイン](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)します。

2. 使用できる仮想ネットワーク ルールを一覧表示します。このキー コンテナーにルールを設定していない場合、一覧は空になります。
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. 既存の仮想ネットワークとサブネット上の Key Vault のサービス エンドポイントを有効にします
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. 仮想ネットワークとサブネットのネットワーク ルールを追加します
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. トラフィックを許可する発信元の IP アドレスの範囲を追加します
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. すべての信頼できるサービスがこのキー コンテナーにアクセスできるようにする必要がある場合は、AzureServices に 'バイパス' を設定します
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. 最後の、そして重要な手順は、既定のアクションを '拒否' に設定してネットワーク ルールをオンにすることです
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. 最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) をインストールして[ログイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。

2. 使用できる仮想ネットワーク ルールを一覧表示します。このキー コンテナーにルールを設定していない場合、一覧は空になります。
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. 既存の仮想ネットワークとサブネット上の Key Vault のサービス エンドポイントを有効にします
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. 仮想ネットワークとサブネットのネットワーク ルールを追加します
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. トラフィックを許可する発信元の IP アドレスの範囲を追加します
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. すべての信頼できるサービスがこのキー コンテナーにアクセスできるようにする必要がある場合は、AzureServices に 'バイパス' を設定します
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. 最後の、そして重要な手順は、既定のアクションを '拒否' に設定してネットワーク ルールをオンにすることです
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>参照

* Azure CLI 2.0 コマンド - [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell コマンドレット - [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault)、[Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule)、[Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule)、[Update-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>次の手順

* [Key Vault の仮想ネットワーク サービス エンドポイント](key-vault-overview-vnet-service-endpoints.md)
* [キー コンテナーのセキュリティ保護](key-vault-secure-your-key-vault.md)