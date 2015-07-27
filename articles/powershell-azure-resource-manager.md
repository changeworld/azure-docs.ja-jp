<properties 
	pageTitle="Azure リソース マネージャーでの Windows PowerShell の使用" 
	description="Azure PowerShell を使用すると、リソース グループとして複数のリソースを Azure にデプロイできます。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Azure リソース マネージャーでの Windows PowerShell の使用

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Azure リソース マネージャーでは、Azure リソースに関するまったく新しい考え方が導入されています。個々のリソースを作成して管理するのではなく、まず、ブログ、フォト ギャラリー、SharePoint ポータル、Wiki などの複雑なサービスを想定して開始します。サービスのリソース モデルであるテンプレートを使用して、サービスをサポートするために必要なリソースでリソース グループを作成します。これにより、そのリソース グループを論理ユニットとして管理して、デプロイできます。

このチュートリアルでは、Azure PowerShell を Microsoft Azure の Azure リソース マネージャーで使用する方法について説明します。ここでは、サポートするために必要なすべてのリソースを含む、SQL データベースを備えた Azure でホストされる Web アプリケーションのリソース グループを作成し、デプロイするプロセスを説明します。

## 前提条件

このチュートリアルを完了するには、 Azure PowerShell version 0.8.0 以降が必要です。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「[How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](powershell-install-configure.md)」をご覧ください。

