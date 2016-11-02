<properties
   pageTitle="開発環境とテスト環境 | Microsoft Azure"
   description="Azure リソース マネージャーのテンプレートを使用して、迅速かつ一貫した方法で開発環境およびテスト環境を作成し、削除する方法について説明します。"
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
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# Microsoft Azure の開発環境とテスト環境

カスタム アプリケーションは一般的に、運用環境へのデプロイの前に、複数の開発環境やテスト環境にデプロイされます。オンプレミスで環境を構築する場合、環境ごとに各アプリケーションへのコンピューティング リソースの調達または割り当てが行われます。多くの場合、環境は複数の物理マシンまたは仮想マシンを用いた固有の構成で構築され、手動または複雑な自動化スクリプトでデプロイされます。デプロイに数時間かかり、環境全体で見ると一貫性のない構成になることも少なくありません。

## シナリオ ##

Microsoft Azure で開発環境やテスト環境をプロビジョニングすると、料金は使用したリソースの分のみで済みます。この記事では、下図に示すように、開発環境とテスト環境の作成、保守、および削除が、Azure リソース マネージャーのテンプレートおよびパラメーター ファイルを使用することで、一貫性を保ちながらどれほど迅速に実行できるようになるかについて説明します。

![シナリオ](./media/solution-dev-test-environments/scenario.png)

上の図には、3 つの開発環境およびテスト環境が示されています。それぞれの環境に、テンプレート ファイルで指定された Web アプリケーションと SQL Database のリソースがあります。各環境のアプリケーションとデータベースの名前は異なり、環境ごとに固有のパラメーター ファイルで指定されます。

Azure リソース マネージャーの概念に習熟していない場合は、この記事を読む前に「[Azure リソース マネージャーの概要](resource-group-overview.md)」に目を通すことをお勧めします。

短時間で Azure リソース マネージャー テンプレートの使用経験を積みたい場合は、関連記事に目を通さずに、まず、この記事の手順を記載されているとおりに実行してみてください。手順を一通り実行した後で、それらの手順についていろいろ試したり、関連資料に目を通したりすると、最初に生じた不明点に対する答えの大部分を見つけることができるでしょう。

## Azure リソースの使用を計画する
アプリケーション設計の概要を用意できたら、次の項目を定義できます。

- アプリケーションに含める Azure リソース。Azure SQL Database を使用する Azure Web アプリとしてアプリケーションを作成し、デプロイできます。また、PHP と MySQL、IIS と SQL Server などのコンポーネントを使用して、仮想マシンにアプリケーションをビルドできます。「[Azure App Service、Cloud Services、および Virtual Machines の比較](app-service-web/choose-web-site-cloud-service-vm.md)」の記事が、アプリケーションで使用する必要のある Azure リソースを判断するのに役立ちます。
- 可用性、セキュリティ、スケールなど、アプリケーションが満たすサービス レベル要件。

## 既存のテンプレートをダウンロードする
Azure リソース マネージャー テンプレートでは、アプリケーションが使用する Azure リソースをすべて定義します。複数のテンプレートがあらかじめ用意されており、Azure ポータルに直接デプロイすることも、ダウンロードして変更を加え、アプリケーション コードと共にソース管理システムに保存することもできます。以下の手順を完了して、既存のテンプレートをダウンロードします。

1. [Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/) GitHub リポジトリで既存のテンプレートを参照します。リポジトリの一覧に、[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) フォルダーがあります。カスタム アプリケーションには多くの場合 Web アプリケーションと SQL データベースが含まれるため、本記事ではこれ以降、テンプレートの使い方を理解しやすいように、このテンプレートを例として使用します。このテンプレートで作成し、構成するすべての要素を詳しく説明することは、この記事の範囲を超えていますが、このテンプレートを使用して組織に実環境を構築したい場合は、「[Web アプリと SQL Database をプロビジョニングする](app-service-web/app-service-web-arm-with-sql-database-provision.md)」を参照して、十分に理解することをお勧めします。注: この記事は、2015 年 12 月版の [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) テンプレート向けに書かれています。以下のリンクは、このバージョンのテンプレートとパラメーター ファイルを指しています。
2. 201-web-app-sql-database フォルダー内の [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) ファイルをクリックして、その内容を表示します。これが Azure リソース マネージャーのテンプレート ファイルです。
3. 表示モードで、[[Raw]](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) ボタンをクリックします。
4. マウスで、このファイルの内容を選択し、"TestApp1-Template.json" という名前のファイルとしてコンピューターに保存します。
5. テンプレートの内容を確認し、次のことを確認します。
 - **リソース** セクション: このセクションでは、このテンプレートで作成された Azure リソースの種類を定義します。リソースの種類はさまざまありますが、このテンプレートでは、[Azure Web アプリ](app-service-web/app-service-web-overview.md)と [Azure SQL Database](sql-database/sql-database-technical-overview.md) のリソースを作成します。仮想マシンで Web サーバーと SQL Server を実行および管理する場合は、"[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" または "[lamp-app](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)" テンプレートを使用できますが、この記事の手順は [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) テンプレートに基づいています。
 - **パラメーター** セクション: このセクションでは、各リソースを構成できるパラメーターを定義します。テンプレートで指定するパラメーターの中には、"defaultValue" プロパティを持つものとそうでないものがあります。テンプレートを使用して Azure リソースをデプロイする場合、テンプレートで指定する defaultValue プロパティを持たないパラメーターにはすべて値を指定する必要があります。defaultValue プロパティを持つパラメーターは、値が指定されないと、テンプレート内で defaultValue パラメーターに指定した値が使用されます。

