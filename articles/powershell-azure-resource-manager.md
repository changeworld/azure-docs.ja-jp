<properties 
	pageTitle="リソース マネージャーでの Azure PowerShell | Microsoft Azure" 
	description="Azure PowerShell を使用して複数のリソースをリソース グループとして Azure にデプロイする方法について、概要を示します。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2016" 
	ms.author="tomfitz"/>

# Azure リソース マネージャーでの Azure PowerShell の使用

> [AZURE.SELECTOR]
- [ポータル](azure-portal/resource-group-portal.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [REST API](resource-manager-rest-api.md)


Azure Resource Manager では、Azure リソースのライフサイクル管理のための最新のアプローチが実装されています。個々のリソースを作成して管理するのではなく、まず、ブログ、フォト ギャラリー、SharePoint ポータル、Wiki など、ソリューション全体の構想を練ります。ソリューションの宣言型表現であるテンプレートを使用して、ソリューションをサポートするために必要なすべてのリソースが含まれているリソース グループを定義します。その後、そのリソース グループを論理ユニットとしてデプロイし、管理します。

このチュートリアルでは、Azure リソース マネージャーで Azure PowerShell を使用する方法について説明します。ソリューションのデプロイとそのソリューションの使用のプロセスについて段階的に説明します。Azure PowerShell とリソース マネージャーのテンプレートを使用し、以下をデプロイします。

- データベースをホストする SQL Server
- データを格納する SQL Database
- Web アプリにデータベースへの接続を許可するファイアウォール ルール
- Web アプリの機能とコストを定義するための App Service プラン
- Web アプリを実行するための Web サイト
- データベースへの接続文字列を格納するための Web 構成
- アラート ルール - パフォーマンスとエラーの監視用
- App Insights - 自動スケール設定用

## 前提条件

このチュートリアルを完了するには、次のものが必要です。

- Azure アカウント
  + [無料で Azure アカウントを開く](/pricing/free-trial/?WT.mc_id=A261C142F)ことができます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。明示的に設定を変更して課金を求めない限り、クレジット カードに課金されることはありません。
  
  + [MSDN サブスクライバーの特典を有効にする](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)こともできます。MSDN サブスクリプションにより、有料の Azure サービスを使用できるクレジットが毎月提供されます。
- Azure PowerShell 1.0このリリースとそのインストール方法については、「[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)」を参照してください。

このチュートリアルは、PowerShell の初心者向けに設計されていますが、モジュール、コマンドレット、セッションなどの基本概念を理解していることを前提としています。

## コマンドレットのヘルプの取得

このチュートリアルに表示されているすべてのコマンドレットの詳細なヘルプを取得するには、Get-Help コマンドレットを使用します。

    Get-Help <cmdlet-name> -Detailed

たとえば、Get-AzureRmResource コマンドレットのヘルプを取得するには、次のように入力します。

    Get-Help Get-AzureRmResource -Detailed

ヘルプの概要と Resources モジュールのコマンドレットの一覧を取得するには、次のように入力します。

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

次のような出力が表示されます。

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

コマンドレットの完全なヘルプを取得するには、次の形式でコマンドを入力します。

    Get-Help <cmdlet-name> -Full
  
## Azure アカウントへのログイン

ソリューションを操作する前に、ご使用のアカウントにログインする必要があります。

Azure アカウントにログインするには、**Add-AzureRmAccount** コマンドレットを使用します。

    Add-AzureRmAccount

このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。ログイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。

アカウント設定の有効期限が切れるため、ときどき更新する必要があります。アカウント設定を更新するには、**Add-AzureRmAccount** をもう一度実行します。

>[AZURE.NOTE] リソース マネージャー モジュールでは Add-AzureRmAccount が必要になります。発行設定ファイルでは不十分です。

サブスクリプションが複数ある場合、**Set-AzureRmContext** コマンドレットでデプロイに使用するサブスクリプション ID を指定します。

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## リソース グループの作成

サブスクリプションにリソースをデプロイする前に、そのリソースを含めるリソース グループを作成する必要があります。

リソース グループを作成するには、**New-AzureRmResourceGroup** コマンドレットを使用します。

コマンドは **Name** パラメーターを使用してリソース グループの名前を指定し、**Location** パラメーターを使用して場所を指定します。前のセクションで検出した内容に基づいて、"West US" を場所に指定します。

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
次のように出力されます。

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

リソース グループが正常に作成されました。

## ソリューションのデプロイ

このトピックでは、テンプレートの作成方法やテンプレートの構造については説明しません。詳細については、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」と「[Resource Manager テンプレートのチュートリアル](resource-manager-template-walkthrough.md)」をご覧ください。[Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)から事前定義済みの [Web アプリと SQL Database をプロビジョニングする](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)テンプレートをデプロイします。

リソース グループとテンプレートの作成が終わり、これでテンプレート内で定義されたインフラストラクチャをリソース グループにデプロイする準備が整いました。**New-AzureRmResourceGroupDeployment** コマンドレットを使用してリソースをデプロイします。テンプレートにより、使用されるさまざまな既定値が指定されます。それらのパラメーターの値を指定する必要がありません。基本的な構文は次のようになります。

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

リソース グループとテンプレートの場所を指定します。テンプレートがローカル ファイルの場合、**-TemplateFile** パラメーターを使用し、テンプレートのパスを指定します。**-Mode** パラメーターを **Incremental** または **Complete** のいずれかに設定できます。既定では、リソース マネージャーはデプロイ中に増分更新を実行するため、**増分**が必要な場合に **-Mode** を設定する必要はありません。これらのデプロイ モード間の相違については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。

###動的なテンプレート パラメーター

PowerShell に慣れている場合は、マイナス記号 (-) を入力して Tab キーを押すことで、コマンドレットで利用可能なパラメーターを順番に表示できることをご存じのことと思われます。この機能は、テンプレートで定義するパラメーターでも同様に使用できます。テンプレート名を入力すると、コマンドレットがすぐにテンプレートをフェッチし、解析して、テンプレート パラメーターをコマンドに動的に追加します。これにより、テンプレート パラメーターの値の指定が非常に簡単になります。

コマンドを入力すると、不足している必須パラメーター **administratorLoginPassword** を入力するように求められます。パスワードを入力すると、セキュリティ保護された文字列値が隠されます。この対策によって、プレーンテキストでパスワードを提供することの危険性が解消されます。

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

テンプレートをデプロイするコマンド内のパラメーターのいずれかと名前が一致するパラメーターがテンプレートに含まれている場合 (たとえば、[New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) コマンドレットの **ResourceGroupName** パラメーターと同じ名前のパラメーターである **ResourceGroupName** がテンプレートに含まれている場合など)、接尾辞に **FromTemplate** があるパラメーター (**ResourceGroupNameFromTemplate** など) に値を指定するように求められます。一般的に、このような混乱を防ぐために、デプロイメント処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

コマンドを実行してリソースが作成されると、メッセージが返されます。最終的に、デプロイの結果が表示されます。

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

わずかな手順で、複雑な Web サイトに必要なリソースを作成してデプロイしました。

### デバッグ情報のログ

テンプレートをデプロイするとき、要求と応答に関する追加情報をログに記録できます。**New-AzureRmResourceGroupDeployment** の実行時に **-DeploymentDebugLogLevel** パラメーターを指定します。その情報を基に、デプロイ時に発生したエラーを解決できる可能性があります。既定値は **[なし]** です。要求と応答のいずれの内容もログに記録されません。要求、応答、または両方の内容をログに記録するように指定できます。デプロイのトラブルシューティングとデバッグ情報のログの詳細については、[Azure PowerShell でのリソース グループのデプロイのトラブルシューティング](resource-manager-troubleshoot-deployments-powershell.md)に関するページを参照してください。次の例では、デプロイの要求と応答の内容がログに記録されます。

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] DeploymentDebugLogLevel パラメーターを設定するときは、デプロイ中に渡す情報の種類を慎重に検討してください。要求または応答に関する情報をログ記録すると、デプロイ操作で取得される重要なデータを公開する可能性があります。


