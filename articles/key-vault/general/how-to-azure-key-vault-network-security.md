---
title: Azure Key Vault のネットワーク構成を構成する方法
description: Key Vault のファイアウォールと仮想ネットワークを構成する手順
services: key-vault
author: sebansal
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 5/11/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: d6035436a005012cf67a46302213f79b2dce1f59
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2021
ms.locfileid: "109850192"
---
# <a name="configure-azure-key-vault-networking-settings"></a>Azure Key Vault のネットワーク設定を構成する

この記事では、Azure Key Vault を他のアプリケーションや Azure サービスと連携できるように、ネットワーク設定を構成する方法について説明します。 さまざまなネットワーク セキュリティ構成の詳細については、[こちらを参照](network-security.md)してください。

ここでは、Azure portal、Azure CLI、および Azure PowerShell を使用して Key Vault のファイアウォールと仮想ネットワークを構成する手順について説明します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)


1. セキュリティで保護するキー コンテナーに移動します。
2. **[ネットワーク]** を選択してから、 **[ファイアウォールと仮想ネットワーク]** タブを選択します。
3. **[許可するアクセス元]** の **[選択されたネットワーク]** を選択します。
4. 既存の仮想ネットワークをファイアウォールと仮想ネットワークの規則に追加するには、 **[+ 既存の仮想ネットワークを追加]** を選択します。
5. 表示される新しいブレードで、このキー コンテナーへのアクセスを許可するサブスクリプション、仮想ネットワーク、サブネットを選択します。 選択する仮想ネットワークとサブネットでサービス エンドポイントが有効になっていない場合は、サービス エンドポイントを有効にする必要があることを確認して、 **[有効]** を選択します。 有効になるまでに最大 15 分かかることがあります。
6. **[IP ネットワーク]** では、[CIDR (Classless Inter-Domain Routing) 表記](https://tools.ietf.org/html/rfc4632)で IPv4 アドレスの範囲を入力して IPv4 アドレス範囲を追加するか、個々の IP アドレスを追加します。
7. 信頼された Microsoft サービスが Key Vault ファイアウォールをバイパスすることを許可する場合には、[はい] を選択します。 Key Vault で現在信頼されているサービスの完全な一覧については、次のリンクを参照してください。 [Azure Key Vault の信頼済みサービス](./overview-vnet-service-endpoints.md#trusted-services)
7. **[保存]** を選択します。

**[+ 新しい仮想ネットワークを追加]** を選択し、新しい仮想ネットワークとサブネットを追加して、新しく作成した仮想ネットワークとサブネットのサービス エンドポイントを有効にすることもできます。 その後、プロンプトに従います。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します

1. [Azure CLI をインストール](/cli/azure/install-azure-cli)して[サインイン](/cli/azure/authenticate-azure-cli)します。

2. 使用可能な仮想ネットワーク規則の一覧を表示します。 このキー コンテナーに対してルールを何も設定していない場合、一覧は空になります。
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. 既存の仮想ネットワークとサブネット上の Key Vault のサービス エンドポイントを有効にします。
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. そこから送信されたトラフィックを許可する IP アドレス範囲を追加します。
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. すべての信頼されたサービスでこのキー コンテナーにアクセスできるようにする必要がある場合は、`bypass` を `AzureServices` に設定します。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. 既定のアクションを `Deny` に設定することによって、ネットワーク ルールを有効にします。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell を使用して Key Vault ファイアウォールと仮想ネットワークを構成する方法を次に示します。

1. 最新の [Azure PowerShell](/powershell/azure/install-az-ps) をインストールして[サインイン](/powershell/azure/authenticate-azureps)します。

2. 使用可能な仮想ネットワーク規則の一覧を表示します。 このキー コンテナーに対してルールを何も設定していない場合、一覧は空になります。
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. 既存の仮想ネットワークとサブネット上の Key Vault のサービス エンドポイントを有効にします。
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. 仮想ネットワークとサブネットに対するネットワーク ルールを追加します。
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. そこから送信されたトラフィックを許可する IP アドレス範囲を追加します。
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. すべての信頼されたサービスでこのキー コンテナーにアクセスできるようにする必要がある場合は、`bypass` を `AzureServices` に設定します。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. 既定のアクションを `Deny` に設定することによって、ネットワーク ルールを有効にします。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```
---
## <a name="references"></a>References
* ARM テンプレート リファレンス: [Azure Key Vault ARM テンプレート リファレンス](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI コマンド: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell コマンドレット:[Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault)、[Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule)、[Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule)、[Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>次のステップ

* [Key Vault の仮想ネットワーク サービス エンドポイント](overview-vnet-service-endpoints.md)
* [Azure Key Vault のセキュリティの概要](security-features.md)
