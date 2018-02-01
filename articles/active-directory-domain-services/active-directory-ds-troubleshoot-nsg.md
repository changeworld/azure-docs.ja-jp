---
title: "Azure Active Directory Domain Services: ネットワーク セキュリティ グループ構成のトラブルシューティング | Microsoft Docs"
description: "Azure AD Domain Services 向けの NSG 構成のトラブルシューティング"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure AD Domain Services - ネットワーク セキュリティ グループ構成のトラブルシューティング



## <a name="aadds104-network-error"></a>AADDS104: ネットワーク エラー

**警告メッセージ:** "*Microsoft はこの管理対象ドメインのドメイン コントローラーに到達できません。これは、仮想ネットワークで構成されたネットワーク セキュリティ グループ (NSG) によって管理対象ドメインへのアクセスがブロックされている場合に発生することがあります。別の理由として、インターネットからの着信トラフィックをブロックするユーザー定義ルートが存在していることが考えられます。*"

Azure AD Domain Services のネットワーク エラーの最も一般的な原因として考えられるのは、NSG の構成が正しくないことです。 お使いの管理対象ドメインのサービスとメンテナンスを Microsoft が確実に実行できるようにするには、ネットワーク セキュリティ グループ (NSG) を使ってサブネット内の[特定のポート](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)へのアクセスを許可する必要があります。 これらのポートがブロックされている場合、Microsoft は必要なリソースにアクセスできず、お客様のサービスにとって有害になる可能性もあります。 NSG を作成するときは、これらのポートを開いたままにして、サービスが中断されないようにしてください。

## <a name="sample-nsg"></a>NSG のサンプル
次の表では、管理対象ドメインのセキュリティ保護を維持しながら、Microsoft が情報を監視、管理、更新できるようにする、NSG の例を示します。

![NSG のサンプル](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services には、無制限の送信アクセスが必要です。 NSG には余計な送信規則を作成しないことをお勧めします。

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Azure Portal を使用してネットワーク セキュリティ グループに規則を追加する
PowerShell を使いたくない場合は、Azure Portal を使って手動で 1 つの規則を NSG に追加できます。 ネットワーク セキュリティ グループに規則を作成するには、次の手順のようにします。

1. Azure Portal で、[ネットワーク セキュリティ グループ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)のページに移動します
2. テーブルから、ドメインに関連付けられている NSG を選びます。
3. 左側のナビゲーションの [設定] で、**[受信セキュリティ規則]** または **[送信セキュリティ規則]** をクリックします。
4. **[追加]** をクリックして情報を入力し、規則を作成します。 Click **OK**.
5. 規則テーブルで検索して、規則が作成されたことを確認します。


## <a name="create-a-default-nsg-using-powershell"></a>PowerShell を使って既定の NSG を作成する

この手順では、Azure AD Domain Services の実行に必要なすべてのポートを開いたまま、他の望ましくないアクセスをすべて拒否する新しい NSG を、PowerShell を使って作成できます。


この解決方法では、[Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0) をインストールして実行する必要があります。

1. Azure AD ディレクトリに接続します。

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Azure サブスクリプションにログインします。

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. 3 つの規則を含む NSG を作成します。 次のスクリプトでは、Azure AD Domain Services の実行に必要なポートへのアクセスを許可する NSG の 3 つの規則を定義します。 次に、スクリプトではこれらの規則を含む新しい NSG を作成します。 必要に応じて、同じ形式を使って規則を追加してかまいません。

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. 最後に、選択した vnet および subnet と NSG を関連付けます。

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>完全なスクリプト

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>この既定の NSG では、Secure LDAP に使われるポートへのアクセスをロックダウンしていません。 このポートの規則を作成する方法については、[こちらの記事](active-directory-ds-troubleshoot-ldaps.md)をご覧ください。
>

## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](active-directory-ds-contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
