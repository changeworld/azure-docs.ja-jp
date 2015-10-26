<properties
   pageTitle="開発環境とテスト環境 | Microsoft Azure"
   description="Azure リソース マネージャーのテンプレートを使用して、迅速かつ一貫した方法で開発環境およびテスト環境を作成し、削除する方法について説明します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="jdial"/>

# Microsoft Azure の開発環境とテスト環境

カスタム アプリケーションは一般的に、運用環境へのデプロイの前に、複数の開発環境やテスト環境にデプロイされます。オンプレミスで環境を構築する場合、環境ごとに各アプリケーションへのコンピューティング リソースの調達または割り当てが行われます。多くの場合、環境は複数の物理マシンまたは仮想マシンを用いた固有の構成で構築され、手動または複雑な自動化スクリプトでデプロイされます。デプロイに数時間かかり、環境全体で見ると一貫性のない構成になることも少なくありません。

## シナリオ ##

Microsoft Azure で開発環境やテスト環境をプロビジョニングすると、料金は使用したリソースの分のみで済みます。この記事では、下図に示すように、開発環境とテスト環境の作成、保守、および削除が、Azure リソース マネージャーのテンプレートおよびパラメーター ファイルを使用することで、一貫性を保ちながらどれほど迅速に実行できるようになるかについて説明します。

![シナリオ](./media/solution-dev-test-environments-preview-portal/scenario.png)

上の図には、3 つの開発環境およびテスト環境が示されています。それぞれに、Web アプリケーションと SQL データベースがあります。環境ごとにアプリケーションとデータベースの名前が異なっています。この記事では、テンプレートを使用してすべての環境に同じリソースをデプロイし、一意のパラメーター ファイルを使用して環境ごとに異なるリソース構成を指定する方法について説明します。

Azure リソース マネージャーの概念に習熟していない場合は、この記事を読む前に「[Azure リソース マネージャーの概要](resource-group-overview.md)」に目を通すことをお勧めします。

短時間で Azure リソース マネージャー テンプレートの使用経験を積みたい場合は、関連記事に目を通さずに、まず、この記事の手順を記載されているとおりに実行してみてください。手順を進めるうちに、さまざまな不明点が生じると思います。手順を一通り実行した後で、それらの手順についていろいろ試したり、関連資料に目を通したりすることで、不明点に対する答えの大部分を見つけることができるでしょう。

## Azure リソースの使用を計画する
アプリケーション設計の概要を用意できたら、次の項目を定義できます。

- アプリケーションに含める Azure リソース。Azure SQL Database を使用する Azure Web アプリとしてアプリケーションを作成し、デプロイできます。また、PHP と MySQL、IIS と SQL Server などのコンポーネントを使用して、仮想マシンにアプリケーションをビルドできます。「[Azure App Service、Cloud Services、および Virtual Machines の比較](app-service-web/choose-web-site-cloud-service-vm.md)」の記事が、アプリケーションで使用する必要のある Azure リソースを判断するのに役立ちます。
- 可用性、セキュリティ、スケールなど、アプリケーションが満たすサービス レベル要件。

## Azure リソース マネージャーのテンプレートを作成する
Azure リソース マネージャー テンプレートでは、アプリケーションが使用する Azure リソースをすべて定義します。複数のテンプレートがあらかじめ用意されており、Azure プレビュー ポータルに直接デプロイすることも、ダウンロードして変更を加え、アプリケーション コードと共にソース管理システムに保存することもできます。アプリケーションで使用したいリソースが既存のテンプレートに含まれている可能性があります。用意されているテンプレートの一覧は、[Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/) GitHub リポジトリにあります。

リポジトリの一覧に、[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) フォルダーがあります。カスタム アプリケーションには多くの場合 Web アプリケーションと SQL データベースが含まれるため、本記事ではこれ以降、テンプレートの使い方を理解しやすいように、このテンプレートを例として使用します。このテンプレートで作成し、構成するすべての要素を詳しく説明することは、この記事の範囲を超えていますが、このテンプレートを使用して組織に実環境を構築したい場合は、「[Web アプリと SQL Database をプロビジョニングする](app-service-web/app-service-web-arm-with-sql-database-provision.md)」を参照して、十分に理解することをお勧めします。

