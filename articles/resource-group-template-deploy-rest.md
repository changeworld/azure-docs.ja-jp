<properties
   pageTitle="REST API とテンプレートを使用してリソースをデプロイする |Microsoft Azure"
   description="Azure Resource Manager と Resource Manager REST API を使用してリソースを Azure にデプロイします。リソースは Resource Manager テンプレートで定義されます。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [ポータル](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

この記事では、Resource Manager REST API と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。

> [AZURE.TIP] デプロイ時のエラーのデバッグについては、以下を参照してください。
>
> - エラーのトラブルシューティングに役立つ情報を入手するためには、[REST API を使用したデプロイ操作の表示](resource-manager-troubleshoot-deployments-rest.md)に関するページを参照してください。
> - 一般的なデプロイ エラーを解決するためには、「[Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーをトラブルシューティングする](resource-manager-common-deployment-errors.md)」

テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## REST API でデプロイする
1. [一般的なパラメーターおよびヘッダー](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common) (認証トークンを含む) を設定します。
2. 既存のリソース グループがない場合は、リソース グループを作成します。ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、場所を指定します。詳細については、「[リソース グループの作成](https://msdn.microsoft.com/library/azure/dn790525.aspx)」を参照してください。

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. [テンプレート デプロイを検証する](https://msdn.microsoft.com/library/azure/dn790547.aspx)操作を実行して、デプロイを実行する前に検証します。デプロイをテストする場合、(次の手順で示すように) デプロイの実行時に必要なパラメーターを正確に指定します。

3. デプロイを作成します。サブスクリプション ID、デプロイするリソース グループの名前、デプロイの名前、テンプレートへのリンクを指定します。テンプレート ファイルについては、「[パラメーター ファイル](./#parameter-file)」を参照してください。リソース グループを作成する REST API の詳細については、「[テンプレートのデプロイを作成する](https://msdn.microsoft.com/library/azure/dn790564.aspx)」を参照してください。**[モード]** が **[増分]** に設定されていることに注意してください。完全デプロイメントを実行するには、**[モード]** を **[完全]** に設定します。テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
              }
            }
          }
   
      応答の内容、要求の内容、またはその両方を記録するには、要求に **debugSetting** を含めます。

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      ストレージ アカウントを設定して、Shared Access Signature (SAS) トークンを使用することができます。詳細については、「[Delegating Access with a Shared Access Signature (Shared Access Signature を使用したアクセスの委任)](https://msdn.microsoft.com/library/ee395415.aspx)」を参照してください。

4. テンプレートのデプロイの状態を取得します。詳細については、「[テンプレートのデプロイに関する情報を取得](https://msdn.microsoft.com/library/azure/dn790565.aspx)」を参照してください。

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## 次のステップ
- .NET クライアント ライブラリを使用したリソースのデプロイの例については、[.NET ライブラリとテンプレートを使用したリソースのデプロイ](virtual-machines/virtual-machines-windows-csharp-template.md)に関する記事を参照してください。
- テンプレートのパラメーターの定義については、[テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
- ソリューションを別の環境にデプロイする方法については、「[Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments.md)」を参照してください。
- セキュリティで保護された値を渡す KeyVault 参照を使用する方法については、「[デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。

<!---HONumber=AcomDC_0824_2016-->