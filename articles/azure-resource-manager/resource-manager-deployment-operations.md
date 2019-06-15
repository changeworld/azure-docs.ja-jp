---
title: Azure Resource Manager でのデプロイ履歴 | Microsoft Docs
description: Azure Resource Manager のデプロイ操作を、ポータル、PowerShell、Azure CLI、および REST API を使用して表示する方法について説明します。
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606056"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Azure Resource Manager でのデプロイ履歴の表示

Azure Resource Manager では、デプロイ履歴を表示し、過去のデプロイにおける特定の操作を確認することができます。 デプロイされたリソースを参照して、エラーに関する情報を取得できます。

特定のデプロイ エラーの解決については、 [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーの解決](resource-manager-common-deployment-errors.md)に関するページを参照してください。

## <a name="portal"></a>ポータル

デプロイ履歴からデプロイについての詳細を取得する方法は次のとおりです。

1. 調べたいリソース グループを選択します。

1. **[デプロイ]** の下のリンクを選択します。

   ![デプロイ履歴の選択](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. デプロイ履歴からいずれかのデプロイを選択します。

   ![デプロイの選択](./media/resource-manager-deployment-operations/select-details.png)

1. デプロイの概要と、デプロイされたリソースの一覧が表示されます。

    ![デプロイの概要](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. デプロイに使用されたテンプレートを表示するには、 **[テンプレート]** を選択します。 テンプレートをダウンロードして再利用することができます。

    ![テンプレートの表示](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. デプロイが失敗した場合、エラー メッセージが表示されます。 エラー メッセージを選択すると詳細を確認できます。

    ![失敗したデプロイの表示](./media/resource-manager-deployment-operations/show-error.png)

1. 詳細なエラー メッセージが表示されます。

    ![エラーの詳細の表示](./media/resource-manager-deployment-operations/show-details.png)

1. 関連付け ID は、関連するイベントを追跡するために使用されます。また、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときにも有用である可能性があります。

    ![関連付け ID の取得](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. 失敗した手順の詳細を確認するには、 **[操作の詳細]** を選択します。

    ![デプロイ操作の選択](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. 該当するデプロイ手順の詳細が表示されます。

    ![操作の詳細の表示](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Get-AzResourceGroupDeployment** コマンドを使用すると、デプロイ全体のステータスを取得できます。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

または、失敗したデプロイのみの結果をフィルター処理できます。

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

関連付け ID は、関連するイベントを追跡するために使用されます。また、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときにも有用である可能性があります。 相関 ID を取得するには、以下を使用します。

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

各デプロイには、複数の操作が含まれます。 各操作は、デプロイ プロセスの手順を表します。 デプロイメントの問題を検出するには、通常デプロイメント操作に関する詳細を確認する必要があります。 操作の状態は、**Get-AzResourceGroupDeploymentOperation** で確認できます。

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

このコマンドレットを実行すると、複数の操作がそれぞれ次の形式で返されます。

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

失敗した操作についての詳しい情報を得るには、状態が **Failed** である操作のプロパティを取得します。

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

このコマンドレットを実行すると、失敗したすべての操作がそれぞれ次の形式で返されます。

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

操作の serviceRequestId と trackingId に注目してください。 serviceRequestId は、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときに有用である可能性があります。 trackingId は、次の手順で特定の操作に対象を絞り込むために必要となります。

失敗した特定の操作のステータス メッセージを取得するには、次のコマンドを使用します。

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

次のような結果が返されます。

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Azure でのすべてのデプロイ操作には、要求と応答のコンテンツが含まれます。 デプロイの際に **DeploymentDebugLogLevel** パラメーターを使用して、要求または応答 (あるいは両方) がログに記録されるように指定できます。

次の PowerShell コマンドを使用すると、ログから情報を取得して、ローカルに保存できます。

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

デプロイ全体の状態を取得するには、**azure group deployment show** コマンドを使用します。

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
関連付け ID は、関連するイベントを追跡するために使用されます。また、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときにも有用である可能性があります。

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

デプロイ操作を表示するには、次のコマンドを使用します。

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

デプロイに関する情報を取得するには、[テンプレート デプロイに関する情報を取得する](https://docs.microsoft.com/rest/api/resources/deployments)操作を使用します。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

応答では、特に **provisioningState**、**correlationId**、および **error** の各要素に注目します。 **correlationId** は、関連するイベントを追跡するために使用されます。また、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときにも有用である可能性があります。

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

デプロイに関する情報を取得するには、[すべてのテンプレート デプロイを一覧表示する](https://docs.microsoft.com/rest/api/resources/deployments)操作を使用します。 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
応答には、デプロイ時に **debugSetting** プロパティで指定した内容に基づいて、要求または応答の情報が含まれます。

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>次の手順
* 特定のデプロイ エラーの解決については、 [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーの解決](resource-manager-common-deployment-errors.md)に関するページを参照してください。
* アクティビティ ログを使用して、その他の種類のアクションを監視する方法については、「[アクティビティ ログを表示して Azure リソースを管理する](resource-group-audit.md)」を参照してください。
* デプロイを実行する前に検証するには、 [Azure Resource Manager テンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

