<properties
	pageTitle="Azure App Service で API Apps と ASP.NET を使用する | Microsoft Azure"
	description="Visual Studio 2015 を使用して、Azure App Service で ASP.NET API アプリを作成、デプロイし、使用する方法について説明します。"
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# Azure App Service で API Apps と ASP.NET を使用する

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概要

このチュートリアルでは、[App Service API アプリ](app-service-api-apps-why-best-platform.md)を作成し、その API アプリに ASP.NET Web API をデプロイし、ASP.NET MVC クライアントから API アプリを使用します。このチュートリアルは ASP.NET に関する知識を前提としていますが、Microsoft Azure の使用経験は問いません。このチュートリアルでは、クラウドで動作する Web API とクライアント アプリケーションを作成します。

サンプル アプリケーションは簡単な連絡先一覧です。次の図は、MVC アプリが API から受信したデータを表示する方法を示しています。

![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

## 学習内容

このチュートリアルでは、次のことについて説明します。

* Azure SDK for .NET をインストールして、Azure 向け開発用にコンピューターを準備する方法。
* Visual Studio 2015 に組み込まれたツールを利用し、Azure App Service で API アプリと Web アプリを使用する方法。
* Swashbuckle NuGet パッケージを利用して API 検出を自動化し、Swagger API 定義 JSON を動的に生成する方法。
* 自動的に生成されたクライアント コードを使用し、.NET クライアントから API アプリを使用する方法。
* API ポータルを使用し、API アプリ メタデーのエンドポイントを構成する方法。

これは一連のチュートリアルの最初のチュートリアルです。後続のチュートリアルは、このチュートリアルで作成した内容に基づきます。後続のチュートリアルでは次の内容を学習します。
 
* クライアントのドメインが API のドメインと異なるとき、CORS を利用して JavaScript クライアントから API を呼び出す方法。
* Azure Active Directory を使用し、認証されていないアクセスから API を保護する方法。
* Azure Active Directory にログインしたユーザーが保護されている API を使用する方法。
* サービス プリンシパルを使用し、保護されている API を使用する方法。

## 前提条件

このチュートリアルは ASP.NET Web API に関する知識を前提としています。概要については、「[ASP.NET Web API 2 の使用](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)」を参照してください。

指示とスクリーン ショットは Visual Studio 2015 の使用を前提としていますが、Visual Studio 2013 の場合でも操作は同じです。

このチュートリアルを完了するには、Azure アカウントが必要です。そのための方法は次のとおりです。

* [無料で Azure アカウントを開きます](/pricing/free-trial/?WT.mc_id=A261C142F)。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Azure App Service の Web Apps 機能など、無料の Azure サービスと機能を利用できます。
* [Visual Studio サブスクライバーの特典を有効にします](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。

Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Azure App Service アプリケーションの作成](http://go.microsoft.com/fwlink/?LinkId=523751)」にアクセスしてください。有効期間が短いスターター アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

このチュートリアルでは、Azure SDK for .NET 2.8.1 以降のバージョンが必要です。

## サンプル アプリケーションの概要

このチュートリアルで API アプリと Web アプリにデプロイするコードは [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) GitHub リポジトリにあります。ContactsList Visual Studio ソリューションには次のプロジェクトが含まれています。

* **ContactsList.API** - 名前と電子メール アドレスの一覧を返す ASP.NET Web API プロジェクト。Get メソッドの最初の呼び出しでハードコードされた連絡先が 3 つ返されます。後続の Put、Post、Delete メソッドの呼び出しで変更がローカルの JSON ファイルに保存されます。
* **ContactsList.MVC** - ContactsList API の ASP.NET MVC クライアント。
* **ContactsList.Angular** - ContactsList API の単純な AngularJS UI クライアント。保護されていない (認証のない) API アプリを呼び出す方法を示します。
* **ContactsList.Angular.AAD** - Azure Active Directory を使用してユーザーを認証する方法を示す AngularJS クライアント。
* **CompanyContacts.API** - Get 要求に対する応答として連絡先のハードコードされた一覧を返す ASP.NET Web API プロジェクト。**ContactsList.API** Get メソッドにより呼び出され、service-to-service (サービス プリンシパル) 認証で API を呼び出す方法を示します。
 
## サンプル アプリケーションのダウンロード 

1. [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) リポジトリをダウンロードします。

	ローカル コンピューターに [.zip ファイルをダウンロードする](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list/archive/master.zip)か、リポジトリのクローンを作成できます。

2. Visual Studio 2015 または 2013 で ContactsList ソリューションを開きます。

2. ソリューションをビルドし、NuGet パッケージを復元します。

## ローカル実行時、Swashbuckle と Swagger を使用する

[Swagger](http://swagger.io/) は API のメソッドと応答を表すための JSON 形式です。Swagger 2.0 のサポートは Azure App Service に組み込まれています。このチュートリアルの後半で紹介しますが、API に Swagger メタデータを入力すると、Visual Studio は API の使用を簡単にするクライアント コードを生成できます。

ASP.NET Web API プロジェクトに Swagger 2.0 メタデータを入力するために、[Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet パッケージをインストールできます。Swashbuckle はリフレクションを使用し、メタデータを動的に生成します。NuGet パッケージはダウンロードした ContactsList.API プロジェクトに既にインストールされています。また、**Azure API App** プロジェクト テンプレートを利用して新しいプロジェクトを作成するとき、既にインストールされています。

チュートリアルのこのセクションでは、生成した Swagger 2.0 メタデータを確認し、Swagger メタデータに基づいてテスト UI を試します。

2. ContactsList.API プロジェクトをスタートアップ プロジェクトとして設定します。(**ソリューション エクスプローラー**のプロジェクトを右クリックし、コンテキスト メニューで **[スタートアップ プロジェクトに設定]** をクリックします。)
 
4. F5 キーを押してデバッグ モードでプロジェクトを実行します。

	ブラウザーが開き、403 Forbidden ページが表示されます。

	![](./media/app-service-api-dotnet-get-started/403.png)

12. ブラウザーのアドレス バーで、行の末尾に `swagger/docs/v1` を追加し、Return を押します。(URL は `http://localhost:51864/swagger/docs/v1` です。)

	これは API の Swagger 2.0 JSON メタデータを返すために Swashbuckle で使用される既定の URL です。Internet Explorer を使用している場合、v1.json ファイルをダウンロードするように求められます。

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Chrome を使用している場合、ブラウザーのウィンドウで JSON が表示されます。

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	次の例は、API の Swagger メタデータの最初のセクションと Get メソッドの定義です。このメタデータに基づき、次の手順とチュートリアルの後半で使用する Swagger UI はクライアント コードを自動生成します。

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ContactsList.API"
		  },
		  "host": "localhost:51864",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/Contacts": {
		      "get": {
		        "tags": [ "Contacts" ],
		        "operationId": "Contacts_Get",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/Contact" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. ブラウザーを閉じます。

3. **ソリューション エクスプローラー**の ContactsList.API プロジェクトで、*App\_Start\\SwaggerConfig.cs* ファイルを開き、次のコードまで下にスクロールし、そのコメントを解除します。

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	SwaggerConfig.cs ファイルは、プロジェクトに Swashbuckle パッケージをインストールすると作成されます。このファイルでは、さまざまな方法で Swashbuckle を構成できます。

	コメントを解除したコードにより、以降の手順で使用する Swagger UI が有効になります。API アプリ プロジェクト テンプレートで Web API プロジェクトを作成すると、セキュリティ対策として既定でこのコードのコメントが解除されます。

5. プロジェクトを再度実行します。

3. ブラウザーのアドレス バーで、行の末尾に `swagger` を追加し、Return を押します。(URL は `http://localhost:51864/swagger` です。)

4. Swagger UI ページが表示されたら、**[Contacts]** をクリックし、利用できるメソッドを確認します。

	![](./media/app-service-api-dotnet-get-started/contactsmethods.png)

5. **[Get] > [実際に使ってみる]** をクリックします。

	Swagger UI が ContactsList Get メソッドを呼び出し、JSON 結果を表示します。

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. **[Post]** をクリックし、**[モデル スキーマ]** の下にあるボックスをクリックします。

	モデル スキーマをクリックすると、入力ボックスに事前入力されます。このボックスには、Post メソッドのパラメーター値を指定できます。

	![](./media/app-service-api-dotnet-get-started/post.png)

7. 次の例のように `contact` パラメーター入力ボックスで JSON を変更するか、自分の名前と電子メール アドレスを代わりに指定します。

		{
		  "CreatedBy": "",
		  "EmailAddress": "carson@contoso.com",
		  "Id": 4,
		  "Name": "Alexander Carson"
		} 

10. **[試してみる]** をクリックします。

	ContactsList API は HTTP 200 と応答本文を返し、追加内容を確認します。

11. **[Get] > [実際に使ってみる]** をクリックします。

	Get メソッドの応答に新しい連絡先が含まれます。

12. Put、Delete、Get by ID メソッドも試し、ブラウザーを閉じます。

Swashbuckle はあらゆる ASP.NET Web API プロジェクトで利用できます。Swagger メタデータ生成を既存のプロジェクトに追加する場合、Swashbuckle パッケージをインストールします。App Service API アプリとしてデプロイするプロジェクトを新規作成する場合、ASP.NET **Azure API App** プロジェクト テンプレートを使用します。そのテンプレートにより、Swashbuckle がインストールされた Web API プロジェクトが作成されます。

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

## Azure で API アプリを作成し、それに ContactsList.API プロジェクトをデプロイする

このセクションでは、Visual Studio の **Web の発行**ウィザードに統合されている Azure ツールを使用し、Azure で新しい API アプリを作成します。次に、ContactsList.API プロジェクトを新しい API アプリにデプロイし、もう一度、今度はクラウドで実行中に Swagger UI を実行して API を呼び出します。

1. **ソリューション エクスプローラー**で ContactsList.API プロジェクトを右クリックし、**[発行]** をクリックします。

3.  **Web の発行**ウィザードの **[プロファイル]** ステップで、**[Microsoft Azure App Service]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. まだ行っていない場合は Azure アカウントにサインインし、有効期限が切れている場合は資格情報を更新します。

4. [App Service] ダイアログ ボックスで、使用する Azure **サブスクリプション**を選択して、**[新規]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. **[App Service の作成]** ダイアログ ボックスの **[ホスティング]** タブで、**[種類の変更]**、**[API App]** を順にクリックします。

4. *azurewebsites.net* ドメインに固有の **API App 名** を入力します。

	Visual Studio により、プロジェクト名に date-time 文字列が追加された一意の名前が提案されます。その名前で問題なければ、それを利用して構いません。

	入力した名前が既に使用されている場合は、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別の名前を入力する必要があります。

	Azure では、この名前がアプリケーションの URL のプレフィックスとして使用されます。URL 全体は、この名前に *.azurewebsites.net* を追加して構成されます。たとえば、名前が `ContactsListAPI` である場合、URL は `contactslistapi.azurewebsites.net` です。

6. **[リソース グループ]** ドロップダウンに「ContactsListGroup」と入力するか、別の名前を入力します。

	このボックスでは既存の[リソース グループ](../azure-preview-portal-using-resource-groups.md)を選択するか、サブスクリプションの既存のリソース グループとは異なる名前を入力し、新しいリソース グループを作成できます。

	このチュートリアルでは、新しいリソース グループを作成すると便利です。チュートリアルのために作成したすべての Azure リソースを 1 回の手順で簡単に削除できるからです。

4. **[App Service プラン]** ドロップダウンの隣にある **[新規]** ボタンをクリックします。

	![](./media/app-service-api-dotnet-get-started/createas.png)

	App Service プランの詳細については、[App Service プランの概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)に関するページを参照してください。

5. **[App Service プランの構成]** ダイアログに「ContactsListPlan」と入力するか、別の名前を入力します。

5. **[場所]** ドロップダウン リストで、現在の所在地に最も近い場所を選択します。

	この設定によって、アプリが実行される Azure データ センターが指定されます。このチュートリアルでは、任意のリージョンを選択することができます。任意のリージョンを選択しても、大きな違いはありません。ただし、運用アプリでは、[待機時間](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)を最小限に抑えるために、アクセスするクライアントに最も近いサーバーを選択することをお勧めします。

5. **[サイズ]** ドロップダウンで、**[Free]** をクリックします。

	無料の価格レベルでこのチュートリアルに十分な性能が提供されます。

6. **[App Service プランの構成]** ダイアログで、**[OK]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. **[App Service の作成]** ダイアログ ボックスで、**[作成]** をクリックします。

	Visual Studio によって API アプリが作成され、新しい API アプリに必要なすべての設定が含まれる発行プロファイルが作成されます。次の手順では、新しい発行プロファイルを使用し、プロジェクトをデプロイします。
 
	注: Azure App Service で API アプリを作成する方法は他にもあります。Visual Studio では、新しいプロジェクトを作成するとき、同じダイアログが利用できます。Azure ポータル、[Windows PowerShell 用の Azure コマンドレット](../powershell-install-configure.md)、[クロスプラットフォーム コマンドライン インターフェイス](../xplat-cli.md)を利用し、API アプリを作成することもできます。

8. **Web の発行**ウィザードの **[発行]** タブで、**[次へ]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio によって、ContactsList.API プロジェクトに新しい API アプリがデプロイされ、ブラウザーで API アプリの URL が開きます。ブラウザーに "正常に作成されました" ページが表示されます。

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. ブラウザーのアドレス バーの URL に「swagger」を追加し、Enter キーを押します。(URL は `http://{apiappname}.azurewebsites.net/swagger` です。)

	ブラウザーに前に見た Swagger UI が表示されますが、今度はクラウドで実行しています。Get メソッドを試してみます。既定の 3 件の連絡先に戻ります。先に行った変更がローカル ファイルに保存されたためです。これから行う変更は Azure API アプリのファイル システムに保存されます。

12. [Azure ポータル](https://portal.azure.com/) を開きます。
 
14. **[参照] > [API Apps] > {新しい API アプリ}** の順にクリックします。

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. **[設定]** をクリックします。次に、**[設定]** ブレードで API セクションを見つけ、**[API 定義]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	API 定義ブレードでは、JSON 形式で Swagger 2.0 メタデータを返す URL を指定できます。Visual Studio により API アプリが作成されるとき、先に見た既定値 (API アプリの基礎 URL + `/swagger/docs/v1`) に API 定義 URL が設定されます。

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	コードを生成する API アプリを選択するとき、Visual Studio はこの URL からメタデータを取得します。

## <a id="codegen"></a> 生成されたクライアント コードを使用し、.NET から使用します。 

Azure API Apps と Swagger の統合の利点の 1 つは、自動コード生成です。生成されたクライアント クラスにより、API アプリを呼び出すコードの記述が容易になります。

このセクションでは、ASP.NET MVC Web アプリから API アプリを使用する方法について確認します。先にローカルで MVC クライアントと Web API を実行し、次に Azure App Service で新しい Web アプリにクライアントをデプロイし、クラウドで実行します。

### クライアント コードの生成

Visual Studio を使用するか、またはコマンドラインから、API アプリのクライアント コードを生成できます。このチュートリアルでは、Visual Studio を使用します。コマンドラインから実行する方法については、GitHub.com の [Azure/autorest](https://github.com/azure/autorest) リポジトリにある readme ファイルを参照してください。

ContactsList.MVC プロジェクトによりクライアント コードが既に生成されていますが、それを削除して再生成し、そのしくみを観察し、独自の API アプリの URL を既定のターゲット URL にできます。

1. Visual Studio の**ソリューション エクスプローラー**の ContactsList.MVC プロジェクトで、*ContactsList.API* フォルダーを削除します。

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. ContactsList.MVC プロジェクトを右クリックし、**[追加]、[REST API クライアント]** を順にクリックします。

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. **[REST API クライアントの追加]** ダイアログ ボックスで、**[Microsoft Azure API アプリからダウンロード]**、**[参照]** を順にクリックします。

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. **[App Service]** ダイアログ ボックスで、**[ContactsListGroup]** リソース グループを展開し、API アプリを選択し、**[OK]** をクリックします。

	このダイアログ ボックスでは、API アプリが多すぎてスクロールできない場合、いくつかの方法で一覧の API アプリを整理できます。検索文字列を入力し、名前で API アプリを絞り込むこともできます。

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	**[REST API クライアントの追加]** に戻ると、ポータルで先に見た API 定義 URL 値がテキスト ボックスに入力されていることに注意してください。

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	代替として、参照ダイアログを利用する代わりに URL を直接入力できます。たとえば、API を Web アプリにデプロイしたとき、それが参照ダイアログに表示されない場合、Swagger メタデータを返す URL をここに直接入力できます。

	**[既存の Swagger メタデータを選択する]** オプションにも注目してください。Azure にデプロイする前にコードを生成する場合、ローカルで実行し、Swagger JSON ファイルをダウンロードし、それをここで選択できます。

9. **[REST API クライアントの追加]** ダイアログ ボックスで **[OK]** をクリックします。

	Visual Studio が API アプリを元に命名したフォルダーを作成し、クライアント クラスを生成します。

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. *Controllers\\ContactsController.cs* を開き、生成したクライアントで API を呼び出すコードを確認します。

	次のスニペットは、クライアント オブジェクトをインスタンス化し、Get メソッドを呼び出す方法を示しています。

		private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	このコードにより、クライアント クラス コンストラクターへの API プロジェクトのローカル IIS Express URL に渡されるので、MVC Web プロジェクトと API プロジェクトをローカルで実行できます。コンストラクター パラメーターを省略した場合、既定のエンドポイントはコードの生成元の URL です。

6. クライアント クラスは API アプリ名に基づく別の名前で生成されます。型名がプロジェクトで生成されたものに一致するようにこのコードを変更します。

7. ソリューションをビルドします。

MVC プロジェクトのコントローラーとビューは、Entity Framework に対してスキャフォールディングされたコントローラーとビューに似ています。これはそのように作られているためです。つまり、Entity Framework データ モデルをスキャフォールディングし、Entity Framework データベース コンテキストの代わりに REST API クライアントを使用するように細かな変更を加えるという方法です。

### ローカルで実行する

1. ContactsList.API の開始時、ContactsList.MVC の前に、ContactsList.API および ContactsList.MVC プロジェクトをスタートアップ プロジェクトとして設定します。(ソリューションを右クリックし、**[プロパティ]** をクリックし、**[マルチ スタートアップ プロジェクト]** をクリックし、各プロジェクトを **[開始]** に設定します。上/下矢印アイコンを使用し、ContactsList.API をリストの最初にします。) 

2. F5 キーを押してプロジェクトを開始します。

	1 つのブラウザーに API の 403 ページが表示され、別のブラウザーに MVC アプリのホーム ページが表示されます。

3. MVC アプリのホーム ページを表示するブラウザーのメニュー バーで **[連絡先]** をクリックします。

	MVC の UI には、ローカルに格納されている連絡先が表示されます。UI を使用して、連絡先の追加および削除ができます。

	![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

### Azure で Web アプリを作成し、それに ContactsList.MVC プロジェクトをデプロイする

このセクションでは、先に Web アプリを作成した同じメソッドで API アプリを作成し、同じメソッドで Web プロジェクトを Azure Web アプリにデプロイします。

#### Azure API アプリを指すように MVC プロジェクトを変更します。 

Azure にデプロイする前に、コードがデプロイされると、localhost の代わりに先に作成した Azure API アプリを呼び出すように、MVC プロジェクトで API エンドポイントを変更します。

1. ContactsList.MVC プロジェクトで *Controllers\\ContactsController.cs* を開きます。

2. API ベース URL を localhost URL に設定する行をコメントアウトし、コンストラクター パラメーターのない行をコメント解除します。コードは、両方の行のクラス名にコード生成元の API アプリの名前が反映されることを除き、次の例のようになります。

		private ContactsListAPI db = new ContactsListAPI();
		//private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));

	既定のターゲット URL は Azure API アプリです。これはそこからコードを生成したためです。別のメソッドでコードを生成したのであれば、場合によっては、ローカル URL を指定したときと同じ方法で Azure API アプリ URL を指定する必要があります。

#### MVC サイトをホストする Web アプリを作成する

1. **ソリューション エクスプローラー**で ContactsList.MVC プロジェクトを右クリックし、**[発行]** をクリックします。

2. **[Web の発行]** ウィザードで、**[プロファイル]** タブをクリックします。

3.  **Web の発行**ウィザードの **[プロファイル]** タブで、**[Microsoft Azure App Service]** をクリックします。

5. **[App Service]** ダイアログ ボックスで、**[新規]** をクリックします。

3. **[App Service の作成]** ダイアログ ボックスの **[ホスティング]** タブで、**[種類の変更]** をクリックし、種類が **[Web アプリ]** になっていることを確認します。

4. *azurewebsites.net* ドメインに固有の **Web アプリの名前** を入力します。

5. 使用する Azure **サブスクリプション**を選択します。

6. **[リソース グループ]** ドロップダウンで、先に作成したものと同じリソース グループを選択します。

4. **[App Service プラン]** ドロップダウンで、先に作成した同じプランを選択します。

7. **[作成]** をクリックします。

	Visual Studio により Web アプリが作成され、その発行プロファイルが作成され、**[Web の発行]** ウィザードの **[接続]** ステップが表示されます。

### ContactsList.Web プロジェクトを新しい Web アプリにデプロイする

3.  **Web の発行**ウィザードの **[接続]** ステップで、**[発行]** をクリックします。

	Visual Studio によって、ContactsList.MVC プロジェクトが新しい Web アプリにデプロイされ、Web アプリの URL がブラウザーで開きます。ローカルで表示されていたものと同じ MVC UI が現れます。ただし、今回は Azure API アプリのファイル システムに保存されている連絡先が表示されます。

	![](./media/app-service-api-dotnet-get-started/codegencontacts.png)

## 次のステップ

このチュートリアルでは、API アプリを作成、それにコードをデプロイし、.NET クライアントから使用する方法について学習しました。入門シリーズの次のチュートリアルでは、[CORS を利用し、JavaScript クライアントから API アプリを使用する](app-service-api-cors-consume-javascript.md)方法について学習します。

<!---HONumber=AcomDC_1203_2015-->