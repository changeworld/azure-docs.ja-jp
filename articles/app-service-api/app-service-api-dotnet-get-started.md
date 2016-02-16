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
	ms.date="02/05/2016"
	ms.author="tdykstra"/>

# Azure App Service で API Apps と ASP.NET を使用する

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概要

API の開発とホストに役立つ Azure App Service の機能の使用方法を説明するチュートリアル シリーズの第 1 回です。

* API メタデータの統合サポート
* CORS のサポート
* 認証と承認のサポート

サンプル アプリケーションを [API アプリ](app-service-api-apps-why-best-platform.md)と Azure App Service の Web アプリという 2 つのアプリにデプロイします。サンプル アプリは to-do リストです。図のように、AngularJS シングル ページ アプリケーション (SPA) のフロント エンド、ASP.NET Web API の中間層、ASP.NET Web API データ層があります。

![](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

次の図は、SPA フロント エンドのスクリーン ショットです。

![](./media/app-service-api-dotnet-get-started/todospa.png)

このチュートリアルを完了すると、2 つの Web API が完成し、App Service API アプリで実行できるようになります。このチュートリアルを完了した後は、App Service Web アプリの SPA を使用し、クラウドでアプリケーション全体を実行します。以降のチュートリアルでは、認証と承認を追加します。

## 学習内容

このチュートリアルでは、次のことについて説明します。

* Azure SDK for .NET をインストールして、Azure 向け開発用にコンピューターを準備する方法。
* Visual Studio 2015 に組み込まれたツールを利用し、Azure App Service で API アプリと Web アプリを使用する方法。
* Swashbuckle NuGet パッケージを利用して API 検出を自動化し、Swagger API 定義 JSON を動的に生成する方法。
* 自動的に生成されたクライアント コードを使用し、.NET クライアントから API アプリを使用する方法。
* Azure ポータルを使用し、API アプリ メタデータのエンドポイントを構成する方法。

## 前提条件

[AZURE.INCLUDE [前提条件](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

[AZURE.INCLUDE [set-up-dev-environment](../../includes/install-sdk-2015-2013.md)]

このチュートリアルでは、Azure SDK for .NET 2.8.2 以降のバージョンが必要です。

## サンプル アプリケーションのダウンロード 

1. [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list) リポジトリをダウンロードします。

	**[Download ZIP]** ボタンをクリックするか、ローカル コンピューターのリポジトリを複製します。

2. Visual Studio 2015 または 2013 で ToDoList ソリューションを開きます。

	この Visual Studio ソリューションは、説明と所有者で構成されるシンプルな to-do 項目を操作するサンプル アプリケーションです。

		public class ToDoItem 
		{ 
		    public int ID { get; set; } 
		    public string Description { get; set; } 
		    public string Owner { get; set; } 
		} 
 
	ソリューションには、3 つのプロジェクトが含まれています。

	![](./media/app-service-api-dotnet-get-started/projectsinse.png)

	* **ToDoListAngular** - フロント エンド: 中間層を呼び出す AngularJS SPA。 

	* **ToDoListAPI** - 中間層: to-do 項目に対して CRUD 操作を実行するデータ層を呼び出す ASP.NET Web API プロジェクト。

	* **ToDoListDataAPI** - データ層: to-do 項目に対して CRUD 操作を実行する ASP.NET Web API プロジェクト。to-do 項目はメモリに格納されます。つまり、アプリケーションを再起動するたびに、すべての変更は消去されます。

	データ層を呼び出すと、中間層から `Owner` フィールドにユーザー ID が渡されます。ダウンロード時のコードでは、ユーザー ID は常に "*" です。今後のチュートリアルで認証を追加すると、中間層からデータ層に実際のユーザー ID が渡されます。

2. ソリューションをビルドし、NuGet パッケージを復元します。

### 省略可能: アプリケーションをローカルで実行する

このセクションでは、クライアントをローカルで実行でき、API もローカルで実行されている間にはこれを呼び出すことができることを確認します。

**注:** これらのブラウザーでは、`http://localhost` の URL に対してクロス オリジン JavaScript の呼び出しを許可するため、これらの手順を Internet Explorer と Edge ブラウザーに使用できます。Chrome を使用している場合は、`--disable-web-security` スイッチでブラウザーを起動します。Firefox を使用している場合は、このセクションをスキップします。

1. 3 つのプロジェクトすべてをスタートアップ プロジェクトとして設定します。実行順序は ToDoListDataAPI、ToDoListAPI、ToDoListAngular です(ソリューションを右クリックし、**[プロパティ]** をクリックし、**[マルチ スタートアップ プロジェクト]** を選択して、正しい順序でプロジェクトを設定し、各プロジェクトの **[アクション]** を **[開始]** に設定します)。  

2. F5 キーを押してプロジェクトを開始します。

	2 つのブラウザー ウィンドウが開き、HTTP 403 エラー ページ が表示されますが、これは Web API プロジェクトの場合に通常の結果です。3 つ目のブラウザー ウィンドウには AngularJS UI が表示されます。

3. AngularJS UI が表示されているブラウザーのウィンドウで、**[To Do List]** タブをクリックします。

	UI には、2 つの既定の to-do 項目が表示されます。

	![](./media/app-service-api-dotnet-get-started/todospa.png)

4. to-do 項目を追加、編集、削除して、アプリケーションの動作を確認します。

	すべての変更内容はメモリに保存され、アプリケーションを再起動すると消去されます。

3. ブラウザー ウィンドウを閉じます。

## Swagger のメタデータと UI の使用

[Swagger 2.0](http://swagger.io/) API メタデータのサポートは、Azure App Service に組み込まれています。それぞれの API アプリで、API のメタデータを Swagger の JSON 形式で返す URL エンドポイントを指定できます。そのエンドポイントから返されたメタデータを使用して、クライアント コードを生成できます。

[Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet パッケージには、ASP.NET Web API プロジェクト用の Swagger 2.0 メタデータが用意されています。Swashbuckle はリフレクションを使用し、メタデータを動的に生成します。

Swashbuckle NuGet パッケージは、ダウンロードしたプロジェクトの ToDoListDataAPI と ToDoListAPI に既にインストールされています。また、**Azure API アプリ** プロジェクト テンプレートを使用して新しいプロジェクトを作成した場合も、既にインストールされています(Visual Studio で **[ファイル]、[新規作成]、[プロジェクト]、[ASP.NET Web アプリケーション]、[Azure API アプリ]** の順にクリック)。

チュートリアルのこのセクションでは、生成した Swagger 2.0 メタデータを確認し、Swagger メタデータに基づいてテスト UI を試します。

2. スタートアップ プロジェクトとして ToDoListDataAPI プロジェクトを設定します。 
 
4. F5 キーを押してデバッグ モードでプロジェクトを実行します。

	ブラウザーが開き、HTTP 403 エラー ページが表示されます。

12. ブラウザーのアドレス バーで、行の末尾に `swagger/docs/v1` を追加し、Return を押します。(URL は `http://localhost:45914/swagger/docs/v1` です。)

	これは API の Swagger 2.0 JSON メタデータを返すために Swashbuckle で使用される既定の URL です。Internet Explorer を使用している場合、*v1.json* ファイルをダウンロードするように求められます。

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Chrome、Firefox、または Edge を使用している場合、ブラウザーのウィンドウで JSON が表示されます。

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	次の例は、API の Swagger メタデータの最初のセクションと Get メソッドの定義です。このメタデータに基づき、次の手順とチュートリアルの後半で使用する Swagger UI はクライアント コードを自動生成します。

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ToDoListDataAPI"
		  },
		  "host": "localhost:45914",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/ToDoList": {
		      "get": {
		        "tags": [ "ToDoList" ],
		        "operationId": "ToDoList_GetByOwner",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "parameters": [
		          {
		            "name": "owner",
		            "in": "query",
		            "required": true,
		            "type": "string"
		          }
		        ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/ToDoItem" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. ブラウザーを閉じます。

3. **ソリューション エクスプローラー**の ToDoListDataAPI プロジェクトで、*App\_Start\\SwaggerConfig.cs* ファイルを開き、次のコードまで下にスクロールし、そのコメントを解除します。

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	*SwaggerConfig.cs* ファイルは、プロジェクトに Swashbuckle パッケージをインストールするときに作成されます。このファイルでは、さまざまな方法で Swashbuckle を構成できます。

	コメントを解除したコードにより、以降の手順で使用する Swagger UI が有効になります。API アプリ プロジェクト テンプレートを使用して Web API プロジェクトを作成すると、既定では、セキュリティ対策としてこのコードはコメントアウトされています。

5. プロジェクトを再度実行します。

3. ブラウザーのアドレス バーで、行の末尾に `swagger` を追加し、Return を押します。(URL は `http://localhost:45914/swagger` です。)

4. Swagger UI ページが表示されたら、**[ToDoList]** をクリックし、利用できるメソッドを確認します。

	![](./media/app-service-api-dotnet-get-started/methods.png)

5. **[Get]** をクリックします。

6. `owner` パラメーターの値にアスタリスク (*) を入力し、**[Try it out]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	Swagger UI から ToDoList Get メソッドが呼び出され、JSON 結果が表示されます。

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. **[Post]** をクリックし、**[モデル スキーマ]** の下にあるボックスをクリックします。

	モデル スキーマをクリックすると、入力ボックスに事前入力されます。このボックスには、Post メソッドのパラメーター値を指定できます。Internet Explorer で動作しない場合は、別のブラウザーを使用するか、次の手順でパラメーター値を手動入力します。

	![](./media/app-service-api-dotnet-get-started/post.png)

7. `contact` パラメーター入力ボックスの JSON を次の例のように変更するか、独自の説明文に書き換えます。

		{
		  "ID": 2,
		  "Description": "buy the dog a toy",
		  "Owner": "*"
		}

10. **[Try it out]** をクリックします。

	ToDoList API から、成功を示す HTTP 204 応答コードが返されます。

11. **[Get]、[Try it out]** を順にクリックします。

	Get メソッドの応答に、新しい to-do 項目が含まれるようになります。

12. Put、Delete、Get by ID メソッドも試し、ブラウザーを閉じます。

Swashbuckle はあらゆる ASP.NET Web API プロジェクトで利用できます。Swagger メタデータ生成を既存のプロジェクトに追加する場合、Swashbuckle パッケージをインストールします。新規プロジェクトを作成するには、次の図のように ASP.NET **Azure API App** プロジェクト テンプレートを使用します。そのテンプレートにより、Swashbuckle がインストールされた Web API プロジェクトが作成されます。

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

**注:** 既定では、コントローラー メソッドに対して重複する Swagger 操作 ID が Swashbuckle によって生成される場合があります。この現象は、コントローラーに HTTP メソッドのオーバーロード (`Get()` と `Get(id)` など) が存在すると発生します。オーバーロードの扱い方については、「[Swashbuckle が生成する API 定義をカスタマイズする](app-service-api-dotnet-swashbuckle-customize.md)」を参照してください。Visual Studio から Azure API アプリ テンプレートを使って Web API プロジェクトを作成した場合、一意の操作 ID を生成するコードが *SwaggerConfig.cs* ファイルに自動的に追加されます。

## Azure で API アプリを作成して ToDoListAPI プロジェクトをデプロイする

このセクションでは、Visual Studio の **Web の発行**ウィザードに統合されている Azure ツールを使用し、Azure で新しい API アプリを作成します。次に、ToDoListDataAPI プロジェクトを新しい API アプリにデプロイし、今度はクラウドで実行中に Swagger UI を再実行して API を呼び出します。

1. **ソリューション エクスプローラー**で ToDoListDataAPI プロジェクトを右クリックし、**[発行]** をクリックします。

3.  **Web の発行**ウィザードの **[プロファイル]** ステップで、**[Microsoft Azure App Service]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. まだ行っていない場合は Azure アカウントにサインインし、有効期限が切れている場合は資格情報を更新します。

4. [App Service] ダイアログ ボックスで、使用する Azure **サブスクリプション**を選択して、**[新規]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. **[App Service の作成]** ダイアログ ボックスの **[ホスティング]** タブで、**[種類の変更]**、**[API アプリ]** を順にクリックします。

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	**API アプリ**のタイプを設定することで、新しいアプリに利用可能な機能が決定されることはありません。API 定義の URL (このチュートリアルの後半で説明されます)、CORS のサポート (次のチュートリアルにあります)、および認証 (このシリーズの最後の 3 つのチュートリアルにあります) は、Web アプリ、モバイル アプリ、および API アプリに利用できます。API アプリとしてアプリを作成して得られるのは、次の効果のみです。

	a.Azure ポータルでは、ブレードの見出しとアプリ一覧にアプリの種類のアイコンまたはテキストが表示されます。また、**[設定]** ブレードの API アプリ一覧では、API セクションは他のアプリの種類よりも先に表示されます。

	b.Azure SDK for .NET 2.8.1 を使用する Visual Studio で、Visual Studio は新しい ASP.NET API アプリの作成中に、API 定義の URL を設定します。ただし、他のアプリの種類の場合は設定しません。

4. *azurewebsites.net* ドメイン内で一意の **[API アプリ名]** を入力します (ToDoListDataAPI に番号を付加するなどして一意にします)。

	Visual Studio により、プロジェクト名に date-time 文字列が追加された一意の名前が提案されます。その名前で問題なければ、それを利用して構いません。

	入力した名前が既に使用されている場合は、右側に緑色のチェック マークではなく赤色の感嘆符が表示されます。この場合、別の名前を入力する必要があります。

	Azure では、この名前がアプリケーションの URL のプレフィックスとして使用されます。URL 全体は、この名前に *.azurewebsites.net* を追加して構成されます。たとえば、名前が `ToDoListDataAPI` である場合、URL は `todolistdataapi.azurewebsites.net` です。

6. **[リソース グループ]** ドロップダウンに「ToDoListGroup」と入力するか、別の名前を入力します。

	このボックスでは既存の[リソース グループ](../azure-preview-portal-using-resource-groups.md)を選択するか、サブスクリプションの既存のリソース グループとは異なる名前を入力し、新しいリソース グループを作成できます。

	このチュートリアルでは、新しいリソース グループを作成すると便利です。チュートリアルのために作成したすべての Azure リソースを 1 回の手順で簡単に削除できるからです。

4. **[App Service プラン]** ドロップダウンの隣にある **[新規]** ボタンをクリックします。

	![](./media/app-service-api-dotnet-get-started/createas.png)

	App Service プランの詳細については、[App Service プランの概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)に関するページを参照してください。

5. **[App Service プランの構成]** ダイアログに「ToDoListPlan」と入力するか、別の名前を入力します。

5. **[場所]** ドロップダウン リストで、現在の所在地に最も近い場所を選択します。

	この設定によって、アプリが実行される Azure データ センターが指定されます。このチュートリアルでは、任意のリージョンを選択することができます。任意のリージョンを選択しても、大きな違いはありません。ただし、運用アプリでは、[待機時間](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)を最小限に抑えるために、アクセスするクライアントに最も近いサーバーを選択することをお勧めします。

5. **[サイズ]** ドロップダウンで、**[Free]** をクリックします。

	無料の価格レベルでこのチュートリアルに十分な性能が提供されます。

6. **[App Service プランの構成]** ダイアログで、**[OK]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. **[App Service の作成]** ダイアログ ボックスで、**[作成]** をクリックします。

	Visual Studio を使うと、API アプリが作成され、新しい API アプリに必要なすべての設定が含まれる発行プロファイルが作成されます。次の手順では、新しい発行プロファイルを使用し、プロジェクトをデプロイします。
 
	**注:** Azure App Service で API アプリを作成する方法は他にもあります。Visual Studio では、新しいプロジェクトを作成するとき、同じダイアログが利用できます。Azure ポータル、[Windows PowerShell 用の Azure コマンドレット](../powershell-install-configure.md)、[クロスプラットフォーム コマンドライン インターフェイス](../xplat-cli.md)を利用し、API アプリを作成することもできます。

8. **Web の発行**ウィザードの **[接続]** タブで、**[発行]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio によって ToDoListDataAPI プロジェクトは新しい API アプリにデプロイされ、ブラウザーで API アプリの URL が開きます。ブラウザーに "正常に作成されました" ページが表示されます。

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. ブラウザーのアドレス バーの URL に「swagger」を追加し、Enter キーを押します。(URL は `http://{apiappname}.azurewebsites.net/swagger` です。)

	ブラウザーに前に見た Swagger UI が表示されますが、今度はクラウドで実行しています。前回の変更はローカル コンピューターのメモリに保存されているので、Get メソッドを試して、既定の 2 つの to-do 項目に戻ることを確認します。

12. [Azure ポータル](https://portal.azure.com/) を開きます。
 
14. **[参照]、[API Apps]、{新しい API アプリ}** の順にクリックします。

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. **[設定]** をクリックします。次に、**[設定]** ブレードで API セクションを見つけ、**[API 定義]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	API 定義ブレードでは、JSON 形式で Swagger 2.0 メタデータを返す URL を指定できます。Visual Studio により API アプリが作成されるとき、先に見た既定値 (API アプリの基礎 URL + `/swagger/docs/v1`) に API 定義 URL が設定されます。

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	クライアント コードを生成する API アプリを選択するとき、Visual Studio はこの URL からメタデータを取得します。

### Azure リソース マネージャーのツールにおける API 定義 URL

API アプリの API 定義 URL は、Azure リソース マネージャーのツール (Azure PowerShell、CLI、[リソース エクスプローラー](https://resources.azure.com/)など) を使って構成することもできます。

構成するには、使用している `<site name>/web` リソースの `Microsoft.Web/sites/config` リソースの種類で、`apiDefinition` プロパティを設定します。たとえば、**リソース エクスプローラー**から **[subscriptions]、{該当するサブスクリプション}、[resourceGroups]、{該当するリソース グループ}、[providers]、[Microsoft.Web]、[sites]、{該当サイト}、[config]、[web]** の順に移動すると、`apiDefinition` プロパティが表示されます。

		"apiDefinition": {
		  "url": "https://todolistdataapi.azurewebsites.net/swagger/docs/v1"
		}

API 定義プロパティを設定するための JSON を含んだ Azure リソース マネージャー テンプレートの例については、[サンプル アプリケーション リポジトリにある azuredeploy.json ファイル](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json)を開いてください。

## <a id="codegen"></a> 生成されたクライアント コードを使用して .NET クライアントから利用する

Azure API アプリと Swagger の統合の利点の 1 つは、自動コード生成です。生成されたクライアント クラスにより、API アプリを呼び出すコードの記述が容易になります。

このセクションでは、ASP.NET Web API コードから API アプリを使用する方法について確認します。

### クライアント コードの生成

API アプリのクライアント コードは、Visual Studio を使用して生成するか、コマンド ラインから生成することができます。このチュートリアルでは、Visual Studio を使用します。コマンドラインから実行する方法については、GitHub.com の [Azure/autorest](https://github.com/azure/autorest) リポジトリにある readme ファイルを参照してください。

ToDoListAPI プロジェクトでは、クライアント コードが既に生成されていますが、それを削除して再生成し、独自の API アプリを既定のターゲット URL に再設定します。

1. Visual Studio の**ソリューション エクスプローラー**で、ToDoListAPI プロジェクトの *ToDoListDataAPI* フォルダーを削除します。

	このフォルダーは、今まさに行おうとしているコード生成プロセスを使って作成されたものです。

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. ToDoListAPI プロジェクトを右クリックし、**[追加]、[REST API クライアント]** を順にクリックします。

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. **[REST API クライアントの追加]** ダイアログ ボックスの **[Swagger URL]** をクリックし、**[Azure アセットの選択]** をクリックします。

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. **[App Service]** ダイアログ ボックスで、このチュートリアルで使用しているリソース グループを展開し、API アプリを選択して、**[OK]** をクリックします。

	このダイアログ ボックスでは、API アプリが多すぎてスクロールできない場合、いくつかの方法で一覧の API アプリを整理できます。検索文字列を入力し、名前で API アプリを絞り込むこともできます。

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	API アプリが一覧に表示されない場合、API アプリを作成するときに、アプリの種類を (Web アプリから API アプリに) 変更する手順を誤って省略した可能性があります。その場合は、先行する手順をやり直して新しい API アプリを作成してください。API アプリには別の名前を選択する必要があります。

	**[REST API クライアントの追加]** ダイアログに戻ると、ポータルで先に見た API 定義 URL 値がテキスト ボックスに入力されていることに注意してください。

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	コード生成のためにメタデータを取得する代替方法は、参照ダイアログを利用する代わりに URL を直接入力します。たとえば、API を Web アプリにデプロイしたとき、それが参照ダイアログに表示されない場合、Swagger メタデータを返す URL をここに直接入力できます。

	また、メタデータを取得するには、**[既存の Swagger メタデータ ファイルを選択する]** オプションを使用する方法もあります。たとえば、Azure にデプロイする前にクライアント コードを生成する場合、ローカルで実行し、Swagger JSON ファイルをダウンロードし、そのファイルをここで選択できます。

9. **[REST API クライアントの追加]** ダイアログ ボックスで **[OK]** をクリックします。

	Visual Studio が API アプリを元に命名したフォルダーを作成し、クライアント クラスを生成します。

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. ToDoListAPI プロジェクトで *Controllers\\ToDoListController.cs* を開き、生成されたクライアントを使用して API を呼び出すコードを確認します。

	次のスニペットは、クライアント オブジェクトをインスタンス化し、Get メソッドを呼び出す方法を示しています。

		private ToDoListDataAPI db = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	コンストラクターのパラメーターでは、`toDoListDataAPIURL` アプリ設定からエンドポイントの URL を取得しています。アプリケーションをローカルで実行できるようにするため、その値を Web.config ファイルの `toDoListDataAPIURL` 設定で、API プロジェクトのローカル IIS Express URL に設定します。コンストラクター パラメーターを省略した場合、既定のエンドポイントはコードの生成元の URL です。

6. クライアント クラスは API アプリ名に基づく別の名前で生成されます。型名がプロジェクトで生成されたものに一致するようにこのコードを変更します。たとえば、API アプリに ToDoListDataAPI0121 という名前を付けた場合、コードは次のようになります。

		private ToDoListDataAPI0121 db = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

#### 中間層をホストする API アプリを作成する

1. **ソリューション エクスプローラー**で ToDoListAPI プロジェクトを右クリックし、**[発行]** をクリックします。

3.  **Web の発行**ウィザードの **[プロファイル]** タブで、**[Microsoft Azure App Service]** をクリックします。

5. **[App Service]** ダイアログ ボックスで、**[新規]** をクリックします。

3. **[App Service の作成]** ダイアログ ボックスの **[ホスティング]** タブで、**[種類の変更]** をクリックし、種類を **[API アプリ]** に変更します。

4. *azurewebsites.net* ドメインに固有の **API アプリ名**を入力します。

5. 使用する Azure **サブスクリプション**を選択します。

6. **[リソース グループ]** ドロップダウンで、先に作成したものと同じリソース グループを選択します。

4. **[App Service プラン]** ドロップダウンで、先に作成した同じプランを選択します。

7. **[作成]** をクリックします。

	Visual Studio によって API アプリとその発行プロファイルが作成され、**Web の発行**ウィザードの **[接続]** ステップが表示されます。

### 中間層のアプリ設定でデータ層の URL を設定する

1. [Azure ポータル](https://portal.azure.com/)を開き、TodoListAPI (中間層) プロジェクトをホストするために作成した API アプリの **[API アプリ]** ブレードに移動します。

2. **[設定]、[アプリケーションの設定]** の順にクリックします。

3. **[アプリ設定]** セクションで、次のキーと値を追加します。

	|キー|値|例
	|---|---|---|
	|toDoListDataAPIURL|https://{your data tier API app name}.azurewebsites.net|https://todolistdataapi0121.azurewebsites.net|

4. **[保存]** をクリックします。

	Azure でコードを実行すると、Web.config ファイルにある localhost の URL がこの値で上書きされます。

### 新しい API アプリに ToDoListAPI プロジェクトをデプロイする

3.  **Web の発行**ウィザードの **[接続]** ステップで、**[発行]** をクリックします。

	Visual Studio によって ToDoListAPI プロジェクトは新しい API アプリにデプロイされ、ブラウザーで API アプリの URL が開きます。"正常に作成されました" ページが表示されます。

### ToDoListAPI から ToDoListDataAPI が呼び出されることをテストして確認する

11. ブラウザーのアドレス バーの URL に「swagger」を追加し、Enter キーを押します。(URL は `http://{apiappname}.azurewebsites.net/swagger` です。)

	ブラウザーには、ToDoListDataAPI と同様の Swagger UI が表示されますが、今回の `owner` は必須フィールドではありません。これは、中間層 API アプリからデータ層 API アプリに値が送信されているためです。

12. Get メソッドや他のメソッドを試して、中間層 API アプリからデータ層 API アプリが正常に呼び出されていることを確認します。

	![](./media/app-service-api-dotnet-get-started/midtierget.png)

## 次のステップ

このチュートリアルでは、API アプリを作成し、それにコードをデプロイし、クライアント コードを生成し、.NET クライアントから使用する方法について学習しました。API Apps 入門シリーズの次のチュートリアルでは、[CORS を利用し、JavaScript クライアントから API アプリを使用する](app-service-api-cors-consume-javascript.md)方法について学習します。

<!---HONumber=AcomDC_0211_2016-->