## リソース グループに関する情報を取得します。

リソース グループを作成したら、リソース マネージャー モジュールでコマンドレットを使用してリソース グループを管理できます。

- サブスクリプションのリソース グループを取得するには、**Get-AzureRmResourceGroup** コマンドレットを使用します。

		Get-AzureRmResourceGroup -ResourceGroupName TestRG1
	
	返される情報は次のとおりです。

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

	リソース グループ名を指定しない場合、このコマンドレットはサブスクリプションのすべてのリソース グループを返します。

- リソース グループのリソースを取得するには、**Find-AzureRmResource** コマンドレットとその **ResourceGroupNameContains** パラメーターを使用します。パラメーターがない場合、Find-AzureRmResource は Azure サブスクリプション内のすべてのリソースを取得します。

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
	
     次のように書式設定されたリソースの一覧が返されます。
		
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
	        
- タグを使用してサブスクリプションのリソースを論理的に整理し、**Find-AzureRmResource** コマンドレットと **Find-AzureRmResourceGroup** コマンドレットでリソースを取得できます。

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      タグを使用して実行できることはほかにも多数あります。詳細については、[タグを使用した Azure リソースの整理](resource-group-using-tags.md)に関するページを参照してください。

## リソース グループへの追加

リソース グループにリソースを追加するには、**New-AzureRmResource** コマンドレットを使用できます。ただし、この方法でリソースを追加した場合は、その新しいリソースがテンプレート内には存在しないため、将来混乱が生じる可能性があります。元のテンプレートを再度デプロイすると、不完全なソリューションがデプロイされることになります。頻繁にデプロイしていれば、新しいリソースをテンプレートに追加して再度デプロイする方が簡単で確実であることがわかります。

