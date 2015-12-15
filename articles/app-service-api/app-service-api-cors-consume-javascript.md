<properties
	pageTitle="CORS を使用して JavaScript から API アプリを使用する | Microsoft Azure"
	description="JavaScript クライアントから、および CORS を使用して Azure App Service の API アプリを使用する方法について説明します"
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
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# CORS を使用して JavaScript から API アプリを使用する

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概要

このチュートリアルでは、API アプリとは異なるドメインから提供されている Web サイトの JavaScript コードから API アプリを使用する方法を示します。サンプルのクライアントでは、AngularJS を使用します。

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
これは、Azure App Service で API アプリを使用する方法を示すチュートリアルのシリーズの 2 回目です。シリーズの第 1 回目に移動するには、ページ上部の **[Topic]** ボックスの一覧から 1 つ目のトピックを選択してください。

## Azure App Service における CORS のサポート

セキュリティ上の理由から、ブラウザーの既定の動作では JavaScript が含まれるドメイン以外のドメインに対して、JavaScript が API 呼び出しを行うことができません。たとえば、contoso.com の Web ページから contoso.com API エンドポイントには呼び出しができますが、fabrikam.com エンドポイントには呼び出しができません。クロス オリジン リソース共有 (CORS) は、このようなクロス ドメインの API 呼び出しを行う必要があるシナリオを有効にするように設計されたインターネット プロトコルです。Azure App Service のこのようなシナリオの例として、API が API アプリを実行している間に JavaScript クライアントが Web アプリを実行している場合などがあります。

Azure App Service には、API アプリの呼び出しを許可するドメインを簡単に構成する仕組みが用意されています。CORS 機能は、Java や Node.js など、API Apps サービスでサポートされるすべての言語で同じように動作します。

## このチュートリアルの使い方