> [AZURE.NOTE]「[Web アプリと SQL Database をプロビジョニングする](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)」の記事内にある [Azure へのデプロイ] ボタンをクリックすると、テンプレートを直接 Azure にデプロイできます。この操作はテンプレートについて詳しく知るのに役立ちますが、テンプレートとパラメーター値の編集やバージョン管理、アプリケーション コードと併せた保存はできません。本記事のこの手順では、テンプレートとパラメーター値をアプリケーション コードと共に保存してバージョン管理する方法について説明します。

  **手順 1:** 201-web-app-sql-database フォルダー内の [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) ファイルの内容を表示します。これが Azure リソース マネージャーのテンプレート ファイルです。表示モードで、[[Raw]](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json) ボタンをクリックします。マウスで、このファイルの内容全体を選択し、"TestApp1 Template.json" という名前のファイルとしてコンピューターに保存します。

テンプレート ファイル内にある "リソース" セクションで、このテンプレートで作成する Azure リソースを定義します。リソースの種類はさまざまありますが、このテンプレートでは、[Azure Web アプリ](app-service-web/app-service-web-overview.md)と [Azure SQL Database](sql-database/sql-database-technical-overview.md) のリソースを作成します。

また、"パラメーター" セクションでは、各リソースで構成できるパラメーターを定義します。テンプレートで指定するパラメーターの中には、"defaultValue" プロパティを持つものとそうでないものがあります。テンプレートを使用して Azure リソースをデプロイする場合、defaultValue プロパティを持たないパラメーターにはすべて値を指定する必要があります。defaultValue プロパティを持つパラメーターは、値が指定されないと、テンプレート内で defaultValue パラメーターに指定した値が使用されます。

テンプレートでは、作成する Azure リソースと、各リソースの構成に使用できるパラメーターを定義します。一般的には、各開発環境やテスト環境に同じリソースを作成します。テンプレートと、自分で設計する方法の詳細については、「[Azure リソース マネージャー テンプレートを設計するためのベスト プラクティス](best-practices-resource-manager-design-templates.md)」を参照してください。

## パラメーター ファイルを作成する

各環境に同じ Azure リソースを作成する必要がありますが、リソースの構成は環境ごとに変えることができます。このときに役立つのがパラメーター ファイルです。

  **手順 2:** 201-web-app-sql-database フォルダー内の [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) ファイルの内容を表示します。これは、手順 1 で保存したテンプレート ファイルのパラメーター ファイルです。表示モードで、[[Raw]](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json) ボタンをクリックします。マウスで、このファイルの内容全体を選択し、コンピューター上に 3 つのファイルとして保存して、それぞれ次のように名前を設定します。

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **手順 3:** 任意のテキスト エディターまたは JSON エディターを使用し、手順 2 で作成した開発環境およびテスト環境用パラメーター ファイルを編集して、ファイル内の既存の値を次の値に置き換えます。

  --TestApp1-Parameters-Development.json--

| パラメーター | 説明 |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | 米国中央部 |
| **serverName** | testapp1dev |
| **serverLocation** | 米国中央部 |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| パラメーター | 説明 |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | 米国中央部 |
| **serverName** | testapp1test |
| **serverLocation** | 米国中央部 |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

手順 6 で開発環境およびテスト環境の Azure Web アプリと Azure SQL Database のリソースに対する固有の構成を作成する際に、これらのパラメーター ファイルを使用します。

 **手順 4:** 手順 2 で作成した TestApp1-Parameters-Pre-Production.json パラメーター ファイルを編集します。ファイルの内容全体を次の内容に置き換えます。

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
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