このチュートリアルは、PowerShell の初心者向けに設計されていますが、モジュール、コマンドレット、セッションなどの基本概念を理解していることを前提としています。Windows PowerShell の詳細については、「[Getting Started with Windows PowerShell (Windows PowerShell の概要)](http://technet.microsoft.com/library/hh857337.aspx)」を参照してください。

このチュートリアルに表示されているすべてのコマンドレットの詳細なヘルプを取得するには、Get-Help コマンドレットを使用します。

	Get-Help <cmdlet-name> -Detailed

たとえば、Add-AzureAccount コマンドレットについてのヘルプを確認するには、次のように入力します。

	Get-Help Add-AzureAccount -Detailed

## Azure PowerShell モジュールについて
Version 0.8.0 以降、Azure PowerShell のインストールには次の複数の Windows PowerShell モジュールが含まれています。Azure モジュールまたは Azure リソース マネージャー モジュールで利用できるコマンドを使用するかどうかを明示的に指定する必要があります。これらを簡単に切り替えるために、新しいコマンドレット **Switch-AzureMode** を Azure Profile モジュールに追加しました。

Azure PowerShell を使用する場合、Azure モジュールのコマンドレットが既定でインポートされます。Azure リソース マネージャー モジュールに切り替えるには、Switch-AzureMode コマンドレットを使用します。これによって、Azure モジュールはセッションから削除され、Azure リソース マネージャーおよび Azure プロファイル モジュールがインポートされます。

AzureResoureManager モジュールに切り替えるには、次のように入力します。

    PS C:> Switch-AzureMode -Name AzureResourceManager

Azure モジュールに切り替えるには、次のように入力します。

    PS C:> Switch-AzureMode -Name AzureServiceManagement

既定では、Switch-AzureMode は、現在のセッションのみに影響します。スイッチをすべての PowerShell セッションで有効にするには、Switch-AzureMode の **Global** パラメーターを使用します。

Switch-AzureMode コマンドレットについての質問は、`Get-Help Switch-AzureMode` と入力するか、[Switch-AzureMode に関するページ](http://go.microsoft.com/fwlink/?LinkID=394398)を参照してください。
  
ヘルプの概要と AzureResourceManager モジュールのコマンドレットの一覧を取得するには、次のように入力します。

    PS C:> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

次のような出力が表示されます。

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

コマンドレットの完全なヘルプを取得するには、次の形式でコマンドを入力します。

	Get-Help <cmdlet-name> -Full

たとえば、次のように入力します。

	Get-Help Get-AzureLocation -Full

Azure リソース マネージャーのコマンドの全セットについては、[Azure リソース マネージャー コマンドレットに関するページ](http://go.microsoft.com/fwlink/?LinkID=394765)を参照してください。
  
## リソース グループの作成

チュートリアルのこのセクションでは、SQL データベースを使用して Web アプリケーションのリソース グループを作成およびデプロイするプロセスを説明します。

このタスクを実行するためには、Azure、SQL、Web アプリケーション、またはリソース管理の専門家である必要はありません。テンプレートには、リソース グループのモデルと必要になる可能性のあるすべてのリソースが用意されています。タスクを自動化するために Windows PowerShell を使用しているため、これらのプロセスを大規模なタスクのスクリプトのモデルとして使用することができます。

### 手順 1. Azure リソース マネージャーに切り替える 
1. PowerShell の起動Azure PowerShell コンソールや Windows PowerShell ISE など、必要なホスト プログラムを使用することができます。

2. **Switch-AzureMode** コマンドレットを使用して、AzureResourceManager モジュールおよび AzureProfile モジュールのコマンドレットをインポートします。

        PS C:> Switch-AzureMode AzureResourceManager

3. Azure アカウントを Windows PowerShell セッションに追加するには、**Add-AzureAccount** コマンドレットを使用します。

        PS C:> Add-AzureAccount

このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。ログイン後にアカウント設定がダウンロードされるため、Windows PowerShell で使用できるようになります。

アカウント設定の有効期限が切れるため、ときどき更新する必要があります。アカウント設定を更新するには、**Add-AzureAccount** をもう一度実行します。

>[AZURE.NOTE]AzureResourceManager モジュールには、Add-AzureAccount が必要です。発行設定ファイルでは不十分です。

### 手順 2. ギャラリー テンプレートを選択する

リソース グループとそのリソースを作成するにはいくつかの方法がありますが、最も簡単な方法はリソース グループ テンプレートを使用することです。*リソース グループ テンプレート* は、リソース グループのリソースを定義する JSON 文字列です。文字列には、名前やサイズなどのユーザー定義の値の "パラメーター" と呼ばれるプレースホルダーが含まれます。

Azure にはリソース グループ テンプレートのギャラリーがホストされ、独自のテンプレートを最初から、またはギャラリー テンプレートを編集して、作成することができます。このチュートリアルでは、ギャラリー テンプレートを使用します。

Azure リソース グループ テンプレート ギャラリーのすべてのテンプレートを表示するには、**Get-azureresourcegroupgallerytemplate** コマンドレットを使用します。 ただし、このコマンドでは、多数のテンプレートが返されます。表示するテンプレートの数を減らすには、発行元のパラメーターを指定します。

PowerShell プロンプトで、次のように入力します。
    
    PS C:> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

コマンドレットは、発行元が Microsoft であるギャラリー テンプレートの一覧を返します。**Identity** プロパティを使用して、コマンドのテンプレートを識別します。

Microsoft.WebSiteSQLDatabase.0.2.6-preview テンプレートは、興味深いものです。新しいバージョンがリリースされたために、コマンドを実行する時点で、テンプレートのバージョンは少し異なる場合があります。最新バージョンのテンプレートを使用します。ギャラリー テンプレートの詳細を確認するには、**Identity** パラメーターを使用します。Identity パラメーターの値は、そのテンプレートの ID です。

    PS C:> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

コマンドレットは、概要や説明など、テンプレートに関するより多くの情報を持つオブジェクトを返します。

このテンプレートは、ニーズを満たしているようです。ディスクに保存して、詳しく見てみましょう。

### 手順 3. テンプレートを確認する

テンプレートをディスク上の JSON ファイルに保存します。この手順は必須ではありませんが、テンプレートを簡単に表示することができます。テンプレートを保存するには、**Save-AzureResourceGroupGalleryTemplate** コマンドレットを使用します。テンプレートを指定するには **Identity** パラメーターを使用し、ディスク上のパスを指定するには **Path** パラメーターを使用します。

Save-AzureResourceGroupGalleryTemplate はテンプレートを保存して、パス、JSON テンプレート ファイルのファイル名を返します。

	PS C:> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


テンプレート ファイルは、メモ帳などのテキスト エディターで表示できます。各テンプレートには、**parameters** セクションと **resources** セクションがあります。

テンプレートの **parameters** セクションはすべてのリソースで定義されるパラメーターのコレクションです。これには、リソース グループを設定する場合に指定できるプロパティの値が含まれます。

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

一部のパラメーターには既定値があります。テンプレートを使用する場合、これらのパラメーターには値を指定する必要がありません。値を指定しないと、既定値が使用されます。

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

列挙値があるパラメーターの場合は、有効な値がパラメーターと共に一覧表示されます。たとえば、**sku** パラメーターには Free、Shared、Basic、または Standard の値を指定できます。**sku** パラメーターに値を指定しないと、既定値 Free が使用されます。

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },


**administratorLoginPassword** パラメーターは、プレーンテキストではなく、セキュリティ保護された文字列を使用します。セキュリティ保護された文字列の値を指定すると、値が隠されます。

	"administratorLoginPassword": {
      "type": "securestring"
    },

テンプレートの **resources** セクションには、テンプレートを作成するリソースが一覧表示されます。このテンプレートによって、SQL データベース サーバーと SQL データベース、サーバー ファームと Web サイト、およびいくつかの管理設定が作成されます。
  
各リソースの定義には、名前、種類と場所、ユーザー定義の値のパラメーターなどのプロパティが含まれます。たとえば、テンプレートのこのセクションでは、SQL データベースを定義します。データベース名のパラメーター ([parameters('databaseName')])、データベース サーバーの場所のパラメーター [parameters('serverLocation')]、および照合順序プロパティのパラメーター [parameters('collation')] が含まれます。

    {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[parameters('serverLocation')]",
        "apiVersion": "2.0",
        "dependsOn": [
          "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


テンプレートを使用する準備が整いましたが、実行する前に、各リソースの場所を見つける必要があります。

### 手順 4. リソースの種類の場所を取得する

ほとんどのテンプレートでは、リソース グループ内の各リソースの場所を指定するように求められます。すべてのリソースは Azure データ センターにありますが、すべての Azure データ センターがすべてのリソースの種類をサポートしているわけではありません。

そのリソースの種類をサポートしている任意の場所を選択します。リソース グループ内のすべてのリソースを同じ場所に作成する必要はありません。ただし、パフォーマンスを最適化するために、可能な限り、同じ場所にリソースを作成することをお勧めします。特に、アプリケーションがデータベースにアクセスする場所とデータベースが同じ場所にあるかどうかを確認することをお勧めします。

各リソースの種類をサポートする場所を取得するには、**Get-AzureLocation** コマンドレットを使用します。ResourceGroup などの特定の種類のリソースに出力を制限するには、次のコマンドレットを使用します。

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

次のように出力されます。

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

これで、リソース グループを作成するために必要な情報が得られました。

### ステップ 5. リソース グループを作成する
 
この手順では、リソース グループ テンプレートを使用してリソース グループを作成します。詳細については、ディスク上の New_WebSite_And_Database.json ファイルを開いて、理解してください。テンプレート ファイルは、リソースの正しい ApiVersion など、渡すパラメーターの値を決定するために非常に役立つことがあります。

リソース グループを作成するには、**New-AzureResourceGroup** コマンドレットを使用します。

コマンドは **Name** パラメーターを使用してリソース グループの名前を指定し、**Location** パラメーターを使用して場所を指定します。**Get-AzureLocation** の出力を使用して、リソース グループの場所を選択します。**GalleryTemplateIdentity** パラメーターを使用して、ギャラリー テンプレートを指定します。

	PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

テンプレート名を入力すると、New-AzureResourceGroup がテンプレートを取得し、解析して、テンプレート パラメーターをコマンドに動的に追加します。これにより、テンプレート パラメーターの値の指定が非常に簡単になります。また、必須のパラメーター値を忘れた場合は、Windows PowerShell から値を求められます。

**動的なテンプレート パラメーター**

パラメーターを取得するには、マイナス記号 (-) を入力してパラメーター名を示し、Tab キーを押します。または、siteName などのパラメーター名の最初の数文字を入力して、Tab キーを押します。

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

PowerShell がパラメーター名を完了します。パラメーター名を順番に繰り返し、Tab キーも繰り返し押します。

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

Web サイトの名前を入力して、各パラメーターの Tab 処理を繰り返します。既定値があるパラメーターは省略可能です。既定値をそのまま使用するには、コマンドのパラメーターを省略します。

テンプレート パラメーターにこのテンプレートの sku パラメーターのような列挙値がある場合は、パラメーターの値を周期的に繰り返して Tab キーを押します。

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

必要なテンプレート パラメーターと **Verbose** 共通パラメーターのみを指定する New-AzureResourceGroup コマンドの例を次に示します。**administratorLoginPassword** は省略します

	PS C:> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

コマンドを入力すると、不足している必須パラメーター **administratorLoginPassword** を入力するように求められます。パスワードを入力すると、セキュリティ保護された文字列値が隠されます。この対策によって、プレーンテキストでパスワードを提供することの危険性が解消されます。

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-AzureResourceGroup** は作成してデプロイしたリソース グループを返します。

わずかな手順で、複雑な Web サイトに必要なリソースを作成してデプロイしました。テンプレート ギャラリーには、このタスクを実行するために必要なほとんどすべての情報が用意されています。また、タスクは簡単に自動化されます。

## リソース グループに関する情報を取得します。

リソース グループを作成すると、AzureResourceManager モジュールのコマンドレットを使用してリソース グループを管理できます。

- サブスクリプションのすべてのリソース グループを取得するには、**Get-AzureResourceGroup** コマンドレットを使用します。

		PS C:>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- リソース グループのリソースを取得するには、**Get-AzureResource** コマンドレットおよびその ResourceGroupName パラメーターを使用します。パラメーターがない場合、Get-AzureResource は Azure サブスクリプション内のすべてのリソースを取得します。

		PS C:> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## リソース グループへの追加

- リソース グループにリソースを追加するには、**New-AzureResource** コマンドレットを使用します。このコマンドは、新しい Web サイトを TestRG リソース グループに追加します。このコマンドは、テンプレートを使用しないため、もう少し複雑です。 

        PS C:>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- テンプレート ベースの新しいデプロイをリソース グループに追加するには、**New-AzureResourceGroup デプロイ** コマンドを使用します。

		PS C:>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## リソースの移動

新しいリソース グループに、既存のリソースを移動できます。例については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。

## リソース グループの削除

- リソース グループからリソースを削除するには、**Remove-AzureResource** コマンドレットを使用します。このコマンドレットはリソースを削除しますが、リソース グループは削除しません。

	このコマンドは、TestRG リソース グループから TestSite2 Web サイトを削除します。

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- リソース グループを削除するには、**Remove-AzureResourceGroup** コマンドレットを使用します。このコマンドレットは、リソース グループとそのリソースを削除します。

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## リソース グループのトラブルシューティング
AzureResourceManager モジュールのコマンドレットで試してみたように、エラーが発生する可能性があります。このセクションのヒントを使用してエラーを解決してください。

### エラーの防止

AzureResourceManager モジュールにはエラーを防止するためのコマンドレットが含まれています。


- **Get-AzureLocation**: このコマンドレットは、各種のリソースをサポートする場所を取得します。リソースの場所を入力する前に、このコマンドレットを使用してその場所がリソースの種類をサポートしていることを確認します。


- **Test-AzureResourceGroupTemplate**: テンプレートとテンプレート パラメーターを使用する前にテストします。カスタム テンプレートまたはギャラリー テンプレートと、使用するテンプレート パラメーター値を入力します。このコマンドレットは、テンプレートが内部的に一貫性があるかどうか、およびパラメーター値セットがテンプレートと一致するかどうかをテストします。



### エラーの修正

- **Get-AzureResourceGroupLog**: このコマンドレットは、リソース グループの各デプロイのログのエントリを取得します。問題が生じた場合は、デプロイ ログを調べることにから開始します。 

- **Verbose および Debug**: AzureResourceManager モジュールのこのコマンドレットは、実際の作業を行う REST API を呼び出します。API が返すメッセージを表示するには、$DebugPreference 変数を "Continue" に設定して、コマンドで Verbose 共通パラメーターを使用します。多くの場合、メッセージはエラーの原因に関する重要な手掛かりを提供します。

- **Azure の資格情報が設定されていないか、期限が切れています**: Windows PowerShell セッションの資格情報を更新するには、Add-AzureAccount コマンドレットを使用します。発行設定ファイルの資格情報は、AzureResourceManager モジュールのコマンドレットには十分ではありません。


## 次のステップ
Getting Started (概要)

- [Azure リソース マネージャーの概要](./resource-group-overview.md)
- [Azure リソース 管理での Azure CLI for Mac, Linux, and Windows の使用](./xplat-cli-azure-resource-manager.md)
- [Azure ポータルを使用した Azure リソースの管理](./resource-group-portal.md)

アプリケーションの作成とデプロイ

- [Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](./resource-group-template-deploy.md)
- [Azure で複雑なアプリケーションを予測どおりにデプロイする](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Azure でのリソース グループのデプロイのトラブルシューティング](./resource-group-deploy-debug.md)
- [Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)
- [高度なテンプレートの操作](./resource-group-advanced-template.md)

リソースの整理

- [タグを使用した Azure リソースの整理](./resource-group-using-tags.md)

アクセスの管理と監査

- [リソースへのアクセスの管理と監査](./resource-group-rbac.md)
- [Azure リソース マネージャーでのサービス プリンシパルの認証](./resource-group-authenticate-service-principal.md)
- [Azure クラシック ポータルを使用した Azure サービス プリンシパルの新規作成](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO3-->