このチュートリアルは、[このシリーズの ASP.NET 版 (1 つ目のチュートリアル)](app-service-api-dotnet-get-started.md) で、作成する API アプリの利用側としてダウンロードするサンプル アプリケーションを使います。Java または Node.js を使用する場合は、以降の [CORS の構成セクション](#corsconfig)で、すべての API アプリに該当する全般的な手順を参照してください。

## ContactsList.Angular サンプル プロジェクト

[ContactsList サンプル アプリケーション](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list)の ContactsList.Angular プロジェクトは、ContactsList.API Web API プロジェクトの単純な AngularJS クライアントです。

API を呼び出す AngularJS の JavaScript コードは、ContactsList.Angular プロジェクトの *index.html* ファイルにあります。このコードは API の Get メソッドが `$scope.refresh()` に定義されている場合のように、関数を定義して、`$scope` オブジェクトに追加します。

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

ページが読み込まれ (前に示したスニペットの終了時)、UI の **[更新]** ボタンが使用されると、コードが $scope.refresh() メソッドを呼び出します。

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## AngularJS プロジェクトをローカルで実行する

このセクションでは、クライアントをローカルで実行でき、API もローカルで実行されている間にはこれを呼び出すことができることを確認します。

1. ContactsList.API の開始時、ContactsList.Angular の前に、ContactsList.API および ContactsList.Angular プロジェクトをスタートアップ プロジェクトとして設定します。 

2. F5 キーを押してプロジェクトを開始します。

	AngularJS の UI には、ローカルに格納されている連絡先が表示されます。UI を使用して、連絡先の追加および削除ができます。

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. ブラウザー ウィンドウを閉じます。

## Azure API アプリを指すように AngularJS プロジェクトを変更します。 

次に、AngularJS フロントエンドをクラウドで実行して、クラウドで実行されている API バックエンドを呼び出します。フロントエンドを Azure にデプロイする前に、前に作成した Azure API アプリがコードで呼び出されるように、AngularJS プロジェクトの API エンドポイントを変更する必要があります。

1. ContactsList.Angular プロジェクトで、*index.html* を開きます。

2. `baseUrl` を localhost の URL に設定する行をコメントアウトし、`baseUrl` を azurewebsites.net の URL に設定する行をコメント解除して、プレースホルダーを前に作成した API アプリの実際の名前に置き換えます。API アプリに ContactsListAPI という名前を付けた場合、コードは次の例のようになります。

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### ContactsList.Angular プロジェクトを Web アプリにデプロイする

AngularJS プロジェクトをデプロイする新しい Web アプリも作成できますが、このチュートリアルでは、前のチュートリアルで作成した Web アプリにデプロイします。Web アプリの名前には、元々は ASP.NET MVC プロジェクトをデプロイしていたが、このデプロイメントの後は AngularJS コードが実行されることを反映することができます。

8. **ソリューション エクスプローラー**で ContactsList.Angular プロジェクトを右クリックし、**[発行]** をクリックします。

9. **[プロファイル]** タブをクリックします。

3.  **Web の発行**ウィザードの **[プロファイル]** ステップで、**[Microsoft Azure App Service]** をクリックします。

4. **[App Service]** ダイアログ ボックスで、サブスクリプションを選択します。

5. **[ビュー]** を既定値の **[リソース グループ]** に設定した状態で、このチュートリアル シリーズ用に作成したリソース グループを展開します。

7. 最初のチュートリアルで作成した Web アプリを選択して (API アプリを選択しないでください)、**[OK]** をクリックします。

8. **[設定]** タブをクリックします。

9. **[ファイル発行オプション]** を展開して、**[ターゲットの追加ファイルの削除]** を選択します。

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	通常、既存の App Service の Web アプリに Web プロジェクトをデプロイする場合、変更は更新またはファイルの新規作成のどちらかであるため、"追加のファイルを削除する" オプションは必要ありません。この場合、異なるプロジェクトを同じ Web アプリにデプロイしているため、新しいものでは必要ない前のデプロイメントのファイルが多数発生する可能性があります。

10. **[発行]** をクリックします。

	Visual Studio によって、ContactsList.Angular プロジェクトが Web アプリにデプロイされ、Web アプリの URL がブラウザーで開きます。ブラウザーにはローカルでの実行時に表示されたのと同じ AngularJS の UI が表示されます。ただし、今はフロントエンドがバックエンドとは異なるドメイン (Web アプリの URL) で実行されているため、失敗します。

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## <a id="corsconfig"></a>Azure のターゲット API アプリに対して CORS を構成する

8. 別のブラウザー ウィンドウで、[Azure ポータル](https://portal.azure.com/)に移動します。

9. **[参照]、[API Apps]** の順にクリックし、ターゲット API アプリを選択します。このチュートリアルでは、1 つ目のチュートリアルで ContactsList.API プロジェクトに作成した API アプリが該当します。

10. **[API アプリ]** ブレードで、**[設定]** をクリックします。

11. **[API]** セクションを探し、**[CORS]** をクリックします。

12. テキスト ボックスに、呼び出し元として許可する URL を入力します。たとえば、ContactsListMVC という名前の Web アプリに JavaScript アプリケーションをデプロイした場合、「http://contactslistmvc.azurewebsites.net」と入力します。

	URL を入力する代わりに、元のドメインをすべて受け入れることを指定するアスタリスク (*) を入力することができます。

13. **[保存]** をクリックします。

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. AngularJS クライアントを表示するブラウザー ウィンドウに戻り、ページを更新するか、**[更新]** ボタンをクリックします。

	ページには、Azure API アプリのファイル システムに格納されている連絡先が表示されます。

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

### Azure リソース マネージャーのツールにおける CORS

API アプリの CORS は、Azure リソース マネージャーのツール (Azure PowerShell、CLI、[リソース エクスプローラー](https://resources.azure.com/)など) を使って構成することもできます。

`cors` プロパティは、<site name>/web リソースの Microsoft.Web/sites/config リソース タイプで設定します。たとえば、**リソース エクスプローラー**から **[subscriptions]、{該当するサブスクリプション}、[resourceGroups]、{該当するリソース グループ}、[providers]、[Microsoft.Web]、[sites]、{該当サイト}、[config]、[web]** の順に移動すると、cors プロパティが表示されます。

		"cors": {
		    "allowedOrigins": [
		        "contactslistmvc.azurewebsites.net"
		    ]
		}

### App Service の CORS と Web API の CORS

以降のセクションで取り上げるように、ASP.NET Web API プロジェクトでは、CORS をコードの中で簡単に構成することができます。ただし、App Service の CORS と Web API の CORS の両方を一緒に使っている場合、App Service の CORS が優先され、Web API の CORS は無効となります。たとえば、App Service で元のドメインを 1 つ有効にして、Web API コードですべての元のドメインを有効にした場合、Azure API アプリは Azure で指定したドメインからの呼び出しのみを受け付けます。


## Web API コードで CORS を構成する方法

Web API プロジェクトでは、API が JavaScript の呼び出しを受け入れるドメインをコードに指定できる [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) の NuGet パッケージをインストールすることができます。このプロセスの詳細は「[ASP.NET Web API 2 でクロス オリジン要求を有効にする](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)」に記載されています。ASP.NET Web API を使用してビルドされた API アプリの場合、プロセスはまったく同じですが、以下に簡単にまとめています。

1. Web API プロジェクトで **WebApiConfig** の **Register** メソッドに `config.EnableCors()` というコード行を追加します。その例を次に示します。 

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

1. Web API のコントローラーで、`ContactsController` クラスまたは個々のアクション メソッドに `EnableCors` 属性を追加します。次の例では、コントローラー全体に CORS のサポートが適用されます。

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	> **注**: `EnableCors` 属性を持つすべてのパラメーターにワイルドカードを使用しているのはデモンストレーションのみを目的としています。この場合、すべてのオリジンとすべての HTTP 要求に対して API が開かれます。この属性は注意して使用してください。

## 次のステップ 

このチュートリアルでは、クライアントの JavaScript コードが、別のドメイン内の API を呼び出すための App Service の CORS サポートを有効にする方法を説明しました。引き続き API Apps の入門シリーズの記事で、[App Service の API アプリにおける認証](app-service-api-authentication.md)について説明します。

<!---HONumber=AcomDC_1210_2015-->