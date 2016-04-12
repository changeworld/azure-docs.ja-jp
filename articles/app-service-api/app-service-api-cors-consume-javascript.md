<properties
	pageTitle="App Service における CORS のサポート | Microsoft Azure"
	description="Azure App Service の CORS 機能の使い方について説明します。"
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
	ms.topic="get-started-article"
	ms.date="03/31/2016"
	ms.author="tdykstra"/>

# CORS を使用して JavaScript から API アプリを使用する

## 概要

App Service は、API アプリや Web アプリ、モバイル アプリでホストされている API を JavaScript クライアントからドメインの境界を越えて呼び出すことができるクロス オリジン リソース共有 (CORS) をネイティブでサポートしています。この機能が App Service に備わっていることで、独自の API でコードを記述することなく CORS を利用することができます。

[CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) は、JavaScript から別のドメイン (ブラウザーで JavaScript を読み込んだときとは異なるドメイン) の API を呼び出すことができるインターネット プロトコルです。CORS を使用しなかった場合、contoso.com という Web ページから contoso.com の API エンドポイントに対して呼び出しを行うことはできますが、fabrikam.com のエンドポイントに対して呼び出しを行うことはできません。

この記事には 2 つのセクションが含まれます。

* 「[CORS の構成方法](#corsconfig)」セクションでは、API アプリ、Web アプリ、モバイル アプリ用に CORS を構成する方法について説明します。.NET、Node.js、Java など、App Service でサポートされるすべてのフレームワークを対象としています。 

* 「[.NET 入門チュートリアルの続行](#tutorialstart)」セクション以降では、[App Service の機能の使用方法を説明するチュートリアル シリーズの第 1 回](app-service-api-dotnet-get-started.md)で作成したアプリを基に、CORS の機能のデモンストレーションを行います。

## <a id="corsconfig"></a> Azure App Service での CORS の構成方法

CORS は、Azure ポータル、または [Azure Resource Manager](../resource-group-overview.md) ツールを使用して構成できます。

#### Azure ポータルで CORS を構成する

8. ブラウザーで、[Azure ポータル](https://portal.azure.com/)に移動します。

2. **[App Services]** をクリックした後、API アプリの名前をクリックします。

	![Select API app in portal](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. **[API アプリ]** ブレードの右側に表示される **[設定]** ブレードで、**[API]** セクションを探し、**[CORS]** をクリックします。

	![Select CORS in Settings blade](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. テキスト ボックスに、JavaScript の呼び出し元として許可する URL を入力します。


	たとえば、todolistangular という名前の Web アプリに JavaScript アプリケーションをデプロイした場合、「https://todolistangular.azurewebsites.net」と入力します。代わりに、元のドメインをすべて受け入れることを指定するアスタリスク (*) を入力することができます。


13. **[保存]** をクリックします。

	![[保存] をクリックします。](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	**[保存]** をクリックすると、API アプリは、指定した URL からの JavaScript 呼び出しを受け付けるようになります。

### Azure リソース マネージャー ツールを使用して CORS を構成する

API アプリに使用する CORS は、コマンド ライン ツール ([Azure PowerShell](../powershell-install-configure.md)、[Azure CLI](../xplat-cli-install.md) など) から [Azure Resource Manager テンプレート](../resource-group-authoring-templates.md)を使って構成することもできます。

CORS のプロパティを設定する Azure Resource Manager テンプレートの例については、[このチュートリアルのサンプル アプリケーションのリポジトリにある azuredeploy.json ファイル](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json)を参照してください。テンプレートに次のようなセクションがあります。

		"cors": {
		    "allowedOrigins": [
		        "todolistangular.azurewebsites.net"
		    ]
		}

## <a id="tutorialstart"></a> .NET 入門チュートリアルの続行

API アプリの Node.js または Java 入門シリーズに従って学習している場合は、次の記事「[Azure App Service での API Apps の認証と承認](app-service-api-authentication.md)」に進みます。

この記事の残りの部分は .NET 入門シリーズの続きであり、[最初のチュートリアル](app-service-api-dotnet-get-started.md)を問題なく完了したことを前提としています。

## 新しい Web アプリに ToDoListAngular プロジェクトをデプロイする

[最初のチュートリアル](app-service-api-dotnet-get-started.md)では、中間層の API アプリとデータ層の API アプリを作成しました。このチュートリアルでは、中間層の API アプリを呼び出す単一ページ アプリケーション (SPA) Web アプリを作成します。SPA を使用するには、中間層 API アプリで CORS を有効にする必要があります。

[ToDoList サンプル アプリケーション](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list)の ToDoListAngular プロジェクトは、中間層の ToDoListAPI Web API プロジェクトを呼び出す簡単な AngularJS クライアントです。*app/scripts/todoListSvc.js* ファイルの JavaScript コードは、AngularJS HTTP プロバイダーを使用して API を呼び出します。

		angular.module('todoApp')
		.factory('todoListSvc', ['$http', function ($http) {
		    var apiEndpoint = "http://localhost:46439";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems : function(){
		            return $http.get(apiEndpoint + '/api/TodoList');
		        },

		        /* Get by ID, Put, and Delete methods not shown */

		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/TodoList', item);
		        }
		    };
		}]);

### ToDoListAngular プロジェクト用の新しい Web アプリを作成する

新しい Web アプリを作成してそこにプロジェクトをデプロイする手順は、このシリーズの最初のチュートリアルで示したものと同様です。ただしアプリの種類が **API アプリ**ではなく **Web アプリ**になります。

1. **ソリューション エクスプローラー**で ToDoListAngular プロジェクトを右クリックし、**[発行]** をクリックします。

3.  **Web の発行**ウィザードの **[プロファイル]** タブで、**[Microsoft Azure App Service]** をクリックします。

5. **[App Service]** ダイアログ ボックスで、**[新規]** をクリックします。

3. **[App Service の作成]** ダイアログ ボックスの **[ホスティング]** タブで、*azurewebsites.net* ドメインに一意の **Web アプリ名**を入力します。

5. 使用する Azure **サブスクリプション**を選択します。

6. **[リソース グループ]** ボックスの一覧で、先に作成したものと同じリソース グループを選択します。

4. **[App Service プラン]** ボックスの一覧で、先に作成した同じプランを選択します。

7. **[作成]** をクリックします。

	Visual Studio により Web アプリが作成され、その発行プロファイルが作成され、**Web の発行**ウィザードの **[接続]** ステップが表示されます。

	まだ **[発行]** はクリックしないでください。次のセクションで、App Service で実行されている中間層の API アプリを呼び出すように新しい Web アプリを構成します。

### Web アプリの設定で中間層の URL を設定する

1. [Azure ポータル](https://portal.azure.com/)に移動し、TodoListAngular (フロント エンド) プロジェクトをホストする目的で作成した Web アプリの **[Web アプリ]** ブレードに移動します。

2. **[設定]、[アプリケーションの設定]** の順にクリックします。

3. **[アプリ設定]** セクションで、次のキーと値を追加します。

	|キー|値|例
	|---|---|---|
	|toDoListAPIURL|https://{your middle tier API app name}.azurewebsites.net|https://todolistapi0121.azurewebsites.net|

4. **[保存]** をクリックします。

	Azure でコードを実行すると、*Web.config* ファイルにある localhost の URL がこの値で上書きされます。

	設定値を取得するコードは *index.cshtml* にあります。

		<script type="text/javascript">
		    var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
		</script>
		<script src="app/scripts/todoListSvc.js"></script>

	この設定は、*todoListSvc.js* 内のコードで使用されます。

		return {
		    getItems : function(){
		        return $http.get(apiEndpoint + '/api/TodoList');
		    },
		    getItem : function(id){
		        return $http.get(apiEndpoint + '/api/TodoList/' + id);
		    },
		    postItem : function(item){
		        return $http.post(apiEndpoint + '/api/TodoList', item);
		    },
		    putItem : function(item){
		        return $http.put(apiEndpoint + '/api/TodoList/', item);
		    },
		    deleteItem : function(id){
		        return $http({
		            method: 'DELETE',
		            url: apiEndpoint + '/api/TodoList/' + id
		        });
		    }
		};

### 新しい Web アプリに ToDoListAngular Web プロジェクトをデプロイする

*  Visual Studio の **Web の発行**ウィザードの **[接続]** ステップで、**[発行]** をクリックします。

	Visual Studio は、ToDoListAngular プロジェクトを新しい Web アプリにデプロイし、ブラウザーで Web アプリの URL を開きます。

### CORS を有効にしないでアプリケーションをテストする 

2. ブラウザー開発者ツールで、コンソール ウィンドウを開きます。

3. AngularJS UI が表示されているブラウザーのウィンドウで、**[To Do List]** リンクをクリックします。

	JavaScript のコードは中間層 API アプリの呼び出しを試みますが、フロントエンドはバックエンドとは異なるドメインで実行しているため、呼び出しは失敗します。ブラウザーの開発者ツールのコンソール ウィンドウには、クロス オリジンのエラー メッセージが表示されます。

	![Cross-origin error message](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## 中間層 API アプリ用に CORS を構成する

このセクションでは、ToDoListAngular プロジェクト用に作成した Web アプリからの JavaScript 呼び出しを許可するように、ToDoListAPI API アプリを構成します。
 
8. ブラウザーで、[Azure ポータル](https://portal.azure.com/)に移動します。

2. **App Services** をクリックして、ToDoListAPI (中間層) の API アプリをクリックします。

	![Select API app in portal](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. **[API アプリ]** ブレードの右側に表示される **[設定]** ブレードで、**[API]** セクションを探し、**[CORS]** をクリックします。

	![Select CORS in portal](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. テキスト ボックスに、ToDoListAngular (フロントエンド) Web アプリの URL を入力します。たとえば、todolistangular0121 という名前の Web アプリに ToDoListAngular プロジェクトをデプロイした場合は、URL `https://todolistangular0121.azurewebsites.net` からの呼び出しを許可します。

	代わりに、元のドメインをすべて受け入れることを指定するアスタリスク (*) を入力することができます。

13. **[保存]** をクリックします。

	![[保存] をクリックします。](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	**[保存]** をクリックすると、API アプリは、指定した URL からの JavaScript 呼び出しを受け付けるようになります。このスクリーン ショットでは、ToDoListAPI0223 API アプリは ToDoListAngular Web アプリから JavaScript クライアントの呼び出しを受け入れています。

### CORS を有効にしてアプリケーションをテストする

* ブラウザーを開き、Web アプリの HTTPS URL に移動します。 

	今度は、アプリケーションで To Do 項目を表示、追加、編集、削除できます。

	![To Do List page of sample app](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## App Service の CORS と Web API の CORS

Web API プロジェクトでは、[Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) の NuGet パッケージをインストールして、API が JavaScript の呼び出しを受け入れるドメインをコードで指定できます。
 
Web API の CORS サポートは、App Service の CORS サポートよりも柔軟です。たとえば、コード内の異なるアクション メソッドにさまざまな承認済みのオリジンを指定することができます。一方、App Service CORS の場合、すべての API アプリのメソッドに承認済みのオリジンを 1 セット指定します。

> [AZURE.NOTE] 1 つの API アプリ内で、Web API CORS と App Service CORS の両方を使用しないでください。App Service CORS が優先され、Web API CORS は効果がありません。たとえば、App Service で元のドメインを 1 つ有効にして、Web API コードですべての元のドメインを有効にした場合、Azure API アプリは Azure で指定したドメインからの呼び出しのみを受け付けます。

### Web API コードで CORS を有効にする方法

次の手順では、Web API の CORS サポートを有効にするためのプロセスを概説します。詳細については、「[Enabling Cross-Origin Requests in ASP.NET Web API 2 (ASP.NET Web API 2 でのクロスオリジン要求の有効化)](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)」を参照してください。

1. Web API プロジェクトで、[Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet パッケージをインストールします。

1. 次の例に示すように、**WebApiConfig** クラスの **Register** メソッドに `config.EnableCors()` のコード行を追加します。

		public static class WebApiConfig
		{
		    public static void Register(HttpConfiguration config)
		    {
		        // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
		        config.EnableCors();
	
		        // Web API routes
		        config.MapHttpAttributeRoutes();
	
		        config.Routes.MapHttpRoute(
		            name: "DefaultApi",
		            routeTemplate: "api/{controller}/{id}",
		            defaults: new { id = RouteParameter.Optional }
		        );
		    }
		}

1. Web API コントローラーで、`System.Web.Http.Cors` 名前空間に `using` ステートメントを追加し、コントローラー クラスまたは個々のアクション メソッドに `EnableCors` 属性を追加します。次の例では、コントローラー全体に CORS のサポートが適用されます。

		namespace ToDoListAPI.Controllers 
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ToDoListController : ApiController
 
	> **注**: この属性は注意して使用してください。すべてのパラメーターにワイルドカードを指定すると、すべてのオリジンおよびすべての HTTP 要求に API が開放されます。ここに示した設定はデモンストレーションを目的としたものであり、それ以外の用途は想定していません。

## トラブルシューティング

このチュートリアルの手順を行う際に問題が発生した場合は、必ず最新バージョンの Azure SDK for .NET を使用するようにしてください。これを行う最も簡単な方法としては、[Azure SDK for Visual Studio 2015 をダウンロード](http://go.microsoft.com/fwlink/?linkid=518003)します。最新バージョンをインストール済みの場合は、Web Platform Installer によってインストールが不要であることが示されます。

ポータルの CORS ブレードで URL を設定した後も CORS エラーが発生する場合は、適切な場所で正しく変更を行ったかどうかを再確認してください。次に例を示します。

* 入力したプロトコルが正しいことを確認 (`http` ではなく `https`) し、`https` を使用してフロントエンド Web アプリを実行していることを確認します。
* フロントエンドの Web アプリではなく中間層の API アプリで CORS 設定を入力したことを確認します。

アプリケーション コードと Azure App Service の両方で CORS の設定を行っている場合、App Service の CORS 設定が、アプリケーション コードのどのような設定よりも優先されることに注意してください。

トラブルシューティングを効率化する Visual Studio の機能について詳しくは、「[Visual Studio を使用した Azure App Service のトラブルシューティング](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)」を参照してください。

## 次のステップ 

この記事では、クライアントの JavaScript コードが、別のドメイン内の API を呼び出すための App Service の CORS サポートを有効にする方法を説明しました。引き続き API Apps の入門シリーズの記事で、[App Service の API アプリにおける認証](app-service-api-authentication.md)について説明します。

<!---HONumber=AcomDC_0406_2016-->