テンプレートでは、作成する Azure リソースと、各リソースの構成に使用できるパラメーターを定義します。テンプレートと、自分で設計する方法の詳細については、「[Azure リソース マネージャー テンプレートを設計するためのベスト プラクティス](best-practices-resource-manager-design-templates.md)」を参照してください。

## 既存のパラメーター ファイルのダウンロードとカスタマイズ

各環境で "*同じ*" Azure リソースを作成するが、リソースの構成は環境ごとに "*異なる*" ものにする場合があります。このときに役立つのがパラメーター ファイルです。次の手順を完了して、各環境に一意の値を持つパラメーター ファイルを作成します。

1. 201-web-app-sql-database フォルダー内の [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) ファイルの内容を表示します。これは、前の手順で保存したテンプレート ファイルのパラメーター ファイルです。
2. 表示モードで、[[Raw]](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) ボタンをクリックします。
3. マウスで、このファイルの内容を選択し、コンピューター上に 3 つのファイルとして保存して、それぞれ次のように名前を設定します。
 - TestApp1-Parameters-Development.json
 - TestApp1-Parameters-Test.json
 - TestApp1-Parameters-Pre-Production.json

3. 任意のテキスト エディターまたは JSON エディターを使用して、手順 3. で作成した開発環境のパラメーター ファイルを編集します。ファイル内のパラメーター値の右側の値を、以下の**パラメーター**の右側の "*値*" に置き換えます。
 - **siteName**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Central US*
 - **serverName**: *testapp1devsrv*
 - **serverLocation**: *Central US*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *<パスワードに置き換えます>*
 - **databaseName**: *testapp1devdb*

4. 任意のテキスト エディターまたは JSON エディターを使用して、手順 3. で作成したテスト環境のパラメーター ファイルを編集します。ファイル内のパラメーター値の右側の値を、以下の**パラメーター**の右側の "*値*" に置き換えます。
 - **siteName**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *Central US*
 - **serverName**: *testapp1testsrv*
 - **serverLocation**: *Central US*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *<パスワードに置き換えます>*
 - **databaseName**: *testapp1testdb*

5. テキスト エディターまたは JSON エディターを使用して、手順 3 で作成した運用前のパラメーター ファイルを編集します。ファイルの内容全体を次の内容に置き換えます。

	    {
    	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    	  "contentVersion" : "1.0.0.0",
    	  "parameters" : {
    	"administratorLogin" : {
    	  "value" : "testApp1Admin"
    	},
    	"administratorLoginPassword" : {
    	  "value" : "replace with your password"
    	},
    	"databaseName" : {
    	  "value" : "testapp1preproddb"
    	},
    	"hostingPlanName" : {
    	  "value" : "TestApp1PreProdPlan"
    	},
    	"serverLocation" : {
    	  "value" : "Central US"
    	},
    	"serverName" : {
    	  "value" : "testapp1preprodsrv"
    	},
    	"siteLocation" : {
    	  "value" : "Central US"
    	},
    	"siteName" : {
    	  "value" : "TestApp1PreProdApp"
    	},
    	"sku" : {
    	  "value" : "Standard"
    	},
    		"requestedServiceObjectiveName" : {
    		  "value" : "S1"
    	}
    	  }
    	}

上記の運用前用パラメーター ファイルでは、**sku** パラメーターと **requestedServiceObjectiveName** パラメーターが*追加*されていますが、開発およびテスト用パラメーター ファイルでは追加されていませんでした。これは、これらのパラメーターに指定された既定値がテンプレートにあり、開発環境とテスト環境ではその既定値が使用されるのに対して、運用前環境ではこれらのパラメーターに既定以外の値が使用されるためです。

