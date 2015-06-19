<properties 
	pageTitle="Azure リソース マネージャーのテンプレートを使用した DocumentDB と Azure の Web サイトのデプロイ | Azure" 
	description="Azure リソース マネージャーのテンプレートを使用して DocumentDB アカウント、Azure の Web サイト、サンプルの Web アプリケーションをデプロイする方法を説明します。" 
	services="documentdb, websites" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="stbaro"/>

# Azure リソース マネージャーのテンプレートを使用した DocumentDB と Azure の Web サイトのデプロイ #

このチュートリアルでは、Azure リソース マネージャーのテンプレートを使用して [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)、[Azure の Web サイト](http://azure.microsoft.com/services/websites/)、サンプルの Web アプリケーションをデプロイおよび統合する方法を示します。

このチュートリアルを完了すると、次の項目について説明できるようになります。  

-	Azure リソース マネージャーのテンプレートを使用して、DocumentDB アカウントと Azure の Web サイトをデプロイおよび統合する方法
-	Azure リソース マネージャーのテンプレートを使用して、DocumentDB アカウント、Azure の Web サイト、Web デプロイ アプリケーションをデプロイおよび統合する方法

##<a id="Prerequisites"></a>前提条件 ##
> [AZURE.TIP] このチュートリアルは、Azure リソース マネージャーのテンプレート、JSON、Azure PowerShell を使用した経験があるユーザーを対象にしているわけではありませんが、参照するテンプレートやデプロイメント オプションに変更を加える場合は、これらの領域に関する知識が必要になります。

このチュートリアルの手順を実行する前に、次のものを備えておく必要があります。

- Azure サブスクリプション。Azure はサブスクリプション方式のプラットフォームです。サブスクリプションの入手方法の詳細については、[購入オプション](http://azure.microsoft.com/pricing/purchase-options/)に関するページ、「[メンバー プラン](http://azure.microsoft.com/pricing/member-offers/)」、または[無料評価版](http://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- Azure Storage アカウント。詳細については、「[Azure ストレージ アカウントについて](../storage-whatis-account.md)」を参照してください
- Azure PowerShell を実行できるワークステーション。手順については、[Azure PowerShell のインストールおよび構成](../install-configure-powershell.md)に関するページを参照してください。

##<a id="CreateDB"></a>手順 1:サンプル ファイルのダウンロードと抽出 ##
このチュートリアルで使用するサンプル ファイルをダウンロードすることから始めましょう。

1. [DocumentDB アカウントと Web サイトの作成およびデモ アプリケーションのデプロイのサンプル](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)を、ローカルのフォルダー (C:\DocumentDBTemplates など) にダウンロードし、ファイルを展開します。このサンプルでは、DocumentDB アカウント、Azure の Web サイト、Web アプリケーションをデプロイします。また、Web アプリケーションをこの DocumentDB アカウントに接続するように自動的に構成します。

2. [DocumentDB アカウントと Web サイトの作成のサンプル](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)をローカルのフォルダー (C:\DocumentDBTemplates など) にダウンロードし、ファイルを展開します。このサンプルでは、DocumentDB アカウントと Azure の Web サイトをデプロイし、Web サイトの構成を変更して DocumentDB の接続情報を容易に表示できるようにしますが、Web アプリケーションは含んでいません。  

> [AZURE.TIP] コンピューターのセキュリティ設定によっては、展開したファイルを右クリックし、**[プロパティ]**、**[ブロックの解除]** の順にクリックして、ファイルのブロックを解除することが必要になる場合があります。

![Screenshot of the Properties window with the Unblock button highlighted](./media/documentdb-create-documentdb-website/image1.png)


##<a id="Build"></a>手順 2:Document アカウント、Web サイト、デモ アプリケーションのデプロイのサンプル ##

では、最初のテンプレートをデプロイしましょう。

> [AZURE.TIP] このテンプレートでは、ここで入力する Web サイト名と DocumentDB アカウント名が a) 有効であること、および b) 使用可能であることについては確認されていません。PowerShell デプロイメント スクリプトの実行前に、指定する予定の名前の可用性を確認しておくことを強くお勧めします。

