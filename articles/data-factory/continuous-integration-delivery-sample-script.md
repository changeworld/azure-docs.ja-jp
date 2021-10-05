---
title: 継続的インテグレーションおよびデリバリーのデプロイ前と後のスクリプト
description: このサンプルの Azure Data Factory 内で継続的インテグレーションおよびデリバリーを使用したデプロイ前と後のスクリプトを使用する方法について説明します。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 50a93f31e7a8fd1c161545bc9d7cbbc218a17db4
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219307"
---
# <a name="sample-pre--and-post-deployment-script"></a>サンプルの配置前と配置後スクリプト

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

次のサンプルでは、Azure Data Factory 内で継続的インテグレーションおよびデリバリーを使用したデプロイ前と後のスクリプトを使用する方法を示します。

## <a name="install-azure-powershell"></a>Azure PowerShell をインストールする

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-Az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

>[!WARNING]
>最新バージョンの PowerShell および Data Factory モジュールを使用しない場合は、コマンドの実行中に逆シリアル化エラーが発生する可能性があります。 
>

## <a name="script-execution-and-parameters"></a>スクリプトの実行とパラメーター

次のサンプル スクリプトは、トリガーをデプロイ前に停止し、デプロイ後に再起動するために使用できます。 このスクリプトには、削除済みのリソースを完全に削除するコードも含まれています。 Azure DevOps の git リポジトリにスクリプトを保存し、Azure PowerShell タスク、最新の Azure PowerShell バージョンを介して参照します。


デプロイ前スクリプトを実行する場合は、 **[スクリプトの引数]** フィールドで、次のパラメーターのバリエーションを指定する必要があります。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


デプロイ後スクリプトを実行する場合は、 **[スクリプトの引数]** フィールドで、次のパラメーターのバリエーションを指定する必要があります。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

> [!NOTE]
> `-deleteDeployment` フラグは、ARM のデプロイ履歴から ADF デプロイ エントリを削除することを指定するために使用されます。

:::image type="content" source="media/continuous-integration-delivery/continuous-integration-image11.png" alt-text="Azure PowerShell タスク":::

## <a name="pre--and-post-deployment-script"></a>デプロイ前と後のスクリプト

こちらがデプロイ前/後に使用できるスクリプトです。 削除されたリソースとリソース参照を示しています。

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
## <a name="next-steps"></a>次のステップ

- [継続的インテグレーションとデリバリーの概要](continuous-integration-delivery.md)
- [Azure Pipelines リリースを使用して継続的インテグレーションを自動化する](continuous-integration-delivery-automate-azure-pipelines.md)
- [Resource Manager テンプレートを各環境に手動でレベル上げする](continuous-integration-delivery-manual-promotion.md)
- [Resource Manager テンプレートでカスタム パラメーターを使用する](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [リンクされた Resource Manager テンプレート](continuous-integration-delivery-linked-templates.md)
- [修正プログラム運用環境の適用](continuous-integration-delivery-hotfix-environment.md)