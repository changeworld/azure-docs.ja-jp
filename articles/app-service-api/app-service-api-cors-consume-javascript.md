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
	ms.date="11/27/2015"
	ms.author="tdykstra"/>

# CORS を使用して JavaScript から API アプリを使用する

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概要

このチュートリアルでは、API アプリとは異なるドメインから提供されている Web サイトの JavaScript コードから API アプリを使用する方法を示します。サンプルのクライアントでは、AngularJS を使用します。

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
これは、Azure App Service で API アプリを使用する方法を示すチュートリアルのシリーズの 2 回目です。このシリーズの詳細については、「[Azure App Service で API Apps と ASP.NET を使ってみる](app-service-api-dotnet-get-started.md)」を参照してください。

## CORS の紹介

セキュリティ上の理由から、ブラウザーの既定の動作では JavaScript が含まれるドメイン以外のドメインに対して、JavaScript が API 呼び出しを行うことができません。たとえば、contoso.com の Web ページから contoso.com API エンドポイントには呼び出しができますが、fabrikam.com エンドポイントには呼び出しができません。クロス オリジン リソース共有 (CORS) は、このようなクロス ドメインの API 呼び出しを行う必要があるシナリオを有効にするように設計されたインターネット プロトコルです。Azure App Service のこのようなシナリオの例として、API が API アプリを実行している間に JavaScript クライアントが Web アプリを実行している場合などがあります。

Web API プロジェクトでは、API が JavaScript の呼び出しを受け入れるドメインをコードに指定できる CORS の NuGet パッケージをインストールすることができます。または、Azure App Service に組み込まれている CORS 機能を使用すると、API アプリが呼び出しを受け入れるドメインを指定することができます。このチュートリアルの前半では、API Apps サービスでサポートされるすべての言語で動作する Azure の機能を使用する方法を示します。後半は省略可能で、ASP.NET Web API で動作する NuGet パッケージのメソッドを示します。

## ContactsList.Angular サンプル プロジェクト

このチュートリアルでは、このシリーズの 1 回目のチュートリアルでダウンロードしたサンプル プロジェクトと、1 回目のチュートリアルで作成した Azure リソース (API アプリと Web アプリ) を使用します。

ContactsList.Angular プロジェクトは、ContactsList.API Web API プロジェクトの単純な AngularJS クライアントです。API を呼び出す AngularJS の JavaScript コードは、ContactsList.Angular プロジェクトの *index.html* ファイルにあります。このコードは API の Get メソッドが `$scope.refresh()` に定義されている場合のように、関数を定義して、`$scope` オブジェクトに追加します。

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

AngularJS プロジェクトをデプロイする新しい Web アプリも作成できますが、このチュートリアルでは、前に作成した Web アプリにデプロイします。Web アプリの名前には、元々は ASP.NET MVC プロジェクトをデプロイしていたが、このデプロイメントの後は AngularJS コードが実行されることを反映することができます。

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

## Azure のターゲット API アプリに対して CORS を構成する

