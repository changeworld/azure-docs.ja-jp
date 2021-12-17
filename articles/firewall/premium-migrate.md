---
title: Azure Firewall Premium への移行
description: Azure Firewall Standard から Azure Firewall Premium に移行する方法について説明します。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 11/02/2021
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 574c1f7f84ae34b1a1158b61206135de79ee73af
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548066"
---
# <a name="migrate-to-azure-firewall-premium"></a>Azure Firewall Premium への移行

Azure Firewall Standard を Azure Firewall Premium に移行することで、新しい Premium の機能を利用できます。 Azure Firewall Premium の機能の詳細については、[Azure Firewall Premium の機能](premium-features.md)に関するページを参照してください。

次の 2 つの例では、以下の方法を示します。
- Azure PowerShell を使用して既存の標準ポリシーを移行する
- 既存の標準ファイアウォール (クラシック規則を使用) を Premium ポリシーを使用する Azure Firewall Premium に移行します。

Terraform を使用して Azure Firewall をデプロイすると、Terraform を使用して Azure Firewall Premium に移行することができます。 詳細については、「[Terraform を使用して Azure Firewall Standard を Premium に移行する](/azure/developer/terraform/firewall-upgrade-premium?toc=/azure/firewall/toc.json&bc=/azure/firewall/breadcrumb/toc.json)」を参照してください。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

Standard SKU から移行するとき、パフォーマンスが考慮事項になります。 IDPS と TLS 検査は多くのコンピューティング処理を要する操作です。 Premium SKU では、Standard SKU に匹敵する最大スループット 30 Gbps までスケーリングされる、より強力な VM SKU が使用されます。 30 Gbps スループットは、アラート モードで IDPS と共に構成されるときにサポートされます。 拒否モードで IDPS を使用するときと TLS 検査では CPU 消費が増えます。 最大スループットが低下する可能性があります。 

1 つまたは複数のシグネチャを **[アラートを出して拒否]** に設定するか、アプリケーション ルールで **TLS 検査** を有効にするとき、ファイアウォール スループットが 30 Gbps を下回ることがあります。 Microsoft では、ファイアウォール サービスのパフォーマンスが期待値を満たせるよう、お客様が Azure デプロイで完全なスケール テストを実行することをお勧めしています。

## <a name="downtime"></a>ダウンタイム

移行中はダウンタイムが発生するため、計画メンテナンス中にファイアウォールを移行します。

## <a name="migrate-classic-rules-to-standard-policy"></a>クラシックルールを標準ポリシーに移行する

移行プロセス中に、従来のファイアウォール規則を標準ポリシーに移行することが必要になる場合があります。 これを行うには、Azure portal を使用します。

1. Azure portal で、お使いの標準ファイアウォールを選択します。 **[概要]** ページで、 **[Migrate to firewall policy]\(ファイアウォール ポリシーへの移行\)** を選択します。

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="ファイアウォール ポリシーへの移行":::

1. **[Migrate to firewall policy]\(ファイアウォール ポリシーへの移行\)** ページで、 **[Review + create]\(確認と作成\)** を選択します。
1. **［作成］** を選択します

   デプロイが完了するまでに数分かかる場合があります。

また、Azure PowerShell を使用して Azure Firewall から既存のクラシックルールを移行して、ポリシーを作成することもできます。 詳細については、「 [PowerShell を使用して Azure Firewall 構成を Azure Firewall ポリシーに移行する](../firewall-manager/migrate-to-policy.md)」を参照してください。

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Azure PowerShell を使用して既存のポリシーを移行する

`Transform-Policy.ps1` は、既存の Standard ポリシーから新しい Premium ポリシーを作成する Azure PowerShell スクリプトです。

標準ファイアウォール ポリシー ID が指定されている場合、このスクリプトで、それを Premium Azure Firewall ポリシーに変換します。 このスクリプトは、まず Azure アカウントに接続し、ポリシーをプルし、さまざまなパラメーターを変換および追加してから、新しい Premium ポリシーをアップロードします。 新しい Premium ポリシーは `<previous_policy_name>_premium` という名前になります。 子ポリシーを変換しても、親ポリシーへのリンクは残ります。

使用例:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> このスクリプトでは、脅威インテリジェンスの設定は移行されません。 先に進む前にそれらの設定をメモしておき、それらを手動で移行する必要があります。

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy.Id 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -MinimumVersion 4.5 -ErrorAction SilentlyContinue
    if ($null -eq $networkModule) {
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    $resourceModule = Get-InstalledModule -Name "Az.Resources" -MinimumVersion 4.2 -ErrorAction SilentlyContinue
    if ($null -eq $resourceModule) {
        Write-Host "Please install Az.Resources module version 4.2.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
    Import-Module Az.Resources -MinimumVersion 4.2.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy

```

## <a name="migrate-azure-firewall-using-stopstart"></a>停止/開始を使用して Azure Firewall を移行する

Azure Firewall Standard SKU をファイアウォールポリシーと共に使用する場合は、割り当て/割り当て解除方法を使用して、ファイアウォール SKU をプレミアムに移行できます。 この移行アプローチは、VNet ハブとセキュリティで保護されたハブファイアウォールの両方でサポートされています。 セキュリティで保護されたハブの展開を移行すると、ファイアウォールのパブリック IP アドレスが保持されます。

Azure PowerShell バージョンの最小要件は6.5.0 です。 詳細については、「 [Az 6.5.0](https://www.powershellgallery.com/packages/Az/6.5.0)」を参照してください。

 
### <a name="migrate-a-vnet-hub-firewall"></a>VNET ハブファイアウォールを移行する

- 標準のファイアウォールの割り当てを解除する 

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Deallocate()
   Set-AzFirewall -AzureFirewall $azfw
   ```


- ファイアウォールプレミアムの割り当て

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Sku.Tier="Premium"
   $azfw.Allocate($vnet,$pip, $mgmtpip)
   Set-AzFirewall -AzureFirewall $azfw
   ```

### <a name="migrate-a-secure-hub-firewall"></a>セキュリティで保護されたハブファイアウォールを移行する


- 標準のファイアウォールの割り当てを解除する

   ```azurepowershell
   $azfw = Get-AzFirewall -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Deallocate()
   Set-AzFirewall -AzureFirewall $azfw
   ```

- ファイアウォールプレミアムの割り当て

   ```azurepowershell
   $azfw = Get-AzFirewall -Name -Name "<firewall-name>" -ResourceGroupName "<resource-group-name>"
   $azfw.Sku.Tier="Premium"
   $azfw.Allocate($hub.id)
   Set-AzFirewall -AzureFirewall $azfw
   ```

## <a name="next-steps"></a>次のステップ

- [Azure Firewall Premium の機能の詳細を確認する](premium-features.md)
