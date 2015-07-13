<properties
	pageTitle="Azure で複雑なアプリケーションを予測どおりにデプロイする"
	description="JSON リソース グループ テンプレートと PowerShell スクリプトを使用して、Azure の複雑なアプリケーションを単一のユニットとして予測どおりにデプロイする方法について説明します。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/29/2015"
	ms.author="cephalin"/>


# Azure で複雑なアプリケーションを予測どおりにデプロイする #

このチュートリアルでは、JSON リソース グループ テンプレートと PowerShell スクリプトを使用して、Azure で複雑なアプリケーションを 1 つのユニットとして予測どおりにデプロイする方法について説明します。

DevOps では、反復性と予測可能性が、拡張性の高い複雑なアプリケーションのデプロイメントに成功するための鍵になります。[Azure App Service](/services/app-service/) を使用すると、Web アプリ、モバイル、API、ロジック アプリなどの複雑なアプリケーションを作成できます。[Azure リソース マネージャー](../resource-group-overview.md)を使用すると、このようなアプリケーションのすべてのコンポーネントを 1 つのユニットとして管理できます。現時点では、JSON テンプレートと単純な PowerShell スクリプトを使用して、このような複雑なアプリケーションをデプロイすることもできます。

## 学習内容 ##

このチュートリアルでは、次のものを含むアプリケーションをデプロイします。

-	2 つの Web Apps
-	バックエンド SQL データベース
-	アプリ設定、接続文字列、ソース管理
-	Application Insights、アラート、自動スケールの設定

## 使用するツール ##

このチュートリアルでは、次のツールを使用します。ここはツールについて包括的に説明するための場所ではないため、簡単な説明と詳細情報へのリンクを紹介するにとどめて、エンド ツー エンドのシナリオに準拠します。

### Azure リソース マネージャー テンプレート (JSON) ###
 