## リソースの移動

新しいリソース グループに、既存のリソースを移動できます。例については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。

## テンプレートをエクスポートする

(PowerShell または、ポータルなど、その他の方法でデプロイされた) 既存のリソース グループの場合、そのリソース グループのリソース マネージャーのテンプレートを参照できます。テンプレートのエクスポートには、2 つの利点があります。

1. ソリューションの将来のデプロイを簡単に自動化できます。すべてのインフラストラクチャがテンプレートに定義されているためです。

2. ソリューションを表す JavaScript Object Notation (JSON) を見ることでテンプレートの構文に詳しくなります。

PowerShell により、リソース グループの現在の状態を表すテンプレートを生成するか、特定のデプロイに使用されたテンプレートを取得できます。

リソース グループのテンプレートのエクスポートは、リソース グループを変更した後、その現在の状態の JSON 表現を取得する必要があるときに便利です。しかしながら、生成されたテンプレートには、最小数のパラメーターのみが含まれ、変数はありません。テンプレートの大半の値はハードコーディングされています。生成されたテンプレートをデプロイする前に、さまざまな環境に合わせてデプロイをカスタマイズできるように、多くの値をパラメーターに変換しておくと便利です。

リソースのデプロイに使用された実際のテンプレートを表示する必要があるとき、特定のデプロイのテンプレートをエクスポートすると便利です。テンプレートには、元のデプロイに定義されていたすべてのパラメーターと変数が含まれます。ただし、組織の誰かがテンプレートに定義されている以外のリソース グループを変更した場合、そのテンプレートはそのリソース グループの現在の状態を表しません。

> [AZURE.NOTE] テンプレートのエクスポート機能はプレビューの段階にあり、現在のところ、一部の種類のリソースでは、テンプレートをエクスポートできません。テンプレートのエクスポートを試行したとき、一部のリソースがエクスポートされなかったというエラーが表示されることがあります。必要に応じて、ダウンロード後、エクスポートされなかったリソースをテンプレート内で手動で定義できます。

###リソース グループからテンプレートをエクスポートする

リソース グループのテンプレートを表示するには、**Export-AzureRmResourceGroup** コマンドレットを実行します。

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###デプロイからテンプレートをダウンロードする

特定のデプロイで使用されたテンプレートをダウンロードするには、**Save-AzureRmResourceGroupDeploymentTemplate** コマンドレットを実行します。

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## リソースまたはリソース グループを削除する

- リソース グループからリソースを削除するには、**Remove-AzureRmResource** コマンドレットを使用します。このコマンドレットはリソースを削除しますが、リソース グループは削除しません。

	このコマンドは、TestRG1 リソース グループから TestSite Web サイトを削除します。

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- リソース グループを削除するには、**Remove-AzureRmResourceGroup** コマンドレットを使用します。このコマンドレットは、リソース グループとそのリソースを削除します。

		Remove-AzureRmResourceGroup -Name TestRG1
		
	削除の確認が求められます。
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## デプロイ スクリプト

このトピックの最初のデプロイ例では、Azure にリソースをデプロイするために必要な個々のコマンドレットのみが示されていました。続く例では、リソース グループを作成し、リソースをデプロイするデプロイ スクリプトが示されています。

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## 次のステップ

- リソース マネージャーのテンプレートの作成の詳細については、「[Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)」を参照してください。
- テンプレートをデプロイする方法の詳細については、「[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](./resource-group-template-deploy.md)」を参照してください。
- プロジェクトのデプロイの詳細な例については、[Azure でマイクロサービスを予測どおりにデプロイする](app-service-web/app-service-deploy-complex-application-predictably.md)方法に関するページを参照してください。
- 失敗したデプロイのトラブルシューティングについては、「[Azure でのリソース グループのデプロイのトラブルシューティング](./resource-manager-troubleshoot-deployments-powershell.md)」を参照してください。

<!---HONumber=AcomDC_0914_2016-->