1. Microsoft Azure PowerShell を開き、[DocumentDB アカウントと Web サイトの作成およびデモ アプリケーションのデプロイのサンプル](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)をダウンロードして展開したフォルダー (C:\DocumentDBTemplates\CreateDocDBWebsiteTodo など) に移動します。


2. CreateDocDBWebsiteTodo.ps1 という PowerShell スクリプトを実行します。このスクリプトでは、次のような必須のパラメーターを使用します。
	- WebsiteName:Web サイトの名前を指定します。これは、Web サイトへのアクセスに使用する URL を作成するときに使用されます (たとえば、"mydemodocdbwebsite" を指定した場合、Web サイトへのアクセスに使用する URL は mydemodocdbwebsite.azurewebsites.net になります)。

	- ResourceGroupName:デプロイする Azure リソース グループの名前を指定します。指定したリソース グループが存在しない場合は作成されます。

	- docDBAccountName:作成する DocumentDB アカウントの名前を指定します。

	- location:DocumentDB と Web サイト リソースを作成する Azure の場所を指定します。有効な値は East Asia、Southeast Asia、East US、West US、North Europe、West Europe です (指定した値は大文字と小文字が区別されます)。


3. このスクリプトを実行するコマンドの例を次に示します。

    	PS C:\DocumentDBTemplates\CreateDocDBWebsiteTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebsite" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] このスクリプトを実行する過程で、Azure アカウントのユーザー名とパスワードの入力を求められます。デプロイメントをすべて完了するには 10 ～ 15 分かかります。  	

4. 結果の出力の例を次に示します。 

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebsite-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebsite' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebsite/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebsite/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebsite, ForbiddenRequests mydemodocdbwebsite, LongHttpQueue mydemodocdbwebsite...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebsite              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebsite    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebsite        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebsite         microsoft.insights/alertrules          eastus
                    mydemodocdbwebsite-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebsite                      microsoft.insights/components          centralus
                    mydemodocdbwebsite                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebsite                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. サンプル アプリケーションを見る前に、このテンプレートのデプロイメントによって得られた結果を把握しておきましょう。

	- Azure の Web サイトが作成されました。

	- Azure DocumentDB アカウントが作成されました。

	- Web デプロイ パッケージが Azure の Web サイトにデプロイされました。

	- Azure の Web サイトの構成が変更され、Azure DocumentDB エンドポイントとプライマリ マスター キーがアプリケーションの設定として表示されるようになりました。

	- 一連の既定の監視ルールが作成されました。

	
6. アプリケーションを使用するには、単純にその Web サイトの URL にアクセスします (上の例では URL は http://mydemodocdbwebsite.azurewebsites.net)。次のような Web アプリケーションが表示されます。

	![Sample Todo application](./media/documentdb-create-documentdb-website/image2.png)

7. 操作を進めていくつかのタスクを作成し、[Azure プレビュー ポータル](https://portal.azure.com)を開きます。

8. リソース グループを参照するように選択し、デプロイメント時に作成したリソース グループを選択します (上のサンプルでは、myDemoResourceGroup)。

	![Screenshot of the Azure Preview portal with the myDemoResourceGroup highlighted](./media/documentdb-create-documentdb-website/image3.png)
9.  [概要] レンズのリソース マップに関連リソース (DocumentDB アカウント、Web サイト、監視) がすべて表示されることを確認します。

	![Screenshot of the Summary lens](./media/documentdb-create-documentdb-website/image4.png)
10.  DocumentDB アカウントをクリックし、(アカウント ブレードの下部にある) クエリ エクスプローラーを起動します。

	![Screenshot of the Resource Group and Account blades with the Query Explorer tile highlighted](./media/documentdb-create-documentdb-website/image8.png)

11. 既定のクエリ "SELECT * FROM c" を実行し、その結果を調べます。このクエリでは、前の手順 7. で作成した Todo 項目の JSON 表現を取得しました。クエリを自由に試してください。たとえば、SELECT * FROM c WHERE c.isComplete = true を実行してみると、完了済みとしてマークされているすべての Todo 項目が返されます。


	![Screenshot of the Query Explorer and Results blades showing the query results](./media/documentdb-create-documentdb-website/image5.png)
12. DocumentDB ポータルの操作やサンプル Todo アプリケーションの変更を自由に試してみてください。準備ができたら、別のテンプレートをデプロイしましょう。
	 
##<a id="Build"></a>手順 3:Document アカウントと Web サイトの作成のサンプル ##

では、2 番目のテンプレートをデプロイしましょう。

> [AZURE.TIP] このテンプレートでは、ここで入力する Web サイト名と DocumentDB アカウント名が a) 有効であること、および b) 使用可能であることについては確認されていません。PowerShell デプロイメント スクリプトの実行前に、指定する予定の名前の可用性を確認しておくことを強くお勧めします。