運用前環境でこれらのパラメーターに既定以外の値が使用されるのは、運用環境で望ましいと考えられるパラメーター値をテストするためです。これらのパラメーターはいずれも、アプリケーションで使用される、Azure [Web アプリ ホスティング プラン](http://azure.microsoft.com/pricing/details/app-service/) ( **sku**) と Azure [SQL Database](http://azure.microsoft.com/pricing/details/sql-database/) (**requestedServiceObjectiveName**) に関連しています。SKU やサービス目標名が異なると、コストや機能も異なり、サポートするサービス レベル メトリックも異なります。

次の表に、テンプレートで指定されているこれらのパラメーターの既定値と、運用前用パラメーター ファイルで既定値の代わりに使用される値を一覧表示します。

| パラメーター | 既定値 | パラメーター ファイルの値 |
|---|---|---|
| **sku** | Free | Standard |
| **requestedServiceObjectiveName** | S0 | S1 |

## 環境を作成する
Azure リソースはすべて、[Azure リソース グループ](azure-portal/resource-group-portal.md)内に作成する必要があります。リソース グループを使用すると、Azure リソースをグループ化し、まとめて管理できます。組織内の特定のユーザーがリソース グループの作成、変更、削除、またはリソース グループ内のリソースの表示を実行できるように、[アクセス許可](azure-portal/resource-group-rbac.md)をリソース グループに割り当てることができます。リソース グループ内のリソースのアラートと請求情報は、[Azure プレビュー ポータル](https://portal.azure.com)で表示できます。また、リソース グループは Azure [リージョン](http://azure.microsoft.com/regions/)に作成されます。この記事では、すべてのリソースが米国中部リージョンに作成されます。実環境の作成を開始する際には、自社の要件に最も合ったリージョンを選択してください。

  **手順 5:** 以下の方法のいずれかを使用して、開発環境およびテスト環境のリソース グループを作成します。どちらの方法でも、まったく同じ結果を達成できます。

  **方法 1:** Azure コマンド ライン インターフェイス (CLI)

  Windows、OS X、または Linux のコンピューター上に CLI が[インストールされていて](xplat-cli-install.md)、組織 ID が Azure サブスクリプションに[接続されている](xplat-cli-connect.md)ことを確認します。CLI のコマンドラインから次のコマンドを入力し、開発環境用のリソース グループを作成します。

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

  **方法 2:** PowerShell

  PowerShell が Windows コンピューター上にインストールされていて、「[Azure PowerShell のインストールと構成の方法](powershell-install-configure.md)」の記事で説明されているとおりにサブスクリプションに接続されていることを確認します。PowerShell のコマンド プロンプトから次のコマンドを入力し、開発環境用のリソース グループを作成します。Azure PowerShell 1.0 プレビューを使用する場合、下記のようにコマンドは **New-AzureRmResourceGroup** です。Azure PowerShell の 1.0 プレビューよりも前のバージョンを使用している場合、コマンドは **New-AzureResourceGroup** です。

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

  コマンドが成功すると、次の内容が返されます。

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  テスト環境用のリソース グループを作成するには、次のコマンドを入力します。

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

  運用前環境用のリソース グループを作成するには、次のコマンドを入力します。

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **手順 6:** 次の方法のいずれかを実行し、アプリケーションのテンプレート ファイルと各環境のパラメーターを使用して Azure リソースをリソース グループにデプロイします。どちらの方法でも、まったく同じ結果を達成できます。

  **方法 1:** Azure コマンド ライン インターフェイス (CLI)

  CLI のコマンドラインから次のコマンドを入力し、開発環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  コマンドが成功すると、次の内容が返されます。

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
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  成功しなかった場合は、すべてのエラー メッセージを解決して、再度実行してください。よくある問題として、Azure リソースの名前付けに関する制約に準拠していないパラメーター値の使用があります。「[Azure でのリソース グループのデプロイのトラブルシューティング](virtual-machines/resource-group-deploy-debug.md)」に、その他のトラブルシューティング ヒントが記載されています。

  CLI のコマンドラインから次のコマンドを入力し、テスト環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  CLI のコマンドラインから次のコマンドを入力し、運用前環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **方法 2:** PowerShell

  PowerShell のコマンド プロンプトから次のコマンドを入力し、開発環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。Azure PowerShell 1.0 プレビューを使用する場合、下記のようにコマンドは **New-AzureRmResourceGroupDeployment** です。Azure PowerShell 1.0 プレビューよりも前のバージョンを使用している場合、コマンドは **New-AzureResourceGroupDeployment** です。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  コマンドが成功すると、次の内容が返されます。

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  成功しなかった場合は、すべてのエラー メッセージを解決して、再度実行してください。よくある問題として、Azure リソースの名前付けに関する制約に準拠していないパラメーター値の使用があります。「[Azure でのリソース グループのデプロイのトラブルシューティング](virtual-machines/resource-group-deploy-debug.md)」に、その他のトラブルシューティング ヒントが記載されています。

  PowerShell のコマンド プロンプトから次のコマンドを入力し、テスト環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  PowerShell のコマンド プロンプトから次のコマンドを入力し、運用前環境用に作成したリソース グループにリソースをデプロイします。コマンド内の [path] は、前の手順で保存したファイルへのパスに置き換えてください。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

テンプレートおよびパラメーター ファイルは、ソース管理システムでバージョン管理され、アプリケーション コードと共に保持されます。また、上記のコマンドをスクリプト ファイルに保存し、コードと共に保存することもできます。

## 環境を管理する
各環境の Azure リソースの構成は、開発を進める間に、意図的に、または誤って、一貫性のない変更が加えられる場合があります。これにより、アプリケーションの開発サイクル中に、不要なトラブルシューティングや問題解決が生じることがあります。

  **手順 7:** 環境に変更を加えます。[Azure プレビュー ポータル](https://portal.azure.com)を開き、上記の手順を完了するために使用したのと同じアカウントを使用してサインインします。次の図に示すように、[すべて参照]、[リソース グループ] を順にクリックします (リソース グループを表示するために [参照] ブレードで下へスクロールする必要がある場合があります)。前の手順のいずれかの方法を使用して作成した 3 つのリソース グループがすべて表示されます。次に示すように TestApp1-Development リソース グループをクリックします。

  ![ポータル](./media/solution-dev-test-environments-preview-portal/portal1.png)

  TestApp1-Development リソース グループをクリックするとブレードが表示され、前の手順で実施したリソース グループのデプロイメントにおいてテンプレートで作成されたリソースが一覧表示されます。次に示すように、[TestApp1Dev]、[削除] を順にクリックして TestApp1Dev Web アプリ リソースを削除します。

  ![ポータル](./media/solution-dev-test-environments-preview-portal/portal2.png)

  リソースを削除してよいかどうかを確認するメッセージがポータルに表示されたら、[はい] をクリックします。リソース グループの内容は、本来あるべき内容と異なるものになりました。複数のリソース グループから複数のリソースを削除したり、リソースの一部の構成設定を変更したりなど、いろいろ試してみてください。

> [AZURE.NOTE]リソース グループからリソースを削除するときに、Azure プレビュー ポータルを使用する代わりに、PowerShell の [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) コマンドか CLI の "azure resource delete" コマンドを使用して、同じタスクを実行できます。

  **手順 8:** 手順 6 で使用したのと同じコマンドを使用して、リソース グループに環境を再デプロイします。ただし、今回は "Deployment1" を "Deployment2" で置き換えてください。 次の図の [概要] セクションに示されているように、テンプレート内のすべてのリソースが再び TestApp1-Development リソース グループに存在しています。Azure リソース マネージャーのテンプレートを使用して環境をデプロイする利点の 1 つは、いつでも簡単に環境を既知の状態に再デプロイできる点です。

  ![ポータル](./media/solution-dev-test-environments-preview-portal/portal3.png)

  図中の "前回のデプロイメント" の下にあるテキストをクリックすると、ブレードにリソース グループのデプロイ履歴が表示されます。最初のデプロイでは "Deployment1"、2 回目のデプロイでは "Deployment2" という名前を使用したため、2 つのエントリが表示されています。いずれかのデプロイメントをクリックすると、ブレードに各デプロイの結果が表示されます。

## 環境を削除する
環境の作成後に Azure リソースを使用しなくなった場合は、使用料金が発生しないように環境を削除できます。環境の削除は環境の作成よりも簡単です。前の手順では、環境ごとに個別の Azure リソース グループを作成しました。リソース グループを削除すると、グループに含まれているリソースもすべて削除されます。この点を念頭に置いて、次のいずれかの方法を用いて、前にデプロイした環境 (リソース グループ) をグループ内のすべての Azure リソースと共に削除します。

  **手順 9:** 以下の方法のいずれかを使用して環境を削除します。どちらの方法でも、まったく同じ結果を達成できます。

  **方法 1: Azure CLI**

  CLI プロンプトで、次のように入力します。

	azure group delete "TestApp1-Development"

  メッセージが表示されたときに "y" を入力すると、次の内容が返されます。

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  CLI プロンプトで、次のように入力して、残りの環境を削除します。

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **方法 2:** PowerShell

  Azure PowerShell 1.0 プレビューを使用する場合、下記のようにリソース グループを削除するコマンドは **Remove-AzureRmResourceGroup** です。Azure PowerShell 1.0 プレビューよりも前のバージョンを使用している場合、コマンドは **Remove-AzureResourceGroup** です。PowerShell プロンプトで、次のように入力します。

	Remove-AzureRmResourceGroup -Name TestApp1-Development

  メッセージが表示されたときに "y" を入力すると、次の内容が返されます。

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  PowerShell プロンプトで、次のように入力して、残りの環境を削除します。

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

使用する方法にかかわらず、コマンドの実行が完了すると、リソース グループとそこに含まれるすべてのリソースが存在しなくなり、リソースの料金請求は発生しなくなります。

アプリケーションの開発中に発生する Azure リソースの使用料金を最小限に抑えるために、[Azure Automation](automation/automation-intro.md) を使用して、次のようなジョブをスケジュールできます。

- 1 日の最後に仮想マシンを停止し、1 日の開始時に再起動する。
- 1 日の最後に環境全体を削除し、1 日の開始時に再作成する。
 
ここまでで、開発環境とテスト環境の作成、保守、および削除をどれほど簡単に実行できるかを体験してきました。これらの操作を詳しく知りたい場合は、上記の手順をさらに試してみたり、この記事に含まれている参照資料に目を通したりしてみてください。

## 次のステップ

- Azure リソースに対して操作のサブセットを実行できる特別なロールを Microsoft Azure AD のグループまたはユーザーに割り当てることで、各環境のさまざまなリソースに[管理制御を委任](role-based-access-control-configure.md)する。
- 各環境のリソース グループや個々のリソースに[タグを割り当てる](resource-group-using-tags.md)。"環境" タグをリソース グループに追加し、その値を環境名に対応するように設定できます。タグは、課金または管理の目的でリソースを整理する必要がある場合に特に役立ちます。
- [Azure プレビュー ポータル](https://portal.azure.com)でリソース グループのリソースのアラートと課金情報を監視する。

## その他のリソース

- Azure SDK 2.6 がインストールされている [Visual Studio での Azure リソース マネージャー テンプレートの作成とデプロイ](http://msdn.microsoft.com/library/azure/Dn872471.aspx)
- [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs)、[Visual Studio のコード](http://www.visualstudio.com/products/code-vs)、または [Web Matrix](http://www.microsoft.com/web/webmatrix/) を使用したアプリケーション作成
- 作成した環境への [Web アプリのデプロイ](app-service-web/web-sites-deploy.md)
- [Visual Studio Release Management](http://msdn.microsoft.com/Library/vs/alm/Release/overview) を使用した、迅速かつ容易で頻度の高いリリースを可能にする、継続的なデプロイメントのマネージ パイプラインの作成
- [Azure Dev/Test Lab](http://azure.microsoft.com/campaigns/devtest-lab/) のプレビューへの招待のリクエスト。このプレビューでは、テンプレートを使用して開発/テスト ラボ環境を管理し、組織内での使用に対するクォータとポリシーを構成できます。

<!---HONumber=Oct15_HO3-->