<properties pageTitle="リソース マネージャーでの Windows PowerShell の使用" metaKeywords="ResourceManager, PowerShell, Azure PowerShell" description="Windows PowerShell を使用してリソース グループを作成する" metaCanonical="" services="" documentationCenter="" title="リソース マネージャーでの Windows PowerShell の使用" authors="juneb" solutions="" manager="mbaldwin" editor="mollybos" />

# リソース マネージャーでの Windows PowerShell の使用#

リソース マネージャーでは、Azure リソースに関するまったく新しい考え方が導入されています。個々のリソースを作成して管理するのではなく、まず、ブログ、フォト ギャラリー、SharePoint ポータル、Wiki などの複雑なサービスを想定して開始します。サービスのリソース モデルであるテンプレートを使用して、サービスをサポートするために必要なリソースでリソース グループを作成します。これにより、そのリソース グループを論理ユニットとして管理して、展開できます。

このチュートリアルでは、Windows PowerShell を Microsoft Azure のリソース マネージャーで使用する方法について説明します。ここでは、サポートするために必要なすべてのリソースを含む、SQL データベースを備えた Azure でホストされる Web サイト (または Web アプリケーション) のリソース グループを作成し、展開するプロセスを説明します。

**所要時間:** 15 分


## 前提条件##

リソース マネージャーで Windows PowerShell を使用するには、以下が必要です。

