---
title: Automation Runbook を使用してマシンを開始する
description: Azure Automation Runbook を使用して Azure DevTest Labs のラボで仮想マシンを起動する方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24a9b21e9a081a875653a6b368f1ce501b1cb0fb
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401898"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Azure Automation Runbook を使用してラボで仮想マシンを順番に起動する
DevTest Labs の[自動起動](devtest-lab-set-lab-policy.md#set-autostart)機能を使用すると、指定した時間に自動的に VM が起動するように構成できます。 ただし、この機能はマシンが特定の順序で起動することはサポートしません。 このタイプのオートメーションに役立つシナリオがいくつかあります。  1 つのシナリオは、ラボ内の Jumpbox VM が他の VM へのアクセス ポイントである場合です。 Jumpbox VM は、他の VM よりも前に起動する必要があります。 この記事では、スクリプトを実行する PowerShell Runbook を使用して Azure Automation アカウントを設定する方法を説明します。 スクリプトでは、ラボの VM 上のタグを使用して、スクリプトを変更しなくても起動順序を制御できるようにします。

## <a name="setup"></a>セットアップ
この例では、ラボの VM には、0、1、2 などの適切な値とともに追加された **StartupOrder** タグが付けられている必要があります。 起動する必要のないマシンは -1 に指定します。

## <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する
Azure Automation アカウントは、[こちらの記事](../automation/automation-create-standalone-account.md)の手順に従って作成します。 アカウントを作成するときに、**[アカウントとして実行]** オプションを選択します。 Automation アカウントが作成されたら、**[モジュール]** ページを開き、メニューバーの **[Azure モジュールの更新]** を選択します。 既定のモジュールは数バージョン古いため、更新プログラムがないとスクリプトが機能しない可能性があります。

## <a name="add-a-runbook"></a>Runbook を追加する
ここで、Runbook を Automation アカウントに追加するには、左側のメニューで **[Runbook]** を選択します。 メニューの **[Runbook の追加]** を選択し、指示に従って [PowerShell Runbook を作成](../automation/learn/powershell-runbook-managed-identity.md)します。

## <a name="powershell-script"></a>PowerShell スクリプト
次のスクリプトは、サブスクリプション名、ラボ名をパラメーターとして取ります。 スクリプトのフローは、ラボ内のすべての VM を取得し、タグ情報を解析して VM 名とその起動順序のリストを作成することです。 スクリプトでは、VM を順番に調べ、VM を起動します。 ある特定の順序番号に複数の VM がある場合は、それらの VM は PowerShell ジョブを使って非同期的に起動します。 タグを持たない VM に対しては、スタートアップ値を最後 (10) に設定します。 既定では、これらのマシンは最後に起動します。  VM を自動的に起動させたくない場合は、タグの値を 11 に設定します。この場合スクリプトは VM を無視します。

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>スケジュールを作成する
このスクリプトを毎日実行するには、Automation アカウントで[スケジュールを作成](../automation/shared-resources/schedules.md#create-a-schedule)します。 スケジュールが作成されたら、[Runbook にリンク](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook)します。 

複数のラボで複数のサブスクリプションを持つ大規模な状況では、それぞれのラボのファイルにパラメーター情報を格納します。 スクリプトには個々のパラメーターではなくファイルを渡します。 スクリプトは変更する必要がありますが、コアの実行は同じです。 このサンプルでは Azure Automation を使用して PowerShell スクリプトを実行していますが、ビルド/リリース パイプラインでタスクを使用するなど、他のオプションもあります。

## <a name="next-steps"></a>次のステップ
Azure Automation の詳細については、「[Azure Automation の概要](../automation/automation-intro.md)」の記事を参照してください。