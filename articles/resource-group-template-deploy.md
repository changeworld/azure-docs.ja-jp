<properties
   pageTitle="PowerShell とテンプレートを使用してリソースをデプロイする |Microsoft Azure"
   description="Azure Resource Manager と Azure PowerShell を使用してリソースを Azure にデプロイします。リソースは Resource Manager テンプレートで定義されます。"
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [ポータル](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

このトピックでは、Azure PowerShell と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。

> [AZURE.TIP] デプロイ時のエラーのデバッグについては、以下を参照してください。
>
> - エラーのトラブルシューティングに役立つ情報を入手するためには、「[Azure PowerShell でのデプロイ操作の表示](resource-manager-troubleshoot-deployments-powershell.md)」
> - 一般的なデプロイ エラーを解決するためには、「[Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーをトラブルシューティングする](resource-manager-common-deployment-errors.md)」

テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

## 簡単なデプロイ手順

この記事では、デプロイ時に使用できるさまざまなオプションをすべて説明します。ただし、多くの場合、必要なのは 2 つの簡単なコマンドのみです。デプロイを簡単に開始するには、次のコマンドを使用します。

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

ご使用のシナリオにさらに適したデプロイのオプションについて詳しく知るには、この記事を読み進めてください。

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## PowerShell でデプロイする

1. Azure アカウントにログインします。

        Add-AzureRmAccount

     アカウントの概要が返されます。

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. 複数のサブスクリプションがある場合、**Set-AzureRmContext** コマンドで、デプロイに使用するサブスクリプション ID を指定します。

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. 通常、新しいテンプレートをデプロイする場合は、リソースを格納するためのリソース グループを作成します。デプロイする既存のリソース グループがある場合は、この手順をスキップしてそのリソース グループを使用することができます。

     リソース グループを作成するには、リソース グループの名前と場所を指定します。リソース グループにはリソースに関するメタデータが格納されるため、リソース グループの場所を入力する必要があります。場合によっては、コンプライアンス上の理由から、そのメタデータが格納される場所を指定する必要があります。一般に、ほとんどのリソースが存在する場所を指定することをお勧めします。同じ場所を使用することで、テンプレートを簡素化できます。

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     新しいリソース グループの概要が返されます。
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. デプロイを実行する前に、デプロイの設定を検証できます。**Test-AzureRmResourceGroupDeployment** コマンドレットを使用すると、実際のリソースを作成する前に問題を検出することができます。次の例では、デプロイを検証する方法を示しています。

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. リソース グループにリソースをデプロイするには、**New-AzureRmResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。パラメーターには、デプロイの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。**Mode** パラメーターを指定しない場合、**Incremental** の既定値が使用されます。完全デプロイメントを実行するには、**[モード]** を **[完全]** に設定します。テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。

     ローカル テンプレートをデプロイするには、**TemplateFile** パラメーターを使用します。

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     外部テンプレートをデプロイするには、**TemplateUri** パラメーターを使用します。

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     次のオプションを使用してパラメーターの値を提供できます。
   
     1. インライン パラメーターを使用する

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. パラメーター オブジェクトを使用する

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. ローカル パラメーター ファイルを使用する。テンプレート ファイルについては、「[パラメーター ファイル](#parameter-file)」を参照してください。

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. 外部パラメーター ファイルを使用する。テンプレート ファイルについては、「[パラメーター ファイル](#parameter-file)」を参照してください。

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        外部パラメーター ファイルを使用する場合、他の値をインラインまたはローカル ファイルから渡すことはできません。詳細については、「[パラメーターの優先順位](#parameter-precendence)」を参照してください。

     リソースがデプロイされると、デプロイの概要が表示されます。

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     PowerShell コマンドのパラメーターのいずれかと名前が同じであるパラメーターがテンプレートに含まれている場合、そのパラメーターに値を指定するように求められます。テンプレートのパラメーターには、接尾辞 **FromTemplate** が含まれます。たとえば、テンプレート内の **ResourceGroupName** という名前のパラメーターは、[New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) コマンドレットの **ResourceGroupName** パラメーターと競合します。**ResourceGroupNameFromTemplate** に値を指定するように求められます。一般的に、このような混乱を防ぐために、デプロイ処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

6. デプロイ エラーのトラブルシューティングに役立つ可能性がある追加情報を記録する場合は、**DeploymentDebugLogLevel** パラメーターを使用します。デプロイ操作と共に要求の内容、応答の内容、またはその両方を記録するように指定できます。

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     このデバッグの内容を使用したデプロイのトラブルシューティングの詳細については、[Azure PowerShell によるリソース グループのデプロイのトラブルシューティング](resource-manager-troubleshoot-deployments-powershell.md)に関するページを参照してください。

## SAS トークンを使用してストレージからテンプレートをデプロイする

SAS トークンを使用したデプロイの際に、テンプレートをストレージ アカウントに追加し、リンクできます。

> [AZURE.IMPORTANT] 次の手順に従うことにより、テンプレートを含む BLOB はアカウントの所有者だけがアクセスできるようになります。ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。SAS トークンの使用はテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。

### ストレージ アカウントにプライベートのテンプレートを追加する

次の手順で、テンプレートのストレージ アカウントをセットアップします。

1. リソース グループを作成します。

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. ストレージ アカウントを作成します。ストレージ アカウント名は、 Azure 内で一意である必要があるため、独自の名前を入力してください。

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. 現在のストレージ アカウントを設定します。

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. コンテナーを作成します。アクセス許可は **Off** に設定します。これは所有者だけがコンテナーにアクセスできることを表します。

        New-AzureStorageContainer -Name templates -Permission Off
        
5. コンテナーに、テンプレートを追加します。

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### デプロイ時に SAS トークンを指定する

ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを取得しテンプレートの URI に含めます。

1. 現在のストレージ アカウントを変更した場合は、現在のストレージ アカウントをテンプレートが含まれるストレージ アカウントに設定します。

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. アクセスを制限するため、読み取りアクセス許可と有効期限を持つ SAS トークンを作成します。SAS トークンを含むテンプレートの完全な URI を取得します。

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. SAS トークンを含む URI を指定することにより、テンプレートをデプロイします。

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

リンクされたテンプレートでの SAS トークン使用例については、「[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## パラメーターの優先順位

同じデプロイ操作で、インライン パラメーターとローカル パラメーター ファイルを使用することができます。たとえば、一部の値をローカル パラメーター ファイルで指定し、その他の値をデプロイ中にインラインで追加します。ローカル パラメーター ファイルとインラインの両方でパラメーターの値を指定すると、インラインの値が優先されます。

ただし、インライン パラメーターを外部パラメーター ファイルと共に使用することはできません。**TemplateParameterUri** パラメーターでパラメーター ファイルを指定すると、すべてのインライン パラメーターが無視されます。すべてのパラメーター値を外部ファイル内で指定する必要があります。パラメーター ファイルに含めることができない機密性の高い値がテンプレートに含まれている場合は、その値をキー コンテナーに追加して外部パラメーター ファイルでそのキー コンテナーを参照するか、すべてのパラメーター値をインラインで動的に指定してください。

セキュリティで保護された値を渡すために KeyVault 参照を使用する方法の詳細については、「[デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。

## 次のステップ
- .NET クライアント ライブラリを使用したリソースのデプロイの例については、[.NET ライブラリとテンプレートを使用したリソースのデプロイ](virtual-machines/virtual-machines-windows-csharp-template.md)に関する記事を参照してください。
- テンプレートのパラメーターの定義については、[テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
- ソリューションを別の環境にデプロイする方法については、「[Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->