運用前環境でこれらのパラメーターに既定以外の値が使用されるのは、運用環境で望ましいと考えられるパラメーター値をテストするためです。これで、運用前環境でもテストすることができます。これらのパラメーターはいずれも、アプリケーションで使用される、Azure [Web アプリ ホスティング プラン](https://azure.microsoft.com/pricing/details/app-service/) ( **sku**) と Azure [SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) (**requestedServiceObjectiveName**) に関連しています。SKU やサービス目標名が異なると、コストや機能も異なり、サポートするサービス レベル メトリックも異なります。

次の表に、テンプレートで指定されているこれらのパラメーターの既定値と、運用前用パラメーター ファイルで既定値の代わりに使用される値を一覧表示します。

| パラメーター | 既定値 | パラメーター ファイルの値 |
|---|---|---|
| **sku** | Free | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## 環境を作成する
Azure リソースはすべて、[Azure リソース グループ](resource-group-overview.md)内に作成する必要があります。リソース グループを使用すると、Azure リソースをグループ化し、まとめて管理できます。組織内の特定のユーザーがリソース グループの作成、変更、削除、またはリソース グループ内のリソースの表示を実行できるように、[アクセス許可](./active-directory/role-based-access-built-in-roles.md)をリソース グループに割り当てることができます。リソース グループ内のリソースのアラートと課金情報は、[Azure ポータル](https://portal.azure.com)に表示できます。リソース グループは Azure [リージョン](https://azure.microsoft.com/regions/)で作成されます。この記事では、すべてのリソースが米国中部リージョンで作成されます。実環境の作成を開始するときには、自社の要件に最も合ったリージョンを選択してください。

次の方法のいずれかを使用して、各環境のリソース グループを作成します。すべての方法で、同じ結果を達成できます。

###Azure コマンド ライン インターフェイス (CLI)

Windows、OS X、または Linux のコンピューター上に CLI が[インストールされていて](xplat-cli-install.md)、[Azure AD アカウント](./active-directory/active-directory-how-subscriptions-associated-directory.md) (職場または学校アカウントとも呼ばれます) が Azure サブスクリプションに[接続されている](xplat-cli-connect.md)ことを確認します。CLI のコマンドラインから次のコマンドを入力し、開発環境用のリソース グループを作成します。

	azure group create "TestApp1-Development" "Central US"

コマンドが成功すると、次の内容が返されます。

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

テスト環境用のリソース グループを作成するには、次のコマンドを入力します。

	azure group create "TestApp1-Test" "Central US"

運用前環境用のリソース グループを作成するには、次のコマンドを入力します。

	azure group create "TestApp1-Pre-Production" "Central US"

###PowerShell

Azure PowerShell 1.01 以降が Windows コンピューター上にインストールされていて、[Azure AD アカウント](./active-directory/active-directory-how-subscriptions-associated-directory.md) (職場または学校アカウントとも呼ばれます) が「[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)」の記事で説明されているとおりに、サブスクリプションに接続されていることを確認します。PowerShell のコマンド プロンプトから次のコマンドを入力し、開発環境用のリソース グループを作成します。

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

コマンドが成功すると、次の内容が返されます。

	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              :
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

テスト環境用のリソース グループを作成するには、次のコマンドを入力します。

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

運用前環境用のリソース グループを作成するには、次のコマンドを入力します。

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###Azure ポータル

1. [Azure ポータル](https://portal.azure.com)に [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) アカウント (職場または学校アカウントとも呼ばれます) でサインインします。[新規]、[管理]、[リソース グループ] の順にクリックして、以下の図に示すように、[リソース グループ名] ボックスに「TestApp1-Development」と入力し、サブスクリプションを選択して、[リソース グループの場所] ボックスで "米国中部" を選択します。![ポータル](./media/solution-dev-test-environments/rgcreate.png)
2. [作成] ボタンをクリックして、リソース グループを作成します。
3. [参照] をクリックし、以下の図に示すようにリソース グループの一覧を下までスクロールして、[リソース グループ] をクリックします。![ポータル](./media/solution-dev-test-environments/rgbrowse.png)
4. [リソース グループ] をクリックすると、新しいリソース グループで [リソース グループ] ブレードが表示されます。![ポータル](./media/solution-dev-test-environments/rgview.png)
5. 上記の TestApp1-Development リソース グループを作成したのと同じ方法で、TestApp1-Test と TestApp1-Pre-Production のリソース グループを作成します。

##環境へのリソースのデプロイ

次の方法のいずれかを実行し、ソリューションのテンプレート ファイルと各環境のパラメーターを使用して、Azure リソースを各環境のリソース グループにデプロイします。どちらの方法でも、同じ結果を達成できます。

###Azure コマンド ライン インターフェイス (CLI)

CLI のコマンドラインから次のコマンドを入力し、開発環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

数分間、"デプロイメントの完了を待機中" のメッセージが表示された後、コマンドが成功した場合、以下が返されます。

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1DevApp
	data:    hostingPlanName                String        TestApp1DevPlan
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1devsrv
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1devdb
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

成功しなかった場合は、すべてのエラー メッセージを解決して、再度実行してください。よくある問題として、Azure リソースの名前付けに関する制約に準拠していないパラメーター値の使用があります。「[Azure でのリソース グループのデプロイのトラブルシューティング](./resource-manager-troubleshoot-deployments-cli.md)」に、その他のトラブルシューティング ヒントが記載されています。

CLI のコマンドラインから次のコマンドを入力し、テスト環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

CLI のコマンドラインから次のコマンドを入力し、運用前環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###PowerShell

Azure PowerShell (バージョン 1.01 以降) のコマンド プロンプトから次のコマンドを入力し、開発環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

数分間、点滅カーソルが表示された後、コマンドが成功した場合、以下が返されます。

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1DevApp
	                    hostingPlanName  String                     TestApp1DevPlan
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1devsrv
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1devdb
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  成功しなかった場合は、すべてのエラー メッセージを解決して、再度実行してください。よくある問題として、Azure リソースの名前付けに関する制約に準拠していないパラメーター値の使用があります。「[Azure でのリソース グループのデプロイのトラブルシューティング](./resource-manager-troubleshoot-deployments-powershell.md)」に、その他のトラブルシューティング ヒントが記載されています。

  PowerShell のコマンド プロンプトから次のコマンドを入力し、テスト環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  PowerShell のコマンド プロンプトから次のコマンドを入力し、運用前環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

テンプレートおよびパラメーター ファイルは、ソース管理システムでバージョン管理され、アプリケーション コードと共に保持されます。また、上記のコマンドをスクリプト ファイルに保存し、コードと共に保存することもできます。

> [AZURE.NOTE] 「[Web アプリと SQL Database をプロビジョニングする](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)」の記事内にある [Azure へのデプロイ] ボタンをクリックすると、テンプレートを直接 Azure にデプロイできます。この操作はテンプレートについて理解するために役立ちますが、テンプレートとパラメーター値の編集やバージョン管理、アプリケーション コードと併せた保存は行いません。そのため、この方法の詳細は、この記事には含まれていません。

## 環境を管理する
各環境の Azure リソースの構成は、開発を進める間に、意図的に、または誤って、一貫性のない変更が加えられる場合があります。これにより、アプリケーションの開発サイクル中に、不要なトラブルシューティングや問題解決が生じることがあります。

1. [Azure ポータル](https://portal.azure.com)を開いて、環境を変更します。
2. 上記の手順を完了するために使用したのと同じアカウントを使用してサインインします。
3. 次の図に示すように、[参照]、[リソース グループ] の順にクリックします (リソース グループを表示するために、下へのスクロールが必要になる場合があります)。![ポータル](./media/solution-dev-test-environments/rgbrowse.png)
4. 上記の図のリソース グループをクリックすると、以下の図に示すように、前の手順で作成したリソース グループ ブレードと 3つのリソース グループが表示されます。TestApp1-Development リソース グループをクリックすると、ブレードが表示され、前の手順で実施した TestApp1-Development リソース グループのデプロイメントにおいて、テンプレートで作成されたリソースが一覧表示されます。[TestApp1-Development リソース グループ] ブレードで [TestApp1DevApp] をクリックし、[TestApp1DevApp Web アプリ] ブレードで [削除] をクリックして、TestApp1DevApp Web アプリ リソースを削除します。![ポータル](./media/solution-dev-test-environments/portal2.png)
5. リソースを削除してよいかどうかを確認するメッセージがポータルに表示されたら、[はい] をクリックします。TestApp1-Development リソース グループ ブレードを閉じて、もう一度開くと、削除した Web アプリを除いて表示されます。リソース グループの内容は、本来あるべき内容と異なるものになりました。複数のリソース グループから複数のリソースを削除したり、リソースの一部の構成設定を変更したりなど、いろいろ試してみてください。Azure ポータルを使用してリソース グループからリソースを削除する代わりに、PowerShell の [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) コマンドか CLI の "azure resource delete" コマンドを使用して、同じタスクを実行できます。
6. 必要な状態に戻すリソース グループにあるリソースと構成をすべて取得するには、「[環境へのリソースのデプロイ](#deploy-resources-to-environments)」セクションで使用したのと同じコマンドを使用して、環境をリソース グループにもう一度デプロイします。ただし、"Deployment1" を "Deployment2" に置き換えます。
7.  手順 4 に示した図中の TestApp1-Development ブレードの [サマリー] セクションに示すように、前の手順でポータルで削除した Web アプリが、削除するように選択した可能性のあるその他のリソースも同様に、再度存在していることを確認します。任意のリソースの構成を変更した場合、パラメーター ファイルの値も元に戻したことを確認することもできます。Azure リソース マネージャーのテンプレートを使用して環境をデプロイする利点の 1 つは、いつでも簡単に環境を既知の状態に再デプロイできる点です。
8. 以下の図中の "前回のデプロイメント" の下にあるテキストをクリックすると、ブレードにリソース グループのデプロイメント履歴が表示されます。最初のデプロイでは "Deployment1"、2 回目のデプロイでは "Deployment2" という名前を使用したため、2 つのエントリが表示されています。いずれかのデプロイをクリックすると、ブレードに各デプロイの結果が表示されます。![ポータル](./media/solution-dev-test-environments/portal3.png)



## 環境を削除する
環境を使用して作成した後に Azure リソースを使用しなくなった場合は、使用料金が発生しないように環境を削除できます。環境の削除は環境の作成よりも簡単です。前の手順で、個々の Azure リソース グループが環境ごとに作成され、リソースがリソース グループにデプロイされました。

以下の方法のいずれかを使用して、環境を削除します。すべての方法で、同じ結果を達成できます。

### Azure CLI

CLI プロンプトで、次のように入力します。

	azure group delete "TestApp1-Development"

プロンプトが表示されたら、「y」を入力し、Enter キーを押して、開発環境とそのすべてのリソースを削除します。数分後に、コマンドは次の内容を返します。

	info:    group delete command OK

CLI プロンプトで、次のように入力して、残りの環境を削除します。

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
### PowerShell

Azure PowerShell (バージョン 1.01 以降) のコマンド プロンプトで、次のコマンドを入力して、リソース グループとそのすべての内容を削除します。

	Remove-AzureRmResourceGroup -Name TestApp1-Development

リソース グループを削除することを確認するメッセージが表示されたら、「y」を入力して、Enter キーを押します。

次のように入力して、残りの環境を削除します。

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### Azure ポータル

1. Azure ポータルで、前の手順で行ったように、リソース グループを参照します。
2. TestApp1-Development リソース グループを選択し、TestApp1-Development リソース グループ ブレードで [削除] をクリックします。新しいブレードが表示されます。リソース グループ名を入力し、[削除] をクリックします。![ポータル](./media/solution-dev-test-environments/rgdelete.png)
3. TestApp1-Development リソース グループを削除したのと同じ方法で、TestApp1-Test と TestApp1-Pre-Production のリソース グループを削除します。

使用する方法にかかわらず、リソース グループとそこに含まれるすべてのリソースが存在しなくなり、リソースの料金請求は発生しなくなります。

アプリケーションの開発中に発生する Azure リソースの使用料金を最小限に抑えるために、[Azure Automation](automation/automation-intro.md) を使用して、次のようなジョブをスケジュールできます。

- 1 日の最後に仮想マシンを停止し、1 日の開始時に再起動する。
- 1 日の最後に環境全体を削除し、1 日の開始時に再作成する。
 
ここまでで、開発環境とテスト環境の作成、保守、および削除をどれほど簡単に実行できるかを体験してきました。これらの操作を詳しく知りたい場合は、上記の手順をさらに試してみたり、この記事に含まれている参照資料に目を通したりしてみてください。

## 次のステップ

- Azure リソースに対して操作のサブセットを実行できる特別なロールを Microsoft Azure AD のグループまたはユーザーに割り当てることで、各環境のさまざまなリソースに[管理制御を委任](./active-directory/role-based-access-control-configure.md)する。
- 各環境のリソース グループや個々のリソースに[タグを割り当てる](resource-group-using-tags.md)。"環境" タグをリソース グループに追加し、その値を環境名に対応するように設定できます。タグは、課金または管理の目的でリソースを整理する必要がある場合に特に役立ちます。
- [Azure ポータル](https://portal.azure.com)でリソース グループのリソースのアラートと課金情報を監視する。

<!---HONumber=AcomDC_0907_2016-->