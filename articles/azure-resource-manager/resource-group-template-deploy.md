---
title: "PowerShell とテンプレートを使用してリソースをデプロイする | Microsoft Docs"
description: "Azure Resource Manager と Azure PowerShell を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f5119fef1fb0a316b4109ccbc4433f8c9a18d128
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ

このトピックでは、Azure PowerShell と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。 テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。

これらの例で使用するテンプレート (storage.json) は、「[初めての Azure Resource Manager テンプレートを作成する](resource-manager-create-first-template.md#final-template)」から取得できます。 このテンプレートを例で使用するには、JSON ファイルを作成し、コピーしたコンテンツを追加します。

## <a name="deploy-local-template"></a>ローカル テンプレートのデプロイ
デプロイを速やかに開始するには、次のコマンドレットを使用してインライン パラメーターを含むローカル テンプレートをデプロイします。 

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

デプロイが完了するまでに数分かかる場合があります。 デプロイが完了すると、次のような結果を含むメッセージが表示されます。

```powershell
ProvisioningState       : Succeeded
```

前の例では、既定のサブスクリプションでリソース グループを作成しました。 別のサブスクリプションを使用するには、ログインの後に [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) コマンドレットを追加します。

## <a name="deploy-external-template"></a>外部テンプレートのデプロイ

外部テンプレートをデプロイするには、**TemplateUri** パラメーターを使用します。 外部テンプレートの場所は、パブリックにアクセスできる URI (ストレージ アカウント内のファイルなど) であればどこでもかまいません。

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -storageNamePrefix contoso -storageSKU Standard_GRS
```

アクセスの際に Shared Access Signature (SAS) トークンを要求することで、テンプレートを保護することができます。 SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-powershell-sas-token.md)」を参照してください。

## <a name="parameter-files"></a>パラメーター ファイル

前の例では、パラメーターをインライン値として渡す方法を示しました。 ファイル内にパラメーター値を指定し、デプロイ時にそのファイルを渡すことができます。 

パラメーター ファイルは次の形式にする必要があります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

ローカル パラメーター ファイルを渡すには、**TemplateParameterFile** パラメーターを使用します。

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

外部パラメーター ファイルを渡すには、**TemplateParameterUri** パラメーターを使用します。

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.parameters.json
```

同じデプロイ操作で、インライン パラメーターとローカル パラメーター ファイルを使用することができます。 たとえば、一部の値をローカル パラメーター ファイルで指定し、その他の値をデプロイ中にインラインで追加します。 ローカル パラメーター ファイルとインラインの両方でパラメーターの値を指定すると、インラインの値が優先されます。

ただし、外部パラメーター ファイルを使用する場合、他の値をインラインまたはローカル ファイルから渡すことはできません。 **TemplateParameterUri** パラメーターでパラメーター ファイルを指定すると、すべてのインライン パラメーターが無視されます。 すべてのパラメーター値を外部ファイル内で指定します。 パラメーター ファイルに含めることができない機密性の高い値がテンプレートに含まれている場合は、その値をキー コンテナーに追加するか、すべてのパラメーター値をインラインで動的に指定してください。

PowerShell コマンドのパラメーターのいずれかと名前が同じであるパラメーターがテンプレートに含まれている場合、PowerShell ではテンプレート内のパラメーター名の後ろに **FromTemplate** という文字を付加します。 たとえば、テンプレート内の **ResourceGroupName** という名前のパラメーターは、[New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment) コマンドレットの **ResourceGroupName** パラメーターと競合します。 **ResourceGroupNameFromTemplate** に値を指定するように求められます。 一般的に、このような混乱を防ぐために、デプロイ処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

## <a name="test-a-deployment"></a>デプロイのテスト

リソースを実際にデプロイすることなく、テンプレートとパラメーターの値をテストするには、[Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment) を使用します。 ローカル ファイルを使用する場合も、リモート ファイルを使用する場合も、オプションは同じです。

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

## <a name="log-deployment-content-for-debugging"></a>デバッグ用にデプロイ コンテンツを記録する

デプロイ操作に関する情報はアクティビティ ログに自動的に記録されますが、 デプロイ エラーのトラブルシューティングに役立つ可能性がある追加情報を記録する場合は、**DeploymentDebugLogLevel** パラメーターを使用します。 デプロイ操作と共に要求の内容、応答の内容、またはその両方を記録するように指定できます。
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All `
  -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

ログを表示する方法については、「[リソースのアクションを監査するアクティビティ ログの表示](resource-group-audit.md)」を参照してください。

## <a name="export-resource-manager-template"></a>Resource Manager テンプレートのエクスポート
(PowerShell または、ポータルなど、その他の方法でデプロイされた) 既存のリソース グループの場合、そのリソース グループのリソース マネージャーのテンプレートを参照できます。 テンプレートのエクスポートには、2 つの利点があります。

1. ソリューションの将来のデプロイを簡単に自動化できます。すべてのインフラストラクチャがテンプレートに定義されているためです。
2. ソリューションを表す JavaScript Object Notation (JSON) を見ることでテンプレートの構文に詳しくなります。

リソース グループのテンプレートを表示するには、 [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) コマンドレットを実行します。

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleResourceGroup
```

詳細については、「[Export an Azure Resource Manager template from existing resources (既存のリソースから Azure Resource Manager テンプレートをエクスポートする)](resource-manager-export-template.md)」を参照してください。


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

完全モードを使用するには、`Mode` パラメーターを使用します。

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```


## <a name="next-steps"></a>次のステップ
* テンプレートのデプロイ用の完全なサンプル スクリプトについては、[Resource Manager テンプレートのデプロイ用のスクリプト](resource-manager-samples-powershell-deploy.md)に関するページを参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-powershell-sas-token.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。


