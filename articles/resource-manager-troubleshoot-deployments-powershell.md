<properties
   pageTitle="PowerShell でのデプロイのトラブルシューティング | Microsoft Azure"
   description="Azure PowerShell を使用して、リソース マネージャーのデプロイからの問題を修正する方法について説明します。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Azure PowerShell でのリソース グループのデプロイのトラブルシューティング

> [AZURE.SELECTOR]
- [ポータル](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

リソースを Azure にデプロイするときにエラーを受け取った場合は、発生した問題のトラブルシューティングを行う必要があります。Azure PowerShell では、簡単にエラーを見つけ、可能性のある修正を確認することができるように、コマンドレットを提供します。

監査ログまたはデプロイ操作のいずれかを確認して、デプロイのトラブルシューティングを行うことができます。このトピックでは、両方のメソッドを示します。

デプロイの前にテンプレートおよびインフラストラクチャを検証して、いくつかのエラーを回避できます。詳細については、「[Azure リソース マネージャーのテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)」を参照してください。

## 監査ログを使用してトラブルシューティングを行う

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

デプロイのエラーを表示するには、次の手順を使用します。

1. ログ エントリを取得するには、**Get-AzureRmLog** コマンドを実行します。**ResourceGroup** と **Status** パラメーターを使用して、単一のリソース グループの失敗したイベントのみを返すことができます。開始時間と終了時間を指定しない場合は、過去 1 時間のエントリが返されます。たとえば、過去 1 時間に失敗した操作を取得するには、次を実行します。

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    特定の期間を指定できます。次の例では、過去 14 日間に失敗したアクションを検索します。

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    または、失敗したアクションに対して、正確な開始時間と終了時間を設定できます。

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. このコマンドで返されるエントリとプロパティの数が多すぎる場合、**Properties** プロパティを取得することで監査に集中できます。エラー メッセージを表示するには、**DetailedOutput** パラメーターも追加します。

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
    これにより、次の形式でログ エントリのプロパティが返されます。
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. 特定の 1 つのエントリのステータス メッセージを確認して、結果をさらに絞り込むことができます。

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
    これにより、次の形式でステータス メッセージが返されます。
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## デプロイ操作を使用してトラブルシューティングを行う

1. **Get-AzureRmResourceGroupDeployment** コマンドを使用すると、デプロイ全体のステータスを取得できます。失敗したデプロイのみの結果をフィルター処理することができます。

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    これにより、次の形式で失敗したデプロイが返されます。
        
        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleGroup
        ProvisioningState : Failed
        Timestamp         : 8/27/2015 8:03:34 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
        Name             Type                       Value
        ===============  =========================  ==========
        siteName         String                     ExampleSite
        hostingPlanName  String                     ExamplePlan
        siteLocation     String                     West US
        sku              String                     Free
        workerSize       String                     0
        
        Outputs           :

2. 通常、各デプロイメントは、それぞれの操作がデプロイメント処理の 1 手順を示す、複数の操作で構成されています。デプロイメントの問題を検出するには、通常デプロイメント操作に関する詳細を確認する必要があります。操作の状態は、**Get-AzureRmResourceGroupDeploymentOperation** で確認できます。

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment | Format-List
        
    これにより、次の形式で操作が返されます。
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. 操作に関する詳細を取得するには、**Properties** オブジェクトを取得します。

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
    これにより、次の形式で操作のプロパティが返されます。
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. 失敗した操作のステータス メッセージに注目するには、次のコマンドを使用します。

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
    これにより、次の形式でステータス メッセージが返されます。
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## 次のステップ

- 他の種類のアクションを監視するために監査ログを使用する方法については、「[Resource Manager の監査操作](resource-group-audit.md)」を参照してください。
- デプロイを実行する前に検証するには、「[Azure Resource Manager のテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)」を参照してください。

<!---HONumber=AcomDC_0420_2016-->