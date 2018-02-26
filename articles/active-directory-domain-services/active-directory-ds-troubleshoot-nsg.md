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
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: cc9a61314de7e10afe370c3b1307d03544a379d5
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>管理対象ドメインの無効なネットワーク構成のトラブルシューティング
この記事は、ネットワーク関連の構成エラーのトラブルシューティングと解決に役立ちます。次のような警告メッセージは、このようなエラーにより表示されます。

## <a name="alert-aadds104-network-error"></a>アラート AADDS104: ネットワーク エラー
**警告メッセージ:** "*Microsoft はこの管理対象ドメインのドメイン コントローラーに到達できません。これは、仮想ネットワークで構成されたネットワーク セキュリティ グループ (NSG) によって管理対象ドメインへのアクセスがブロックされている場合に発生することがあります。別の理由として、インターネットからの着信トラフィックをブロックするユーザー定義ルートが存在していることが考えられます。*

無効な NSG 構成が、Azure AD Domain Services のネットワーク エラーの最も一般的な原因です。 仮想ネットワーク用に構成されたネットワーク セキュリティ グループ (NSG) は、[特定のポート](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)へのアクセスを許可する必要があります。 こうしたポートがブロックされていると、Microsoft は、管理対象ドメインを監視または更新できません。 さらに、Azure AD ディレクトリと管理対象ドメインの間の同期が影響を受けます。 NSG を作成するときは、こうしたポートを開いたままにして、サービスが中断されないようにしてください。


## <a name="sample-nsg"></a>NSG のサンプル
次の表では、管理対象ドメインのセキュリティ保護を維持しながら、Microsoft が情報を監視、管理、更新できるようにする、NSG の例を示します。

![NSG のサンプル](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services には、仮想ネットワークからの無制限の送信アクセスが必要です。 仮想ネットワークの発信アクセスを制限する追加の NSG 規則は作成しないようにすることをお勧めします。

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Azure Portal を使用してネットワーク セキュリティ グループに規則を追加する
PowerShell を使いたくない場合は、Azure Portal を使って手動で 1 つの規則を NSG に追加できます。 ネットワーク セキュリティ グループに規則を作成するには、次の手順を実行します。

1. Azure Portal で、[ネットワーク セキュリティ グループ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)のページに移動します
2. テーブルから、管理対象ドメインが有効になっているサブネットに関連付けられている NSG を選択します。
3. 左側のパネルの **[設定]** で、**[受信セキュリティ規則]** または **[送信セキュリティ規則]** をクリックします。
4. **[追加]** をクリックして情報を入力し、規則を作成します。 Click **OK**.
5. 規則テーブルで検索して、規則が作成されたことを確認します。


## <a name="create-an-nsg-for-azure-ad-domain-services-using-powershell"></a>PowerShell を使用して Azure AD Domain Services の NSG を作成する
この NSG は、望ましくない着信アクセスを拒否しながら、Azure AD Domain Services に必要なポートへの着信トラフィックを許可するように構成されています。

**前提条件: Azure PowerShell をインストールして構成する。**[Azure PowerShell モジュールをインストールして Azure サブスクリプションに接続](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)する手順に従います。

>[!TIP]
> 最新バージョンの Azure PowerShell モジュールを使用することをお勧めします。 以前のバージョンの Azure PowerShell モジュールがインストールされている場合は、最新バージョンに更新します。
>

次の手順に従って、PowerShell を使用して新しい NSG を作成します。 
1. Azure サブスクリプションにログインします。

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

2. 3 つの規則を含む NSG を作成します。 次のスクリプトでは、Azure AD Domain Services の実行に必要なポートへのアクセスを許可する NSG の 3 つの規則を定義します。 次に、スクリプトではこれらの規則を含む新しい NSG を作成します。 仮想ネットワークでデプロイされているワークロードで必要な場合は、同じ形式を使用して、他の着信トラフィックを許可する規則を追加します。

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD `
  -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting `
  -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, `
  13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, `
  52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, `
  52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 `
  -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD `
  -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Create the NSG with the 3 rules above
  $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $Location `
  -Name "AAD-DomainServices-NSG" -SecurityRules $SyncRule,$PSRemotingRule,$RemoteDesktopRule
  ```

3. 最後に、NSG を、選択した vnet および subnet に関連付けます。

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Azure AD Domain Services の NSG を作成および適用する完全スクリプト
>[!TIP]
> 最新バージョンの Azure PowerShell モジュールを使用することをお勧めします。 以前のバージョンの Azure PowerShell モジュールがインストールされている場合は、最新バージョンに更新します。
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Login-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD `
-Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting `
-Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, `
13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, `
52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, `
52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 `
-SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD `
-Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Create the NSG with the 3 rules above
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Location $Location `
-Name "AAD-DomainServices-NSG" -SecurityRules $SyncRule,$PSRemotingRule,$RemoteDesktopRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```

> [!NOTE]
> この既定の NSG では、Secure LDAP に使われるポートへのアクセスをロックダウンしていません。 インターネット経由で Secure LDAP アクセスをロックダウンするには、[こちらの記事](active-directory-ds-troubleshoot-ldaps.md)をご覧ください。
>

## <a name="need-help"></a>お困りの際は、
[フィードバックの共有およびサポートについては](active-directory-ds-contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