8. 別のブラウザー ウィンドウで、[Azure ポータル](https://portal.azure.com/)に移動します。

9. 最初のチュートリアルで作成した API アプリの [API アプリ] ブレードに移動します。

10. **[設定]** をクリックします。

11. **[API]** セクションを探し、**[CORS]** をクリックします。

12. テキスト ボックスに、ASP.NET MVC フロントエンドの最初のチュートリアルで作成した Web アプリの URL を入力します。たとえば、Web アプリに ContactsListMVC という名前を付けた場合は、"http://contactslistmvc.azurewebsites.net" と入力します。

	URL を入力する代わりに、元のドメインをすべて受け入れることを指定するアスタリスク (*) を入力することができます。

13. **[保存]** をクリックします。

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Azure Web アプリで実行されている AngularJS クライアントを表示するブラウザー ウィンドウに戻り、ページを更新するか、**[更新]** ボタンをクリックします。

	ページには、Azure API アプリのファイル システムに格納されている連絡先が表示されます。

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Web API コードのターゲット API アプリに対して CORS を構成する

Azure App Service の CORS サポートを使用しない場合は、ASP.NET Web API コードで CORS を有効にする方法もあります。このプロセスの詳細は「[ASP.NET Web API 2 でクロス オリジン要求を有効にする](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)」に記載されています。ASP.NET Web API を使用してビルドされた API アプリの場合、プロセスはまったく同じですが、以下に簡単にまとめています。

このセクションでは、Azure App Service の CORS サポートを無効にしてから、Web API の CORS サポートを有効にします。Web API のサポートを有効にする前に Azure の CORS サポートを無効にするという手順は省略できないことに注意してください。両方のメソッドを一緒に使用する場合は、Azure の CORS が優先され、Web API の CORS は効果がありません。たとえば、Azure で元のドメインを 1 つ有効にして、Web API コードですべての元のドメインを有効にした場合、Azure API アプリは Azure で指定したドメインからの呼び出しのみを受け付けます。

### Azure の CORS サポートを無効にする

1. Azure の CORS サポートを無効にするには、Azure ポータルに戻って [CORS] ブレードに入力した URL をクリアしてから、**[保存]** をクリックします。
 
3.  AngularJS アプリをデプロイした Web アプリの URL に戻り、ページを更新するか、**[更新]** ボタンをクリックします。

	"連絡先の読み込みエラー" がもう一度表示されます。

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

### Web API の CORS サポートを有効にする

Web API の CORS 機能は、[Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet パッケージによって提供されています。このパッケージはダウンロードしたサンプル アプリケーションで既にインストールされているため、CORS を有効にするために必要なことは一部のコードをコメント解除することのみです。

1. ContactsList.API プロジェクトで、*App\_Start/WebApiConfig.cs* ファイルを開きます。**WebApiConfig** の **Register** メソッドで、コードのconfig.EnableCors 行をコメント解除します。 

	ファイルが更新されると、コードは次の例のようになります。

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // Uncomment the following line to control CORS by using Web API code
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

1. *Controllers/ContactsController.cs* ファイルを開き、`EnableCors` 属性を `ContactsController` クラスに追加する行をコメント解除します。

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	必要に応じて、コント ローラー全体ではなく個々のアクション メソッドに属性を適用できます。

	> **注**: `EnableCors` 属性を持つすべてのパラメーターにワイルドカードを使用しているのはデモンストレーションのみを目的としています。この場合、すべてのオリジンとすべての HTTP 要求に対して API が開かれます。この属性は注意して使用してください。

### API を Azure にデプロイして、フロントエンドを再テストする

8. **ソリューション エクスプローラー**で ContactsList.API プロジェクトを右クリックし、**[発行]** をクリックします。

	これがこのプロジェクトがデプロイされる最後のターゲットであるため、前に作成した API アプリの発行プロファイルの **[プレビュー]** 手順で、**Web の発行**ウィザードが自動的に開きます。

3.  **[発行]** をクリックします。

	![](./media/app-service-api-cors-consume-javascript/pubapi.png)

	デプロイメントが完了した後、ブラウザーに API アプリの URL のウィンドウが開き "Web アプリが正常に作成された" というページが表示されます。

3.  AngularJS アプリをデプロイした Web アプリの URL に戻り、ページを更新するか、**[更新]** ボタンをクリックします。

	ページがもう一度、正常に読み込まれます。

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Azure の CORS サポートを再度有効にする

次のチュートリアルで組み込みの Azure 認証サービスを使用するには、Web API の CORS ではなく Azure の CORS を使用する必要があります。
 
1. Azure の CORS サポートを有効にするには、API アプリで Azure ポータルの [CORS] ブレードに戻り、Web アプリの URL を再入力します。

13. **[保存]** をクリックします。

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

## 次のステップ 

このチュートリアルでは、クライアントの JavaScript コードが、別のドメイン内の API を呼び出すための CORS サポートを有効にする 2 つの方法を説明しました。次のチュートリアルでは、[認証されたユーザーのみがアクセスできるように、API アプリへのアクセスを制限する](app-service-api-dotnet-user-principal-auth.md)方法を説明します。

<!---HONumber=AcomDC_1203_2015-->