Azure App Service で Web アプリを作成するたびに、Azure リソース マネージャーでは、JSON テンプレートを使用してリソース グループ全体とコンポーネント リソースが作成されます。[スケーラブルな WordPress](/marketplace/partners/wordpress/scalablewordpress/) アプリなどの [Azure Marketplace](/marketplace) の複雑なテンプレートには、MySQL Database、ストレージ アカウント、App Service プラン、Web アプリ自体、アラート ルール、アプリケーション設定、自動スケールの設定などが含まれます。これらのすべてのテンプレートは、PowerShell を介して利用できます。これらのテンプレートのダウンロード方法と使用方法の詳細については、「[Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」をご覧ください。

Azure リソース マネージャー テンプレートの詳細については、[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)に関するページをご覧ください。

### Azure SDK 2.6 for Visual Studio ###

最新の SDK では、JSON エディターのリソース マネージャー テンプレートをサポートする機能が強化されました。これを使用すると、最初からリソース グループ テンプレートを簡単に作成することや、既存の JSON テンプレート (ダウンロードしたギャラリー テンプレートなど) を開いて変更することができます。また、パラメーター ファイルを設定し、Azure リソース グループのソリューションから直接リソース グループをデプロイすることもできます。

詳細については、[Azure SDK 2.6 for Visual Studio](/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/) に関するページをご覧ください。

### Azure PowerShell 0.8.0 以降 ###

Version 0.8.0 以降の Azure PowerShell のインストールには、Azure モジュール以外に Azure リソース マネージャー モジュールが含まれています。この新しいモジュールを使用して、リソース グループをデプロイするスクリプトを作成できます。

詳細については、「[Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」をご覧ください。

### Azure リソース エクスプローラー ###

この[プレビュー ツール](https://resources.azure.com)を使用すると、サブスクリプションのすべてのリソース グループの JSON 定義と個々のリソースを表示できます。また、リソースの JSON 定義の編集、リソースの階層全体の削除、新しいリソースの作成を実行できます。このツールですぐに使用できる情報には、特定の種類のリソースに設定する必要があるプロパティや適切な値などがあるため、テンプレートの作成に役立ちます。[Azure プレビュー ポータル](https://portal.azure.com)でリソース グループを作成し、エクスプローラー ツールで JSON 定義を調べてリソース グループのテンプレート化に利用できます。

### [Azure へのデプロイ] ボタン ###

GitHub をソース管理に使用する場合、[[Azure へのデプロイ] ボタン](/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/)を README.MD に配置することで、設定なしで Azure にデプロイする UI を作成できます。すべての単純な Web アプリでこれを行うことができます。また、これを拡張して、リポジトリのルートに azuredeploy.json ファイルを配置することにより、リソース グループ全体をデプロイすることもできます。この JSON ファイルにはリソース グループ テンプレートが含まれ、[Azure へのデプロイ] ボタンでリソース グループを作成するために使用されます。その例については、このチュートリアルで使用する [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) サンプルをご覧ください。

## サンプルのリソース グループ テンプレートを取得する ##

それでは早速本題に入りましょう。

1. 	[ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service のサンプルに移動します。

2.	 readme.md の **[Azure へのデプロイ]** をクリックします。
 
3.	[deploy-to-azure](https://deploy.azure.com) サイトが表示され、デプロイメント パラメーターの入力が求められます。ほとんどのフィールドにリポジトリ名が設定されていますが、ランダムな文字列が設定されている場合もあることに注意してください。必要に応じてすべてのフィールドを変更できますが、SQL Server の管理用のログイン名とパスワードのみに入力して、**[次へ]** をクリックします。
 
	![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)

4.	次に、**[デプロイ]** をクリックしてデプロイメント プロセスを開始します。プロセスが完了したら、http://todoapp*XXXX*.azure.websites.net リンクをクリックし、デプロイされたアプリケーションを確認します。

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)

	UI を最初に確認するとき、表示に少し時間がかかります。その理由はアプリが開始されたばかりであるためですが、機能を満載したアプリケーションであるため、ご理解ください。

5.	[デプロイ] ページに戻り、**[管理]** リンクをクリックして、Azure プレビュー ポータルで新しいアプリケーションを確認します。

6.	**[Essentials]** ドロップダウンで、[リソース グループ] リンクをクリックします。Web アプリは **[外部プロジェクト]** の下の GitHub リポジトリに既に接続されていることにも注意してください。

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
 
7.	リソース グループのブレードを見ると、リソース グループ内には 2 つの Web Apps と 1 つの SQL Database が既に存在します。

	![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)
 
このわずか数分間に、複雑なアプリケーションが完全にデプロイされ、コンポーネント、依存関係、設定、データベース、継続的な発行が、Azure リソース マネージャーで自動化されたオーケストレーションによって設定されることを確認しました。これはすべて、次の 2 つの要素で実行しました。

-	[Azure へのデプロイ] ボタン
-	リポジトリのルートの azuredeploy.json

同じアプリケーションを何十、何百、何千回デプロイしても、毎回厳密に同じ構成にできます。この手法が持つ再現性と予測可能性によって、高い拡張性を持つアプリケーションを簡単かつ高い信頼度でデプロイできます。

## AZUREDEPLOY.JSON を確認 (または編集) する ##

次に、GitHub リポジトリをセットアップする方法を見てみましょう。Azure .NET SDK で JSON エディターを使用します。[Azure .NET SDK 2.6](/downloads/) をインストールしていない場合は、今すぐインストールしてください。

1.	お気に入りの git ツールを使用して [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) リポジトリを複製します。次のスクリーン ショットでは、Visual Studio 2013 のチーム エクスプローラーでこれを実行します。

	![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)

2.	リポジトリのルートから、azuredeploy.json を Visual Studio で開きます。[JSON アウトライン] ウィンドウが表示されない場合は、Azure .NET SDK をインストールする必要があります。

	![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

JSON 形式の詳細をすべて説明するわけではありませんが、「[その他のリソース](#resources)」セクションには、リソース グループ テンプレートの言語を学習するためのリンクが用意されています。ここでは、アプリケーションをデプロイするための独自のカスタム テンプレートの作成に役立つ、興味深い機能のみを紹介します。

### パラメーター ###

パラメーター セクションを見てみると、そのパラメーターのほとんどが、**[Azure へのデプロイ]** ボタンで入力を求められるものです。**[Azure へのデプロイ]** ボタンで表示されるサイトの入力 UI は、azuredeploy.json で定義されているパラメーターを使用して設定されます。これらのパラメーターは、リソース名、プロパティの値など、リソースの定義全体で使用されます。

### リソース ###

リソース ノードを見ると、SQL Server のインスタンス、App Service プラン、2 つの Web Apps の、4 つの最上位レベルのリソースが定義されていることがわかります。

#### App Service プラン ####

まず、JSON の単純なルート レベルのリソースから始めましょう。[JSON アウトライン] で、**[hostingPlanName]** という名前の App Service プランをクリックして、対応する JSON コードを強調表示します。

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

`type` 要素は、App Service プラン (これはかなり前にサーバー ファームと呼ばれていました) の文字列を指定し、その他の要素とプロパティは、JSON ファイルで定義されているパラメーターを使用して設定されます。このリソースには、入れ子になったリソースがまったく存在しません。

>[AZURE.NOTE]`apiVersion` の値によって JSON リソース定義と使用する REST API のバージョンが Azure に伝えられ、`{}` 内のリソースの形式に影響を与えることにも注意してください。

#### SQL Server ####

[JSON アウトライン] で **SQLServer** という名前の SQL Server リソースをクリックします。

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)
 
JSON コードの強調部分に関して、次の内容に注意してください。

-	パラメーターの使用により、リソースは相互に一貫性のある方法で必ず作成され、構成されます。
-	SQL Server リソースには、入れ子になった 2 つのリソースがあり、それぞれの `type` の値は異なります。
-	データベースとファイアウォール規則が定義されている、`“resources”: […]` 内の入れ子になったリソースには、ルート レベルの SQL Server リソースのリソース ID を指定する `dependsOn` 要素があります。これは Azure リソース マネージャーに、"このリソースを作成する前に、その他のリソースが既に存在する必要があり、その他のリソースがテンプレートで定義されている場合は、最初に 1 つ作成する" と伝えます。

	>[AZURE.NOTE]`resourceId()` 関数の使用方法の詳細については、「[Azure リソース マネージャーのテンプレートの関数](../resource-group-template-functions.md)」をご覧ください。

-	`dependsOn` 要素の効果は、Azure リソース マネージャーが、どのリソースを並列に作成できるかと、どのリソースを順序どおりに作成する必要があるかを知ることができることです。

#### Web Apps ####

次に、少し複雑な実際の Web Apps に移りましょう。[JSON アウトライン] で [variables('apiSiteName')] Web アプリをクリックして、JSON コードを強調表示します。さらにおもしろくなっていくことがわかります。そのために、機能を 1 つずつ説明します。

##### ルート リソース #####

Web アプリは、2 種類のリソースに依存します。つまり、Azure リソース マネージャーで Web アプリが作成されるのは、App Service プランと SQL Server インスタンスの両方が作成されてからです。

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### アプリケーション設定 #####

アプリケーション設定は、入れ子になったリソースとしても定義されます。

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

`config/appsettings` の `properties` 要素では、`“<name>” : “<value>”` という形式で 2 つのアプリケーション設定を行います。

-	`PROJECT` は、Visual Studio のマルチプロジェクト ソリューションで使用するプロジェクトを Azure のデプロイメントに伝える [KUDU 設定](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)です。ソース管理の構成方法は後で説明しますが、ToDoApp コードは Visual Studio のマルチプロジェクト ソリューションに含まれるため、この設定が必要になります。
-	`clientUrl` は、アプリケーション コードが使用する単なるアプリケーション設定です。

##### Connection strings #####

接続文字列は、入れ子になったリソースとしても定義されます。

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

`config/connectionstrings` の `properties` 要素では、各接続文字列が `“<name>” : {“value”: “…”, “type”: “…”}` という独特の形式で名前と値のペアとしても定義されています。`type` 要素で指定できる値は、`MySql`、`SQLServer`、`SQLAzure`、`Custom` です。

>[AZURE.TIP]接続文字列の種類に関する信頼性のある情報については、Azure PowerShell で次のコマンドを実行します: [Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
    
##### ソース管理 #####

ソース管理の設定は、入れ子になったリソースとしても定義されます。Azure リソース マネージャーは、このリソースを使用して継続的な発行 (後で `IsManualIntegration` の注意点をご確認ください) を構成し、さらに、JSON ファイルの処理中にアプリケーション コードのデプロイメントを自動的に開始します。

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` と `branch` は、直感的にわかるようにして、Git リポジトリと発行元の分岐の名前を指す必要があります。同様に、これらの情報は入力パラメーターで定義されます。

`dependsOn` 要素では、Web アプリのリソース自体以外に、`sourcecontrols/web` も `config/appsettings` と `config/connectionstrings` に依存することに注意してください。これは、`sourcecontrols/web` が構成されると、Azure のデプロイメント プロセスが、アプリケーション コードのデプロイ、ビルド、開始を自動的に試みるためです。そのため、この依存関係を挿入すると、アプリケーション コードの実行前に、必要なアプリケーション設定へのアクセスと接続文字列をアプリケーションで確認できます。[TODO: これが本当であるかどうかを確認する必要があります]

>[AZURE.NOTE]`IsManualIntegration` が `true` に設定されていることにも注意してください。GitHub リポジトリを実際に所有していないため、このチュートリアルではこのプロパティが必要です。そのため、[ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (リポジトリの自動更新を Azure にプッシュします) から継続的な発行を構成するために Azure へのアクセス許可を実際に与えることはできません。[Azure プレビュー ポータル](https://portal.azure.com)で事前に所有者の GitHub の資格情報を構成している場合のみ、指定されたリポジトリの既定値 `false` を使用できます。つまり、ユーザーの資格情報を使用して [Azure プレビュー ポータル](https://portal.azure.com) の任意のアプリでソース管理に GitHub や BitBucket を事前に設定した場合、Azure に資格情報が保存されるため、後で GitHub や BitBucket からアプリをデプロイするときにその情報を使用できます。ただし、まだ設定していない場合は、Azure リソース マネージャーが Web アプリのソース管理の設定を構成しようとしたときに、JSON テンプレートのデプロイメントに失敗します。その原因は、リポジトリの所有者の資格情報で GitHub や BitBucket にログインできないからです。

## JSON テンプレートとデプロイ済みのリソース グループを比較する ##

ここでは、[プレビュー ポータル](https://portal.azure.com)内の Web アプリのすべてのブレードを確認できます。別のツールでも詳細をある程度まで確認できます。[Azure リソース エクスプローラー](https://resources.azure.com) プレビュー ツールに移動します。Azure のバックエンドに実際に存在する、サブスクリプション内のすべてのリソース グループの JSON 表現を把握できます。Azure のリソース グループの JSON 階層が、リソース グループを作成するために使用されるテンプレート ファイル内の階層にどのように対応しているかもわかります。

たとえば、[Azure リソース エクスプローラー](https://resources.azure.com) ツールにアクセスしてエクスプローラーのノードを展開すると、リソース グループと対応するリソースの型で収集されたルート レベルのリソースを確認できます。

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Web アプリをドリルダウンすると、下のスクリーン ショットのように Web アプリの構成の詳細を確認できます。

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

ここでも、入れ子になったリソースは、JSON テンプレート ファイル内によく似た階層を持つ必要があり、アプリケーション設定、接続文字列などが JSON ウィンドウに適切に反映されている必要があります。ここに設定されていない場合は、JSON ファイルに問題がある可能性があり、JSON テンプレート ファイルのトラブルシューティングが役立つことができます。

## リソース グループ テンプレートを自分でデプロイする ##

**[Azure へのデプロイ]** ボタンは優れていますが、azuredeploy.json を GitHub に既にプッシュしている場合にのみ、azuredeploy.json でリソース グループ テンプレートをデプロイできます。Azure .NET SDK には、ローカル コンピューターから JSON テンプレート ファイルを直接デプロイするためのツールも用意されています。そのためには、次の手順に従います。

1.	Visual Studio で、**[ファイル]**、**[新規作成]**、**[プロジェクト]** の順にクリックします。

2.	**[Visual C#]**、**[クラウド]**、**[Azure リソース グループ]** の順にクリックし、**[OK]** をクリックします。

	![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)

3.	**[Azure テンプレートの選択]** で、**[空白のテンプレート]** を選択し、**[OK]** をクリックします。

4.	azuredeploy.json を新しいプロジェクトの **Template** フォルダーにドラッグします。

	![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)

5.	ソリューション エクスプローラーで、コピーした azuredeploy.json を開きます。

6.	デモで使用する目的のみで、**[リソースの追加]** をクリックして、標準の Application Insight リソースをいくつか JSON ファイルに追加してみましょう。JSON ファイルのデプロイのみに興味がある場合は、デプロイの手順に進みます。

	![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)

7.	**[Web Apps 用の Application Insights]** を選択し、既存の App Service プランと Web アプリが選択されていることを確認して、**[追加]** をクリックします。

	![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)

	リソースとその機能に応じて、App Service プランや Web アプリと依存関係を持つ新しいリソースがいくつか表示されます。これらのリソースは既存の定義では有効でないため、変更します。

	![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
 
8.	[JSON アウトライン] で、**[appInsights AutoScale]** をクリックして、JSON コードを強調表示します。これは App Service プランのスケーリング設定です。

9.	強調表示されている JSON コードで、`location` と `enabled` プロパティを検索し、次のように設定します。

	![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)

10.	[JSON アウトライン] で、**[CPUHigh appInsights]** をクリックして、JSON コードを強調表示します。これはアラートです。

11.	`location` と `isEnabled` プロパティを検索し、次のように設定します。他の 3 つのアラート (紫色の電球) も同様に処理します。

	![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)

12.	これで、デプロイする準備が整いました。次に、プロジェクトを右クリックして **[デプロイ]**、**[新しいデプロイ]** の順に選択します。

	![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)

13.	ログインしていない場合は、Azure アカウントにログインします。

14.	サブスクリプション内の既存のリソース グループを選択するか、新しいリソース グループを 1 つ作成して、**[azuredeploy.json]** を選択し、**[パラメーターの編集]** をクリックします。

	![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)

	これで、便利なテーブルで、テンプレート ファイルで定義されているすべてのパラメーターを編集できるようになりました。既定値を定義するパラメーターには既定値が既に設定されています。許可される値の一覧を定義するパラメーターはドロップダウン リストとして表示されます。

	![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)

15.	空のパラメーターすべてに入力します。**[repoUrl]** には [GitHub リポジトリの ToDoApp のアドレス](https://github.com/azure-appservice-samples/ToDoApp.git)を使用します。その後、**[保存]** をクリックします。
 
	![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)

	>[AZURE.NOTE]自動スケールは、**Standard** 層以上で提供される機能です。プラン レベルのアラートは、**Basic** 層以上で提供される機能です。新しい App Insights のすべてのリソースが点灯するのを確認するために、**sku** パラメーターを **Standard** や **Premium** に設定する必要があります。
	
16.	**[デプロイ]** をクリックします。**[パスワードの保存]** を選択した場合、パスワードはパラメーター ファイルに**プレーン テキスト**で保存されます。それ以外の場合は、デプロイメント プロセス中にデータベースのパスワードを入力するように求められます。

これで終了です。 後は、[プレビュー ポータル](https://portal.azure.com)と[Azure リソース エクスプローラー](https://resources.azure.com) ツールに移動して、JSON でデプロイされたアプリケーションに追加された新しいアラートと自動スケールの設定を確認するのみです。

このセクションの手順では主に次の内容を実行しました。

1.	テンプレート ファイルを準備する
2.	テンプレート ファイルと使用するパラメーター ファイルを作成する
3.	パラメーター ファイルを使用してテンプレート ファイルをデプロイする

最後の手順は、PowerShell コマンドレットで簡単に実行されます。アプリケーションをデプロイするときに Visual Studio で実行された内容を確認するには、Scripts\Deploy-AzureResourceGroup.ps1. を開きます。大量のコードが存在しますが、パラメーター ファイルを使用してテンプレート ファイルをデプロイする必要がある関連するすべてのコードのみを強調表示しています。

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

最後のコマンドレット `New-AzureResourceGroup` で、アクションが実際に実行されます。これまでの内容から、ツールを利用すれば、クラウド アプリケーションを予測どおりにデプロイするのは比較的簡単だということがわかります。コマンドレットを同じテンプレートと同じパラメーター ファイルで実行するたびに、同じ結果が得られます。

## 概要 ##

DevOps では、反復性と予測可能性が、拡張性の高い複雑なアプリケーションのデプロイメントに成功するための鍵になります。このチュートリアルでは、Azure リソース マネージャーのテンプレートを使用して、アプリケーションを 1 つのリソース グループとして Azure にデプロイしました。Azure の複雑なアプリケーションをテンプレートに変換するために必要な知識を提供して、Azure に予測どおりにデプロイするお役に立つことを願っています。

<a name="resources"></a>
## その他のリソース ##

-	[Azure リソース マネージャー テンプレートの言語](http://msdn.microsoft.com/library/azure/dn835138.aspx)
-	[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)
-	[Azure リソース マネージャーのテンプレートの関数](../resource-group-template-functions.md)
-	[高度なテンプレートの操作](../resource-group-advanced-template.md)
-	[Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../resource-group-template-deploy.md)
-	[Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)
-	[Azure でのリソース グループのデプロイのトラブルシューティング](../resource-group-deploy-debug.md)




 

<!---HONumber=62-->