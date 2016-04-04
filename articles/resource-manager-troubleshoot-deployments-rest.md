<properties
   pageTitle="REST API でのデプロイのトラブルシューティング | Microsoft Azure"
   description="Azure リソース マネージャー REST API を使用して、リソース マネージャーのデプロイからの問題を修正する方法について説明します。"
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

# Azure リソース マネージャー REST API でのリソース グループのデプロイのトラブルシューティング

> [AZURE.SELECTOR]
- [ポータル](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

リソースを Azure にデプロイするときにエラーを受け取った場合は、発生した問題のトラブルシューティングを行う必要があります。REST API では、エラーを見つけて、可能性のある修正を確認できるように、操作を提供します。

監査ログまたはデプロイ操作のいずれかを確認して、デプロイのトラブルシューティングを行うことができます。このトピックでは、両方のメソッドを示します。

デプロイの前にテンプレートおよびインフラストラクチャを検証して、いくつかのエラーを回避できます。詳細については、「[Azure リソース マネージャーのテンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)」を参照してください。

## REST API を使用したトラブルシューティング

1. [テンプレート デプロイを作成する](https://msdn.microsoft.com/library/azure/dn790564.aspx)操作でリソースをデプロイします。デバッグに役立つ可能性のある情報を保持するには、JSON 要求の **debugSetting** プロパティを **requestContent** および/または **responseContent** に設定します。 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    既定では、**debugSetting** の値は、**none** に設定されます。**debugSetting** の値を指定する場合、デプロイ時に渡している情報の種類を慎重に検討します。要求または応答に関する情報をログ記録すると、デプロイ操作で取得される重要なデータを公開する可能性があります。

2. [テンプレート デプロイに関する情報を取得する](https://msdn.microsoft.com/library/azure/dn790565.aspx)操作を行って、デプロイに関する情報を取得します。

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    応答では、特に **provisioningState**、**correlationId**、および **error** 要素に注意してください。**correlationId** は、関連するイベントを追跡するために使用されます。また、問題のトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときにも、役立つ場合があります。
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  "error": {\r\n    "message": "Conflict",\r\n    "code": "Conflict"\r\n  }\r\n}"}]
            }  
          }
        }

3. [すべてのテンプレート デプロイ操作を一覧表示する](https://msdn.microsoft.com/library/azure/dn790518.aspx)操作を行って、デプロイ操作に関する情報を取得します。

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    応答には、デプロイ時に **debugSetting** プロパティで指定した内容に基づいて、要求または応答の情報が含まれます。
    
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

4. [サブスクリプションの管理イベントを一覧表示する](https://msdn.microsoft.com/library/azure/dn931934.aspx)操作を行って、デプロイに関する監査情報からイベントを取得します。

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## 次のステップ

- 他の種類のアクションを監視するために監査ログを使用する方法については、「[リソース マネージャーの監査操作](resource-group-audit.md)」を参照してください。
- デプロイを実行する前に検証するには、「[Azure リソース マネージャーのテンプレートを使用してリソース グループをデプロイ](resource-group-template-deploy.md)」を参照してください。

<!---HONumber=AcomDC_0323_2016-->