---
title: PowerShell でのデプロイ操作の表示 | Microsoft Docs
description: Azure PowerShell を使用して、リソース マネージャーのデプロイからの問題を検出する方法について説明します。
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 06/14/2016
ms.author: tomfitz

---
# Azure PowerShell でのデプロイ操作の表示
> [!div class="op_single_selector"]
> * [ポータル](resource-manager-troubleshoot-deployments-portal.md)
> * [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
> * [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
> * [REST API](resource-manager-troubleshoot-deployments-rest.md)
> 
> 

デプロイの操作は、Azure PowerShell から確認することができます。最も一般的な確認の対象としては、デプロイ中にエラーが発生したときに実行されていた操作が挙げられます。この記事では、失敗した操作の表示について重点的に取り上げます。PowerShell には、すぐにエラーを見つけ出し、有効と考えられる解決策を特定できるコマンドレットが用意されています。

[!INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

デプロイの前にテンプレートおよびインフラストラクチャを検証して、いくつかのエラーを回避できます。後からトラブルシューティングに役立つと思われるデプロイメント中の追加の要求および応答の情報を記録することもできます。検証、 ログ記録の要求および応答の情報については、「[Azure リソース マネージャーのテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)」を参照してください。

## デプロイ操作を使用してトラブルシューティングを行う
1. **Get-AzureRmResourceGroupDeployment** コマンドを使用すると、デプロイ全体のステータスを取得できます。失敗したデプロイのみの結果をフィルター処理することができます。
   
        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
   
    これにより、次の形式で失敗したデプロイが返されます。
   
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1
   
        Outputs                 :
        DeploymentDebugLogLevel :
2. 通常、各デプロイメントは、それぞれの操作がデプロイメント処理の 1 手順を示す、複数の操作で構成されています。デプロイメントの問題を検出するには、通常デプロイメント操作に関する詳細を確認する必要があります。操作の状態は、**Get-AzureRmResourceGroupDeploymentOperation** で確認できます。
   
        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
   
    このコマンドレットを実行すると、複数の操作がそれぞれ次の形式で返されます。
   
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
3. 失敗した操作についての詳しい情報を得るには、状態が **Failed** である操作のプロパティを取得します。
   
        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
   
    このコマンドレットを実行すると、失敗したすべての操作がそれぞれ次の形式で返されます。
   
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
   
    操作の追跡 ID をメモします。この ID は、次の手順で特定の操作に対象を絞り込むために必要となります。
4. 失敗した特定の操作のステータス メッセージを取得するには、次のコマンドを使用します。
   
        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
   
    次のような結果が返されます。
   
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## 監査ログを使用してトラブルシューティングを行う
[!INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

デプロイのエラーを表示するには、次の手順を使用します。

1. ログ エントリを取得するには、**Get-AzureRmLog** コマンドを実行します。**ResourceGroup** と **Status** パラメーターを使用して、単一のリソース グループの失敗したイベントのみを返すことができます。開始時間と終了時間を指定しない場合は、過去 1 時間のエントリが返されます。たとえば、過去 1 時間に失敗した操作を取得するには、次を実行します。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
   
    特定の期間を指定できます。次の例では、最終日に失敗したアクションを検索します。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
   
    または、失敗したアクションに対して、正確な開始時間と終了時間を設定できます。
   
        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed
2. このコマンドで返されるエントリとプロパティの数が多すぎる場合、**Properties** プロパティを取得することで監査に集中できます。エラー メッセージを表示するには、**DetailedOutput** パラメーターも追加します。
   
        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
   
    これにより、次の形式でログ エントリのプロパティが返されます。
   
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...
3. これらの結果に基づいて、2 番目の要素に注目しましょう。そのエントリのステータス メッセージを見て、結果をさらに絞り込むことができます。
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    次のような結果が返されます。
   
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## 次のステップ
* 特定のデプロイ エラーの解決については、[Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーの解決](resource-manager-common-deployment-errors.md)に関するページを参照してください。
* 他の種類のアクションを監視するために監査ログを使用する方法については、「[Resource Manager の監査操作](resource-group-audit.md)」を参照してください。
* デプロイを実行する前に検証するには、[Azure Resource Manager テンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

<!---HONumber=AcomDC_0622_2016-->