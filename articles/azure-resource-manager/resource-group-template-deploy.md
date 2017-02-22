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
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5b8b293b5b37365088a3df55581be7b7bf76691c
ms.openlocfilehash: 7bc3421e00215ca4629ea11811c98e581377b24a


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [ポータル](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

このトピックでは、Azure PowerShell と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。 テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。 テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

> [!TIP]
> デプロイ時のエラーのデバッグについては、以下を参照してください。
> 
> * 「[デプロイ操作の表示](resource-manager-deployment-operations.md)」。エラーのトラブルシューティングに役立つ情報を掲載しています。
> * [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーをトラブルシューティングする](resource-manager-common-deployment-errors.md)」で、一般的なデプロイ エラーを解決する方法を知る
> 
> 

## <a name="quick-steps-to-deployment"></a>簡単なデプロイ手順
デプロイを簡単に開始するには、次のコマンドを使用します。

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json -TemplateParameterFile c:\MyTemplates\example.params.json
```

これらのコマンドは、リソース グループを作成し、そのリソース グループにテンプレートをデプロイします。 テンプレート ファイルとパラメーター ファイルは、両方ともローカル ファイルです。 成功した場合は、リソースのデプロイに必要なすべてを入手できたことになります。 ただし、デプロイするリソースを指定する方法は、より多くのオプションから選択できます。 この記事の残りの部分では、デプロイ時に使用できるオプションをすべて説明します。 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>デプロイ
1. Azure アカウントにログインします。

   ```powershell
   Add-AzureRmAccount
   ```

    アカウントの概要が返されます。
    
   ```powershell
   Environment           : AzureCloud
   Account               : someone@example.com
   TenantId              : {guid}
   SubscriptionId        : {guid}
   SubscriptionName      : Example Subscription
   CurrentStorageAccount :
   ```

2. 複数のサブスクリプションがある場合、**Set-AzureRmContext** コマンドで、デプロイに使用するサブスクリプション ID を指定します。 
   
   ```powershell
   Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
   ```
3. テンプレートをデプロイするときは、デプロイ済みのリソースを格納するリソース グループを指定する必要があります。 デプロイする既存のリソース グループがある場合は、この手順をスキップしてそのリソース グループを使用することができます。 
   
     リソース グループを作成するには、リソース グループの名前と場所を指定します。 リソース グループにはリソースに関するメタデータが格納されるため、リソース グループの場所を入力します。 場合によっては、コンプライアンス上の理由から、そのメタデータが格納される場所を指定する必要があります。 一般に、ほとんどのリソースが存在する場所を指定することをお勧めします。 同じ場所を使用することで、テンプレートを簡素化できます。
   
   ```powershell
   New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   ```
   
     新しいリソース グループの概要が返されます。
4. デプロイを実行する前に、デプロイの設定を検証できます。 **Test-AzureRmResourceGroupDeployment** コマンドレットを使用すると、実際のリソースを作成する前に問題を検出することができます。 次の例では、デプロイを検証する方法を示しています。
   
   ```powershell
   Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>
   ```
5. リソース グループにリソースをデプロイするには、**New-AzureRmResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。 パラメーターには、デプロイの名前、リソース グループの名前、テンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。 **Mode** パラメーターを指定しない場合、**Incremental** の既定値が使用されます。 完全デプロイを実行するには、**[モード]** を **[完全]** に設定します。 テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。
   
     ローカル テンプレートをデプロイするには、**TemplateFile** パラメーターを使用します。
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json
   ```
   
     外部テンプレートをデプロイするには、**TemplateUri** パラメーターを使用します。
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json
   ```
   
     前の&2; つの例には、パラメーター値は含まれません。 パラメーター値を渡す方法については、「[パラメーター](#parameters)」のセクションを参照してください。 ここでは、次の構文でパラメーター値を入力するように求められます。

   ```powershell  
   cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
   Supply values for the following parameters:
   (Type !? for Help.)
   firstParameter: <type here>
   ```
     
     リソースがデプロイされると、デプロイの概要が表示されます。 概要には、**ProvisioningState** が含まれます。これは、デプロイが成功したかどうかを示します。

   ```powershell   
   DeploymentName    : ExampleDeployment
   ResourceGroupName : ExampleResourceGroup
   ProvisioningState : Succeeded
   Timestamp         : 4/14/2015 7:00:27 PM
   Mode              : Incremental
   ```
      
6. デプロイ エラーのトラブルシューティングに役立つ可能性がある追加情報を記録する場合は、**DeploymentDebugLogLevel** パラメーターを使用します。 デプロイ操作と共に要求の内容、応答の内容、またはその両方を記録するように指定できます。
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
   ```
   
     このデバッグの内容を使用したデプロイのトラブルシューティングの詳細については、[デプロイ操作の表示](resource-manager-deployment-operations.md)に関するページを参照してください。

## <a name="deploy-private-template-with-sas-token"></a>SAS トークンを使用してプライベートのテンプレートをデプロイする
SAS トークンを使用したデプロイの際に、テンプレートをストレージ アカウントに追加し、リンクできます。

> [!IMPORTANT]
> 次の手順に従うことにより、テンプレートを含む BLOB はアカウントの所有者だけがアクセスできるようになります。 ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。 もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。 SAS トークンの使用はテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。
> 
> 

### <a name="add-private-template-to-storage-account"></a>ストレージ アカウントにプライベートのテンプレートを追加する
次の手順で、テンプレートのストレージ アカウントをセットアップします。

1. リソース グループを作成します。
   
   ```powershell
   New-AzureRmResourceGroup -Name ManageGroup -Location "West US"
   ```
2. ストレージ アカウントを作成します。 ストレージ アカウント名は、 Azure 内で一意である必要があるため、独自の名前を入力してください。
   
   ```powershell
   New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"
   ```
3. 現在のストレージ アカウントを設定します。
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
4. コンテナーを作成します。 アクセス許可は **Off** に設定します。これは所有者だけがコンテナーにアクセスできることを表します。
   
   ```powershell
   New-AzureStorageContainer -Name templates -Permission Off
   ```
5. コンテナーに、テンプレートを追加します。
   
   ```powershell
   Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>デプロイ時に SAS トークンを指定する
ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを取得しテンプレートの URI に含めます。

1. 現在のストレージ アカウントを変更した場合は、現在のストレージ アカウントをテンプレートが含まれるストレージ アカウントに設定します。
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
2. アクセスを制限するため、読み取りアクセス許可と有効期限を持つ SAS トークンを作成します。 SAS トークンを含むテンプレートの完全な URI を取得します。
   
   ```powershell
   $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
   ```
3. SAS トークンを含む URI を指定することにより、テンプレートをデプロイします。
   
   ```powershell
   New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri
   ```

リンクされたテンプレートでの SAS トークン使用例については、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

## <a name="parameters"></a>parameters

次のオプションを使用してパラメーターの値を提供できます。 
   
- インライン パラメーター。 コマンドレット内の個々のパラメーター名などです (たとえば、**-myParameterName**)。

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"
   ```
- パラメーター オブジェクト。 **-TemplateParameterObject** パラメーターなどです。

   ```powershell   
   $parameters = @{"<ParameterName>"="<Parameter Value>"}
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters
   ```
- ローカル パラメーター ファイル。 **-TemplateParameterFile** パラメーターなどです。

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile c:\MyTemplates\example.params.json
   ```
- 外部パラメーター ファイル。 **-TemplateParameterUri** パラメーターなどです。

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.params.json
   ```
      
[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)] 