1. Microsoft Azure PowerShell を開き、[DocumentDB アカウントと Web サイトの作成のサンプル](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)をダウンロードして展開したフォルダー (C:\DocumentDBTemplates\CreateDocDBWebsite など) に移動します。


2. CreateDocDBWebsite.ps1 という PowerShell スクリプトを実行します。このスクリプトでは、デプロイした最初のテンプレートと同じパラメーターを使用します。次のようになります。
	- WebsiteName:Web サイトの名前を指定するパラメーターであり、Web サイトへのアクセスに使用する URL を作成するために使用します (たとえば、"myotherdocumentdbwebsite" を指定した場合、Web サイトへのアクセスに使用する URL は myotherdocumentdbwebsite.azurewebsites.net になります)。

	- ResourceGroupName:デプロイする Azure リソース グループの名前を指定します。指定したリソース グループが存在しない場合は作成されます。

	- docDBAccountName:作成する DocumentDB アカウントの名前を指定します。

	- 	location:DocumentDB と Web サイト リソースを作成する Azure の場所を指定します。有効な値は East Asia、Southeast Asia、East US、West US、North Europe、West Europe です (指定した値は大文字と小文字が区別されます)。

3. このスクリプトを実行するコマンドの例を次に示します。

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebsite" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] このスクリプトを実行する過程で、Azure アカウントのユーザー名とパスワードの入力を求められます。デプロイメントをすべて完了するには 10 ～ 15 分かかります。  	

4. デプロイメントの出力は、最初のテンプレートの例とよく似ています。 
5. Azure ポータルを開く前に、このテンプレートのデプロイメントによって得られた結果を把握しておきましょう。

	- Azure の Web サイトが作成されました。

	- Azure DocumentDB アカウントが作成されました。

	- 	Azure の Web サイトの構成が変更され、Azure DocumentDB エンドポイント、プライマリ マスター キー、セカンダリ マスター キーがアプリケーションの設定として表示されるようになりました。

	- 	一連の既定の監視ルールが作成されました。

6. [Azure プレビュー ポータル](https://portal.azure.com)を開き、リソース グループを参照し、デプロイ時に作成したリソース グループを選択します (上のサンプルでは、myOtherDemoResourceGroup)。
7. [概要] レンズで、デプロイしたばかりの Azure の Web サイトを選択します。

	![Screenshot of the Summary lens with the myotherdocumentdwebsite website highlighted](./media/documentdb-create-documentdb-website/image6.png)
8. Web サイト ブレードで、**[すべての設定]** を選択し、**[アプリケーションの設定]** を選択して、DocumentDB エンドポイントおよびそれぞれの DocumentDB マスター キーに関するアプリケーションの設定がどのように表示されているかを確認します。

	![Screenshot of the Website, Settings, and Site settings blades](./media/documentdb-create-documentdb-website/image7.png)
9. 引き続き、Azure ポータルの操作を確認したり、DocumentDB の[サンプル](http://go.microsoft.com/fwlink/?LinkID=402386)に従って独自の DocumentDB アプリケーションを作成したり、自由に試してみてください。

	
	

##<a name="NextSteps"></a>次のステップ

ご利用ありがとうございます。Azure リソース マネージャーのテンプレートを使用して、DocumentDB、Azure の Web サイト、サンプルの Web アプリケーションをデプロイしました。

- DocumentDB の詳細については、[ここ](http://azure.com/docdb)をクリックしてください。
- Azure Websites の詳細については、[ここ](http://go.microsoft.com/fwlink/?LinkId=325362)をクリックしてください。
- Azure リソース マネージャーのテンプレートの詳細については、[ここ](https://msdn.microsoft.com/library/azure/dn790549.aspx)をクリックしてください。

<!--HONumber=49--> 