- Windows PowerShell、Version 3.0 以降。Windows PowerShell のバージョンを確認するには、「`$PSVersionTable`」と入力し、`PSVersion` の値が 3.0 以上であることを確認します。新しいバージョンをインストールするには、「[Windows Management Framework 3.0](http://www.microsoft.com/ja-jp/download/details.aspx?id=34595)」または「[Windows Management Framework 4.0](http://www.microsoft.com/ja-jp/download/details.aspx?id=40855)」を参照してください。
	
- Azure PowerShell Version 0.8.0 以降。最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、「[How to install and configure Windows Azure PowerShell (Windows Azure PowerShell のインストールおよび構成方法)](http://www.windowsazure.com/ja-jp/documentation/articles/install-configure-powershell/)」を参照してください。

このチュートリアルは、Windows PowerShell の初心者向けに設計されていますが、モジュール、コマンドレット、セッションなどの基本概念を理解していることを前提としています。Windows PowerShell の詳細については、「[Getting Started with Windows PowerShell (Windows PowerShell の概要)](http://technet.microsoft.com/ja-jp/library/hh857337.aspx)」を参照してください。

このチュートリアルに表示されているすべてのコマンドレットの詳細なヘルプを取得するには、Get-Help コマンドレットを使用します。

	Get-Help <cmdlet-name> -Detailed

たとえば、Add-AzureAccount コマンドレットについてのヘルプを確認するには、次のように入力します。

	Get-Help Add-AzureAccount -Detailed

## このチュートリアルの内容##
* [Azure PowerShell モジュールについて](#about)
* [リソース グループの作成](#create)
* [リソース グループの管理](#manage)
* [リソース グループのトラブルシューティング](#troubleshoot)
* [次のステップ](#next)



##<a id="about"></a>Azure PowerShell モジュールについて##
Version 0.8.0 以降、Azure PowerShell のインストールには次の 3 つの Windows PowerShell モジュールが含まれています。

- **Azure**: ストレージ アカウント、Web サイト、データベース、仮想マシン、メディア サービスなどの個々のリソースを管理するための従来型のコマンドレットが含まれます。詳細については、「[Azure Service Management Cmdlets (Azure サービス管理コマンドレット)](http://msdn.microsoft.com/ja-jp/library/jj152841.aspx)」を参照してください。

- **AzureResourceManager**: リソース グループの Azure リソースの作成、管理、および展開のためのコマンドレットが含まれます。詳細については、[Azure リソース マネージャー コマンドレット](http://go.microsoft.com/fwlink/?LinkID=394765)に関するページを参照してください。

- **AzureProfile**: Add-AzureAccount、Get-AzureSubscription、Switch-AzureMode などの両方のモジュールに共通のコマンドレットが含まれます。詳細については、[Azure プロファイル コマンドレット](http://go.microsoft.com/fwlink/?LinkID=394766)に関するページを参照してください。

> [ WACOM.NOTE] Azure リソース マネージャー モジュールは、現在プレビュー段階にあります。Azure サービス管理モジュールと同じ管理機能を提供しないことがあります。

Azure および Azure リソース マネージャー モジュールは、同じ Windows PowerShell セッションで使用するようには設計されていません。これらを簡単に切り替えるために、新しいコマンドレット **Switch-AzureMode** を Azure Profile モジュールに追加しました。

Azure PowerShell を使用する場合、Azure および Azure Profile モジュールのコマンドレットが既定でインポートされます。Azure Resource Manager モジュールに切り替えるには、Switch-AzureMode コマンドレットを使用します。これによって、Azure モジュールはセッションから削除され、Azure Resource Manager (および Azure Profile) モジュールがインポートされます。

AzureResoureManager モジュールに切り替えるには、次のように入力します。

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Azure モジュールに切り替えるには、次のように入力します。

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

既定では、Switch-AzureMode は、現在のセッションのみに影響します。スイッチをすべての Windows PowerShell セッションで有効にするには、Switch-AzureMode の **Global** パラメーターを使用します。

Switch-AzureMode コマンドレットについての質問は、`Get-Help Switch-AzureMode`」と入力するか、「[Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398) に関するページを参照してください。
  
ヘルプの概要と AzureResourceManager モジュールのコマンドレットの一覧を取得するには、次のように入力します。

	PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

	Name                                   Synopsis
	----                                   --------
	Get-AzureLocation                      Azure データ センターの場所とサポートされるリソースの種類を取得します
	Get-AzureResource                      Azure リソースを取得します
	Get-AzureResourceGroup                 Azure リソース グループを取得します
	Get-AzureResourceGroupDeployment       リソース グループ内の展開を取得します。
	Get-AzureResourceGroupGalleryTemplate  ギャラリー内のリソース グループ テンプレートを取得します
	Get-AzureResourceGroupLog              リソース グループの展開ログを取得します
	New-AzureResource                      リソース グループに新しいリソースを作成します
	New-AzureResourceGroup                 Azure リソース グループとそのリソースを作成します
	New-AzureResourceGroupDeployment       リソース グループに Azure の展開を追加します。
	Remove-AzureResource                   リソースを削除します
	Remove-AzureResourceGroup              リソース グループを削除します。
	Save-AzureResourceGroupGalleryTemplate ギャラリー テンプレートを JSON ファイルに保存します
	Set-AzureResource                      Azure リソースのプロパティを変更します。
	Stop-AzureResourceGroupDeployment      リソース グループの展開を取り消します
	Test-AzureResourceGroupTemplate        リソース グループ テンプレートまたはテンプレート パラメーターのエラーを検出します


コマンドレットの完全なヘルプを取得するには、次の形式でコマンドを入力します。

	Get-Help <cmdlet-name> -Full

たとえば、次のように入力します。

	Get-Help Get-AzureLocation -Full

  
#<a id="create"></a> Web サイトとデータベースのリソース グループを作成する#

チュートリアルのこのセクションでは、SQL データベースを使用して Web サイトのリソース グループを作成および展開するプロセスを説明します。

このタスクを実行するためには、Azure、SQL、Web サイト、またはリソース管理の専門家である必要はありません。テンプレートには、リソース グループのモデルと必要になる可能性のあるすべてのリソースが用意されています。タスクを自動化するために Windows PowerShell を使用しているため、これらのプロセスを大規模なタスクのスクリプトのモデルとして使用することができます。

## 手順 1. Azure リソース マネージャーに切り替える
1. Windows PowerShell を起動します。Windows PowerShell コンソールや Windows PowerShell ISE など、必要なホスト プログラムを使用することができます。

2. **Switch-AzureMode** コマンドレットを使用して、AzureResourceManager モジュールおよび AzureProfile モジュールのコマンドレットをインポートします。

	`PS C:\>Switch-AzureMode AzureResourceManager`

3. Azure アカウントを Windows PowerShell セッションに追加するには、**Add-AzureAccount** コマンドレットを使用します。

    `PS C:\> Add-AzureAccount`

このコマンドレットを使用すると、電子メール アドレスとパスワードを求められます。アカウント設定がダウンロードされるため、Windows PowerShell で使用できるようになります。

アカウント設定の有効期限が切れるため、ときどき更新する必要があります。アカウント設定を更新するには、**Add-AzureAccount** をもう一度実行します。

> [ WACOM.NOTE] AzureResourceManager モジュールには、Add-AzureAccount が必要です。発行設定ファイルでは不十分です。



## 手順 2. ギャラリー テンプレートを選択する##

リソース グループとそのリソースを作成するにはいくつかの方法がありますが、最も簡単な方法はリソース グループ テンプレートを使用することです。*リソース グループ テンプレート* は、リソース グループのリソースを定義する JSON 文字列です。文字列には、名前やサイズなどのユーザー定義の値の "パラメーター" と呼ばれるプレースホルダーが含まれます。

Azure にはリソース グループ テンプレートのギャラリーがホストされ、独自のテンプレートを最初から、またはギャラリー テンプレートを編集して、作成することができます。このチュートリアルでは、ギャラリー テンプレートを使用します。

Azure リソース グループ テンプレート ギャラリーでテンプレートを検索するには、**Get-AzureResourceGroupGalleryTemplate**  コマンドレットを使用します。

Windows PowerShell プロンプトで、次のように入力します。
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate

コマンドレットは、Publisher プロパティと Identity プロパティを持つギャラリー テンプレートの一覧を返します。**Identity** プロパティを使用して、コマンドのテンプレートを識別します。

    Publisher       Identity
    ---------       --------
    Ghost           Ghost.Ghost.0.1.0-preview1
	Joomla          Joomla.Joomla.0.1.0-preview1
	Microsoft       Microsoft.ASPNETEmptySite.0.1.0-preview1
    Microsoft       Microsoft.ASPNETstarterSite.0.1.0-preview1
    Microsoft       Microsoft.Bakery.0.1.0-preview1
    Microsoft       Microsoft.Boilerplate.0.1.0-preview1
	...

ヒント: 最後のコマンドを取り消すには、上方向キーを押します。

Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json テンプレートは、興味深いものです。ギャラリー テンプレートの詳細を確認するには、**Identity** パラメーターを使用します。Identity パラメーターの値は、そのテンプレートの ID です。

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json

コマンドレットは、説明など、テンプレートに関するより多くの情報を持つオブジェクトを返します。

	<p>Windows Azure の Web サイトでは、安全で柔軟性の高い開発、展開、
	拡張オプションを、あらゆるサイズの Web アプリケーションに提供します。
	インフラストラクチャ管理の煩雑さを伴わずに、既存のツールを利用して
	アプリケーションを作成および展開できます。</p>
 
このテンプレートは、ニーズを満たしているようです。ディスクに保存して、詳しく見てみましょう。
 
## 手順 3. テンプレートを確認する

テンプレートをディスク上の JSON ファイルに保存します。この手順は必須ではありませんが、テンプレートを簡単に表示することができます。テンプレートを保存するには、**Save-AzureResourceGroupGalleryTemplate** コマンドレットを使用します。テンプレートを指定するには **Identity** パラメーターを使用し、ディスク上のパスを指定するには **Path** パラメーターを使用します。

Save-AzureResourceGroupGalleryTemplate はテンプレートを保存して、パス、JSON テンプレート ファイルのファイル名を返します。

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json -Path D:\Azure\Templates

	Path
	----
	D:\Azure\Templates\Microsoft.WebSite.0.1.0-preview1.json


テンプレート ファイルは、メモ帳などのテキスト エディターで表示できます。各テンプレートには、**resources** セクションと **parameters** セクションがあります。

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
            "edition": "Web",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "1073741824"
          }
        },

テンプレートの **parameters** セクションはすべてのリソースで定義されるパラメーターのコレクションです。これには、databaseName プロパティ、serverLocation プロパティ、および照合順序のプロパティが含まれます。

	"parameters": {
	...    

    "serverLocation": {
      "type": "string"
    }, 
	...

    "databaseName": {
      "type": "string"
    },
    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

一部のパラメーターには既定値があります。テンプレートを使用する場合、これらのパラメーターには値を指定する必要がありません。値を指定しないと、既定値が使用されます。

	"collation": {
	      "type": "string",
	      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
	    }

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


テンプレートを使用する準備が整いましたが、実行する前に、各リソースの場所を見つける必要があります。

## 手順 4. リソースの種類の場所を取得する

ほとんどのテンプレートでは、リソース グループ内の各リソースの場所を指定するように求められます。すべてのリソースは Azure データ センターにありますが、すべての Azure データ センターがすべてのリソースの種類をサポートしているわけではありません。

そのリソースの種類をサポートしている任意の場所を選択します。リソース グループのリソースは同じ場所にある必要はありませんし、リソース グループまたはサブスクリプションと同じ場所にある必要もありません。

各リソースの種類をサポートする場所を取得するには、**Get-AzureLocation** コマンドレットを使用します。出力からの抜粋を次に示します。(この出力は実際の出力とは異なる場合があります。詳細は時間の経過と共に変化する可能性があります)。

	Name                                 Locations
	----                                 ---------
	ResourceGroup                        East Asia, South East Asia, East US, West US, North Central US,
										 South Central US, Central US, North Europe, West Europe

	Microsoft.Sql/servers/databases      Brazil South, Central US, East Asia, East US, East US 2, Japan
	                                     East, Japan West, North Central US, North Europe, South Central US,
	                                     Southeast Asia, West Europe, West US

	Microsoft.Web/serverFarms            Brazil South, East Asia, East US, Japan East, Japan West, North
	                                     Central US, North Europe, West Europe, West US

	Microsoft.Web/sites                  Brazil South, East Asia, East US, Japan East, Japan West, North
	                                     Central US, North Europe, West Europe, West US

これで、リソース グループを作成するために必要な情報が得られました。

##ステップ 5. リソース グループを作成する
 
この手順では、リソース グループ テンプレートを使用してリソース グループを作成します。詳細については、ディスク上の Microsoft.WebSiteSQLDatabase.0.1.0-preview1 JSON ファイルを開いて、理解してください。

リソース グループを作成するには、**New-AzureResourceGroup** コマンドレットを使用します。

コマンドは **Name** パラメーターを使用してリソース グループの名前を指定し、**Location** パラメーターを使用して場所を指定します。**Get-AzureLocation** の出力を使用して、リソース グループの場所を選択します。**GalleryTemplateIdentity** パラメーターを使用して、ギャラリー テンプレートを指定します。

	PS C:\> New-AzureResourceGroup ` 
			-Name TestRG1 `
			-Location "East Asia" `
			-GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
            ....

テンプレート名を入力すると、New-AzureResourceGroup がテンプレートを取得し、解析して、テンプレート パラメーターをコマンドに動的に追加します。これにより、テンプレート パラメーターの値の指定が非常に簡単になります。また、必須のパラメーター値を忘れた場合は、Windows PowerShell から値を求められます。

**動的なテンプレート パラメーター**

パラメーターを取得するには、マイナス記号 (-) を入力してパラメーター名を示し、Tab キーを押します。または、siteName などのパラメーター名の最初の数文字を入力して、Tab キーを押します。

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-si<TAB>

Windows PowerShell がパラメーター名を完了します。パラメーター名を順番に繰り返し、Tab キーも繰り返し押します。

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName 

Web サイトの名前を入力して、各パラメーターの Tab 処理を繰り返します。既定値があるパラメーターは省略可能です。既定値をそのまま使用するには、コマンドのパラメーターを省略します。

テンプレート パラメーターにこのテンプレートの sku パラメーターのような列挙値がある場合は、パラメーターの値を周期的に繰り返して Tab キーを押します。

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName TestSite -sku <TAB>

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName TestSite -sku Free<TAB>

		PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
		-siteName TestSite -sku Basic<TAB>

必要なテンプレート パラメーターと **Verbose** 共通パラメーターのみを指定する New-AzureResourceGroup コマンドの例を次に示します。**administratorLoginPassword** は省略します (バックティック (`) は、Windows PowerShell の行連結文字です)。

	PS C:\> New-AzureResourceGroup 
	-Name TestRG `
	-Location "East Asia" `
	-GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
	-siteName TestSite `
	-hostingPlanName TestPlan `
	-siteLocation "North Europe" `
	-serverName testserver `
	-serverLocation "West US" `
	-administratorLogin Admin01 `
	-databaseName TestDB `
	-Verbose

コマンドを入力すると、不足している必須パラメーター **administratorLoginPassword** を入力するように求められます。パスワードを入力すると、セキュリティ保護された文字列値が隠されます。この対策によって、プレーンテキストでパスワードを提供することの危険性が解消されます。

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-AzureResourcGroup** は作成して展開したリソース グループを返します。詳細な出力などのコマンドの出力を示します。

	VERBOSE: 3:47:30 PM - Create resource group 'TestRG' in location 'East Asia'
	VERBOSE: 3:47:30 PM - Template is valid.
	VERBOSE: 3:47:31 PM - Create template deployment 'Microsoft.WebSiteSQLDatabase.0.1.0-preview1'
	using template https://gallerystoreprodch.blob.core.windows.net/prod-microsoft-windowsazure-gallery/8D6B920B-10F4-4B5A-B3DA-9D398FBCF3EE.PUBLICGALLERYITEMS.MICROSOFT.WEBSITESQLDATABASE.0.1.0-PREVIEW1/DeploymentTemplates/Website_NewHostingPlan_SQL_NewDB-Default.json.
	VERBOSE: 3:47:43 PM - Resource Microsoft.Sql/servers 'testserver' provisioning status is succeeded
	VERBOSE: 3:47:43 PM - Resource Microsoft.Web/serverFarms 'TestPlan' provisioning status is
	succeeded
	VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/databases 'testserver/TestDB' provisioning status is succeeded
	VERBOSE: 3:47:47 PM - Resource microsoft.insights/autoscalesettings 'TestPlan-TestRG'
	provisioning status is succeeded
	VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/firewallrules
	'testserver/AllowAllWindowsAzureIps' provisioning status is succeeded
	VERBOSE: 3:47:50 PM - Resource Microsoft.Web/Sites 'TestSite' provisioning status is succeeded
	VERBOSE: 3:47:54 PM - Resource Microsoft.Web/Sites/config 'TestSite/web' provisioning status is succeeded
	VERBOSE: 3:47:54 PM - Resource microsoft.insights/alertrules 'ServerErrors-TestSite' provisioning
	status is succeeded
	VERBOSE: 3:47:57 PM - Resource microsoft.insights/components 'TestSite' provisioning status is succeeded
	
	
	ResourceGroupName : TestRG
	Location          : eastasia
	ProvisioningState : Succeeded
	Resources         :
                    Name                   Type                                  Location
                    =====================  ====================================  =========
                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                    TestSite               microsoft.insights/components         centralus
                    testserver             Microsoft.Sql/servers                 westus
                    TestDB                 Microsoft.Sql/servers/databases       westus
                    TestPlan               Microsoft.Web/serverFarms             westus
                    TestSite               Microsoft.Web/sites                   westus


わずかな手順で、複雑な Web サイトに必要なリソースを作成して展開しました。
テンプレート ギャラリーには、このタスクを実行するために必要なほとんどすべての情報が用意されています。
また、タスクは簡単に自動化されます。

#<a id="manage"></a> リソース グループの管理

リソース グループを作成すると、AzureResourceManager モジュールのコマンドレットを使用してリソース グループの管理、変更、リソース グループへのリソースの追加、および削除ができます。

- サブスクリプションのリソース グループを取得するには、**Get-AzureResourceGroup** コマンドレットを使用します。

		PS C:>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Resources         :
	                    Name                   Type                                  Location
	                    =====================  ====================================  =========
	                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
	                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	                    TestSite               microsoft.insights/components         centralus
	                    testserver             Microsoft.Sql/servers                 westus
	                    TestDB                 Microsoft.Sql/servers/databases       westus
	                    TestPlan               Microsoft.Web/serverFarms             westus
	                    TestSite               Microsoft.Web/sites                   westus

- リソース グループのリソースを取得するには、**GetAzureResource** コマンドレットおよびその ResourceGroupName パラメーターを使用します。パラメーターがない場合、Get-AzureResource は Azure サブスクリプション内のすべてのリソースを取得します。

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		Name                   ResourceType                          Location
		----                   ------------                          --------
		ServerErrors-TestSite  microsoft.insights/alertrules         eastus
	    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	    TestSite               microsoft.insights/components         centralus
	    testserver             Microsoft.Sql/servers                 westus
	    TestDB                 Microsoft.Sql/servers/databases       westus
	    TestPlan               Microsoft.Web/serverFarms             westus
	    TestSite               Microsoft.Web/sites                   westus



- リソース グループにリソースを追加するには、**New-AzureResource** コマンドレットを使用します。このコマンドは、新しい Web サイトを TestRG リソース グループに追加します。このコマンドは、テンプレートを使用しないため、もう少し複雑です。

		PS C:\>New-AzureResource -Name TestSite2 `
		-Location "North Europe" `
		-ResourceGroupName TestRG `
		-ResourceType "Microsoft.Web/sites" `
		-ApiVersion 2004-04-01 `
		-PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- テンプレート ベースの新しい展開をリソース グループに追加するには、**New-AzureResourceGroupDeployment** コマンドを使用します。

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.1.0-preview1 `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteMode Limited `
		-computeMode Dedicated `
		-siteLocation "North Europe" 
		-subscriptionID "9b14a38b-4b93-4554-8bb0-3cefb47abcde" `
		-resourceGroup TestRG


- リソース グループからリソースを削除するには、**Remove-AzureResource** コマンドレットを使用します。このコマンドレットはリソースを削除しますが、リソース グループは削除しません。

	このコマンドは、TestRG リソース グループから TestSite2 Web サイトを削除します。

		Remove-AzureResource -Name TestSite2 `
			-Location "North Europe" `
			-ResourceGroupName TestRG `
			-ResourceType "Microsoft.Web/sites" `
			-ApiVersion 2004-04-01

- リソース グループを削除するには、**Remove-AzureResourceGroup** コマンドレットを使用します。このコマンドレットは、リソース グループとそのリソースを削除します。

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


#<a id="troubleshoot"></a> リソース グループのトラブルシューティング
AzureResourceManager モジュールのコマンドレットで試してみたように、エラーが発生する可能性があります。このセクションのヒントを使用してエラーを解決してください。

## エラーの防止

AzureResourceManager モジュールにはエラーを防止するためのコマンドレットが含まれています。


- **Get-AzureLocation**: このコマンドレットはリソースの各種類をサポートする場所を取得します。リソースの場所を入力する前に、このコマンドレットを使用してその場所がリソースの種類をサポートしていることを確認します。


- **Test-AzureResourceGroupTemplate**: 使用する前にテンプレートとテンプレート パラメーターをテストします。カスタム テンプレートまたはギャラリー テンプレートと、使用するテンプレート パラメーター値を入力します。このコマンドレットは、テンプレートが内部的に一貫性があるかどうか、およびパラメーター値セットがテンプレートと一致するかどうかをテストします。



## エラーの修正

- **Get-AzureResourceGroupLog**: このコマンドレットはリソース グループの各展開のログのエントリを取得します。問題が生じた場合は、展開ログを調べることにから開始します。

- **Verbose および Debug**: AzureResourceManager モジュールのコマンドレットは実際の作業を行う REST API を呼び出します。API が返すメッセージを表示するには、$DebugPreference 変数を "Continue" に設定して、コマンドで Verbose 共通パラメーターを使用します。多くの場合、メッセージはエラーの原因に関する重要な手掛かりを提供します。

- **Azure の資格情報が設定されていないか、期限が切れています**: Windows PowerShell セッションの資格情報を更新するために、Add-AzureAccount コマンドレットを使用します。発行設定ファイルの資格情報は、AzureResourceManager モジュールのコマンドレットには十分ではありません。


#<a id="next"></a> 次のステップ
リソース マネージャーでの Windows PowerShell の使用の詳細については、次を参照してください。
 
- [Azure リソース マネージャー コマンドレットに関するページ](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): AzureResourceManager モジュールのコマンドレットを使用する方法について説明します。
- [Azure blog (Azure のブログ)](http://blogs.msdn.com/windowsazure): Azure の新機能について説明します。
- [Windows PowerShell blog (Windows PowerShell ブログ)](http://blogs.msdn.com/powershell): Windows PowerShell の新機能について説明します。
- ["Hey, Scripting Guy!"Blog ("Hey, Scripting Guy!" ブログ)](http://blogs.technet.com/b/heyscriptingguy/): 実際のヒントとテクニックをコミュニティから取得します。
- [Using the Azure Cross-Platform Command-Line Interface with the Resource Manager (リソース マネージャーでの Azure クロス プラットフォーム コマンド ライン インターフェイスの使用)](http://www.windowsazure.com/ja-jp/documentation/articles/xplat-cli-azure-resource-manager/): リソース マネージャーの操作を自動化する別の方法を説明します。