PowerShell コマンドのパラメーターのいずれかと名前が同じであるパラメーターがテンプレートに含まれている場合、そのパラメーターに値を指定するように求められます。 Azure PowerShell では、ご使用のテンプレートのパラメーターは、接尾辞 **FromTemplate** を付けて表します。 たとえば、テンプレート内の **ResourceGroupName** という名前のパラメーターは、[New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment) コマンドレットの **ResourceGroupName** パラメーターと競合します。 **ResourceGroupNameFromTemplate** に値を指定するように求められます。 一般的に、このような混乱を防ぐために、デプロイ処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

## <a name="parameter-precedence"></a>パラメーターの優先順位

同じデプロイ操作で、インライン パラメーターとローカル パラメーター ファイルを使用することができます。 たとえば、一部の値をローカル パラメーター ファイルで指定し、その他の値をデプロイ中にインラインで追加します。 ローカル パラメーター ファイルとインラインの両方でパラメーターの値を指定すると、インラインの値が優先されます。

ただし、外部パラメーター ファイルを使用する場合、他の値をインラインまたはローカル ファイルから渡すことはできません。 **TemplateParameterUri** パラメーターでパラメーター ファイルを指定すると、すべてのインライン パラメーターが無視されます。 すべてのパラメーター値を外部ファイル内で指定します。 パラメーター ファイルに含めることができない機密性の高い値がテンプレートに含まれている場合は、その値をキー コンテナーに追加するか、すべてのパラメーター値をインラインで動的に指定してください。

## <a name="next-steps"></a>次のステップ
* .NET クライアント ライブラリを使用したリソースのデプロイの例については、「[Deploy resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」 (.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ) を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* ソリューションを別の環境にデプロイする方法については、「 [Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
* デプロイの自動化についての&4; 回シリーズの解説については、「[Automating application deployments to Azure Virtual Machines (Azure Virtual Machines へのアプリケーションのデプロイの自動化)](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 このシリーズでは、アプリケーションのアーキテクチャ、アクセスとセキュリティ、可用性と拡張性、およびアプリケーションのデプロイについて説明しています。




<!--HONumber=Jan17_HO3-->


