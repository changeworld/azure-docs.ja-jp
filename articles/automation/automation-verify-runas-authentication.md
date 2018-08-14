---
title: Azure Automation アカウントの構成を検証する
description: この記事では、Automation アカウントの構成が正しく設定されていることを確認する方法について説明します。
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 32b73cf4570393ed24ee6d1121aef75aaf193134
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713756"
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Azure Automation 実行アカウントの認証をテストする

Automation アカウントが正常に作成されたら、新しく作成または更新された Automation 実行アカウントを使用して Azure Resource Manager または Azure クラシック デプロイメントで正常に認証できることを確認する簡単なテストを実行できます。

## <a name="automation-run-as-authentication"></a>Automation 実行アカウントの認証

実行アカウントを使った認証を検証するには、以下のサンプル コードを使って、[PowerShell Runbook を作成](automation-creating-importing-runbook.md)します。さらにカスタム Runbook でも、Automation アカウントによる Resource Manager リソースの認証と管理を行ってください。

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

    $logonAttempt = 0
    $logonResult = $False

    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
    # Logging in to Azure...
    $connectionResult = Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

     Start-Sleep -Seconds 30
    }
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Get all ARM resources from all resource groups
$ResourceGroups = Get-AzureRmResourceGroup

foreach ($ResourceGroup in $ResourceGroups)
{
    Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
    $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
    ForEach ($Resource in $Resources)
    {
        Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
    }
    Write-Output ("")
}
```

Runbook での認証に使用されるコマンドレット (**Connect-AzureRmAccount**) は、*ServicePrincipalCertificate* パラメーター セットを使用することにご注意ください。  認証に使用するのはサービス プリンシパル証明書であり、資格情報ではありません。  

> [!IMPORTANT]
> これで、**Connect-AzureRmAccount** のエイリアスは **Add-AzureRMAccount** に設定されました。 ライブラリ項目を検索して **Connect-AzureRMAccount** が表示されない場合は、**Add-AzureRmAccount** を使用するか、Automation アカウントでモジュールを更新できます。

[この Runbook を実行](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal)して実行アカウントを検証すると、[Runbook ジョブ](automation-runbook-execution.md)が作成され、[ジョブ] ページが表示されて、ジョブの状態が **[ジョブの概要]** タイルに表示されます。 最初のジョブの状態は " *キュー登録済み* " であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。 その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  Runbook ジョブが完了すると、状態は **[完了]** と表示されます。

Runbook の詳細な結果を表示するには、 **[出力]** タイルをクリックします。  **[出力]** ページに、正常に認証されたことが示されると共に、ご利用のサブスクリプションに含まれる全リソース グループのすべてのリソースの一覧が返されます。  

Runbook のコードを再利用する際は、`#Get all ARM resources from all resource groups` というコメントで始まるコード ブロックを忘れずに削除してください。

## <a name="classic-run-as-authentication"></a>クラシック実行認証

クラシック実行アカウントを使った認証を検証するには、以下のサンプル コードを使って、[PowerShell Runbook を作成](automation-creating-importing-runbook.md)します。さらにカスタム Runbook でも、クラシック デプロイ モデルにおけるリソースの認証と管理を行ってください。  

```powershell
$ConnectionAssetName = "AzureClassicRunAsConnection"
# Get the connection
$connection = Get-AutomationConnection -Name $connectionAssetName

# Authenticate to Azure with certificate
Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
$Conn = Get-AutomationConnection -Name $ConnectionAssetName
if ($Conn -eq $null)
{
    throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
}

$CertificateAssetName = $Conn.CertificateAssetName
Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
$AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
if ($AzureCert -eq $null)
{
    throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
}

Write-Verbose "Authenticating to Azure with certificate." -Verbose
Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

#Get all VMs in the subscription and return list with name of each
Get-AzureVM | ft Name
```

[この Runbook を実行](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal)して実行アカウントを検証すると、[Runbook ジョブ](automation-runbook-execution.md)が作成され、[ジョブ] ページが表示されて、ジョブの状態が **[ジョブの概要]** タイルに表示されます。 最初のジョブの状態は " *キュー登録済み* " であり、クラウドの Runbook ワーカーが使用できるようになるのを待っていることを示します。 その後、ワーカーがジョブを要求すると*開始中*になり、Runbook が実際に実行を開始すると*実行中*になります。  Runbook ジョブが完了すると、状態は **[完了]** と表示されます。

Runbook の詳細な結果を表示するには、 **[出力]** タイルをクリックします。  **[出力]** ページに、正常に認証されたことが示されると共に、ご利用のサブスクリプションにデプロイされているすべての Azure VM の一覧が VMName で返されます。  

Runbook のコードを再利用する際は、コマンドレット **Get-AzureVM** を忘れずに削除してください。

## <a name="next-steps"></a>次の手順

* PowerShell Runbook の使用を開始するには、「[初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)」を参照してください。
* グラフィカル作成の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。
