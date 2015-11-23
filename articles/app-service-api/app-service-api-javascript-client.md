<properties 
	pageTitle="HTML および JavaScript を使用した Azure API アプリへのアクセス" 
	description="HTML および JavaScript を使用して、API アプリのバックエンドにアクセスする方法について説明します。" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="10/30/2015" 
	ms.author="bradygaster"/>

# HTML および JavaScript を使用した Azure API アプリの実行

## 概要

この記事では、[Azure App Service](/documentation/services/app-service/) で [API アプリ](app-service-api-apps-why-best-platform.md)の HTML および JavaScript クライアントを作成する方法を示しています。この記事は HTML や JavaScript の実践的な知識があることを前提としており、API アプリへの REST 呼び出しを実行するために [AngularJS](https://angularjs.org/) JavaScript フレームワークを使用しています。

事前に次のいくつかの記事に目を通してから、この記事を読み進めてください。

1. [API アプリの作成](app-service-dotnet-create-api-app.md)に関するページでは、API アプリ プロジェクトを作成します。
2. [API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関するページでは、API アプリを Azure サブスクリプションにデプロイします。
3. [API アプリのデバッグ](../app-service-dotnet-remotely-debug-api-app.md)に関するページでは、Azure でコードを実行中に、Visual Studio を使用してそのコードをリモートでデバッグします。

この記事は、このような以前の記事に基づいて作成されており、HTML アプリケーションがどのように JavaScript を使用してバックエンド API アプリにアクセスするかを説明します。

## CORS の有効化

通常、CORS (クロス オリジン リソース共有) は、API 自体ではなく別のホストから提供される HTML アプリケーションで必要となります。API アプリには、CORS を有効にするためのオプションが 2 つ以上あります。このセクションでは、これらのオプションの概要を説明します。

### API アプリ ゲートウェイの CORS の有効化

API アプリ ゲートウェイは、Azure プレビュー ポータルを使用して、CORS を有効にするように構成できます。**MS_CrossDomainOrigins** *appSetting* を追加することで、どの URL に API アプリの呼び出しを許可するかを指定できます。このセクションでは、この *appSetting* を使用して、API ゲートウェイ レベルで CORS を有効にする方法を説明します。

1. Azure プレビュー ポータルで、CORS を有効にする API アプリのブレードに移動します。移動後、API アプリの *[ゲートウェイ]* アイコンをクリックします。 

	![API アプリの [ゲートウェイ] をクリック](./media/app-service-api-javascript-client/19-api-app-blade.png)

1. ポータルのブレードで **[ゲートウェイ ホスト]** リンクをクリックします。

	![API アプリの [ゲートウェイ ホスト] リンクをクリック](./media/app-service-api-javascript-client/20-gateway-host-blade.png)

1. ポータルのブレードで **[すべての設定]** リンクをクリックします。

	![ゲートウェイの [すべての設定] リンク](./media/app-service-api-javascript-client/21-gateway-blade-all-settings.png)

1. ポータルのブレードで **[アプリケーション設定]** をクリックします。

	![ゲートウェイの [アプリケーション設定]](./media/app-service-api-javascript-client/22-gateway-app-settings-blade.png)

1. **MS_CrossDomainOrigins** アプリケーション設定を追加します。設定の値は、API アプリへのアクセスを提供する HTTP ホストのコンマ区切り一覧にします。複数のホストへのアクセスを提供する場合は、*appSetting* の値を次のコードのように設定することができます。

		http://foo.azurewebsites.net, https://foo.azurewebsites.net, http://contactlistwebapp.azurewebsites.net

	次のセクションでは、簡単な HTML ページを含む個別の Web アプリケーションを作成するプロセスについて説明します。この HTML ページでは、API アプリと同じ Azure リソース グループで実行されている Web アプリから API アプリを呼び出します。これが API アプリへのアクセスが許可される唯一のホストであるため、設定は 1 つのホストが含まれるように行われます。

		http://contactlistwebapp.azurewebsites.net

	次のスクリーン ショットでは、この設定を Azure プレビュー ポータルで保存したときにどのように表示されるかを示しています。

	![](./media/app-service-api-javascript-client/23-app-settings-set.png)

**MS_CrossDomainOrigins** アプリケーション設定については、[Azure Mobile Service .NET の更新](http://azure.microsoft.com/blog/2014/07/28/azure-mobile-services-net-updates/)に関するブログ記事で詳しく説明されているため、設定値の詳細については、この記事を参照してください。

### Web API コードでの CORS の有効化

Web API で CORS を有効にするプロセスについては、[ASP.NET Web API 2 でのクロスオリジン要求の有効化](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)に関する ASP.NET の記事で詳しく説明されています。ASP.NET Web API を使用してビルドされた API アプリの場合、プロセスはまったく同じですが、以下に簡単にまとめています。API アプリは既に正しく設定されているため、CORS が有効になっている場合は、このセクションをスキップしてください。

1. CORS 機能は、[Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) NuGet パッケージによって提供されています。これをインストールするには、**Package Manager Console** を開いて、次の PowerShell スクリプトを実行します。 

		Install-Package Microsoft.AspNet.WebApi.Cors

1. コマンドの実行後、Package Manager Console と **packages.config** には、新しい NuGet パッケージの追加が反映されます。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/01-cors-installed.png)

1. *App_Start/WebApiConfig.cs* ファイルを開きます。ファイルで **WebApiConfig** クラスの **Register** メソッドに次のコード行を追加します。

		config.EnableCors();

	ファイルが更新されると、コードは次のようになります。

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
				config.EnableCors(); /* -- NEW CODE -- */
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. CORS を有効にする最後の手順として、有効にする個々のアクション メソッドを区別します。次のコードに示すように、メソッドごとまたはコントローラー全体に **EnableCors** 属性を追加します。

	> **注**: EnableCors 属性を持つすべてのパラメーターにワイルドカードを使用しているのはデモンストレーションのみを目的としています。この場合、すべてのオリジンとすべての HTTP 要求に対して API が開かれます。この属性は、影響を理解したうえで慎重に使用してください。

		using ContactList.Models;
		using System.Collections.Generic;
		using System.Web.Http;
		using System.Web.Http.Cors;
	
		namespace ContactList.Controllers
		{
		    [EnableCors(origins:"*", headers:"*", methods: "*")] /* -- NEW CODE -- */
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]
		            {
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		
		        [HttpPost]
		        public Contact Post([FromBody] Contact contact)
		        {
		            return contact;
		        }
		    }
		}
1. CORS のサポートを追加する前に、既に API アプリを Azure にデプロイしている場合は、Azure でホストされる API で CORS が有効になるようにコードをもう一度デプロイします。 

## API アプリを使用する Web アプリの作成

このセクションでは、新しい空の Web アプリケーションを作成して、AngularJS をインストールして使用し、単純な HTML フロントエンドを API アプリにバインドします。使用中の Web アプリを Azure App Service にデプロイします。HTML Web アプリは、API アプリから取得したデータにバインドされ、そのデータを表示し、Contacts API 用の単純な UI をユーザーに提供します。

1. 前の [API アプリの作成](app-service-dotnet-create-api-app.md)手順で作成したソリューションを右クリックし、**[追加]、[新しいプロジェクト]** の順に選択します。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/02-add-project.png)

1. **[ASP.NET Web アプリケーション]** テンプレートを選択します。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/03-new-web-project.png)

1. 1 つの ASP.NET ダイアログ ボックスから **[空]** テンプレートを選択します。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/04-empty-web.png)

1. **パッケージ マネージャー**を使用するか、**[NuGet パッケージの管理]** コンテキスト メニュー項目を使用して、[AngularJS](https://www.nuget.org/packages/angularjs) NuGet パッケージをインストールします。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/05-install-angular.png)

1. **パッケージ マネージャー**を使用するか、**[NuGet パッケージの管理]** コンテキスト メニュー項目を使用して、[Bootstrap](https://www.nuget.org/packages/bootstrap) NuGet パッケージをインストールします。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/05-install-bootstrap.png)

1. Web アプリ プロジェクトに新しい HTML ファイルを追加します。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/06-new-html-file.png)

1. ファイルに *index.html* と名前を付けます。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/07-index-html.png)

1. ブートストラップ CSS ファイルと AngularJS JavaScript ファイルを HTML ページに追加するほか、単純なブートストラップ テンプレート ([例を参照](http://getbootstrap.com/examples/starter-template/)) を使用して空のスクリプト タグを作成し、ページを準備します。
	
	> 注: 次の HTML および JavaScript コード内のコメントは、このセクションの後続のステップの前置きです。

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Contacts HTML Client</title>
		    <link href="Content/bootstrap.css" rel="stylesheet" />
		    <style type="text/css">
		        body {
		            margin-top: 60px;
		        }
		    </style>
		</head>
		<body>
		
		    <nav class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
		                    <span class="sr-only">Toggle navigation</span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                <a class="navbar-brand" href="#">Contacts</a>
		            </div>
		            <div id="navbar" class="collapse navbar-collapse">
		                <ul class="nav navbar-nav"></ul>
		            </div>
		        </div>
		    </nav>
		
		    <div class="container">
		        <!-- contacts ui here -->
		    </div>
		
		    <script src="Scripts/angular.js" type="text/javascript"></script>
		    <script type="text/javascript">
		        /* client javascript code here */
		    </script>
		
		</body>
		</html>

1. 次に示す HTML テーブル コードを HTML 内の **container** *div* 要素に追加します。

		<!-- contacts ui here -->
        <table class="table table-striped" ng-app="myApp" ng-controller="contactListCtrl">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="con in contacts">
                    <td>[[con.Id]]</td>
                    <td>[[con.Name</td>
                    <td>[[con.EmailAddress]]</td>
                    <td></td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th>Create a new Contact</th>
                    <th colspan="2">API Status: [[status]]</th>
                    <th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>
                </tr>
                <tr>
                    <td><input type="text" placeholder="ID" ng-model="newId" /></td>
                    <td><input type="text" placeholder="Name" ng-model="newName" /></td>
                    <td><input type="text" placeholder="Email Address" ng-model="newEmail" /></td>
                    <td><button class="btn btn-sm btn-success" ng-click="create()">Create</button></td>
                </tr>
            </tfoot>
        </table>

1. `tbody` 要素と `tfoot` 要素にある [ を { に、] を } にすべて置き換えます (現在、このサイトでは、コード ブロック内で二重の中かっこを使用した式を表示できません)。

2. *index.html* ファイルを右クリックし、**[スタート ページに設定]** をクリックします。

3. *index.html* ファイルを右クリックし、**[ブラウザーで表示]** をクリックします。

	HTML 出力のテンプレートのハンドルバーを確認します。次の手順では、AngularJS を使用してそれらの HTML 要素をデータバインドします。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/09-template-ui.png)

1. 次の JavaScript コードを *index.html* ファイルに追加し、API を呼び出して、HTML UI を API 出力にデータバインドします。

		/* client javascript code here */
        angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
            $scope.baseUrl = 'http://localhost:1578';

            $scope.refresh = function () {
                $scope.status = "Refreshing Contacts...";
                $http({
                    method: 'GET',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }).success(function (data) {
                    $scope.contacts = data;
                    $scope.status = "Contacts loaded";
                }).error(function (data, status) {
                    $scope.status = "Error loading contacts";
                });
            };

            $scope.create = function () {
                $scope.status = "Creating a new contact";

                $http({
                    method: 'POST',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        'Id': $scope.newId,
                        'Name': $scope.newName,
                        'EmailAddress': $scope.newEmail
                    }
                }).success(function (data) {
                    $scope.status = "Contact created";
                    $scope.refresh();
                    $scope.newId = 0;
                    $scope.newName = '';
                    $scope.newEmail = '';
                });
            };

            $scope.refresh();
        });

1. Index.html に追加したコードのベース URL (`http://localhost:1578`) のポート番号を、API プロジェクトの実際のポート番号に置き換えます。

>[AZURE.NOTE]**注意** HTML クライアント プロジェクトのポート番号を使用しないでください。API プロジェクトを右クリックし、**[デバッグ]、[新しいインスタンスを開始]** の順にクリックして、ポート番号を示すブラウザー ウィンドウを表示できます。

1. HTML クライアントを実行したときに API アプリ プロジェクトも実行されていることを確認します。実行されていない場合は、JavaScript HTML が正しく機能しません。ソリューションを右クリックして、**[プロパティ]** を選択します。その後、両方の Web プロジェクトを **[デバッグなしで開始]** に設定します。最初に API プロジェクトが実行されます。 

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/10-run-both-web-projects.png)

1. ソリューションを実行します。HTML/JavaScript クライアントが API アプリ プロジェクトに接続し、そのデータを表示します。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/11-web-client-running.png)

## Web アプリのデプロイ

このセクションでは、HTML/JavaScript クライアントを App Service Web アプリとしてデプロイします。デプロイが完了したら、JavaScript で使用されている URL を、デプロイされた API アプリを使用するように変更します。

> 注: このセクションでは、[API アプリのデプロイ](app-service-dotnet-deploy-api-app.md)に関する記事を読んで完了しているか、既に独自の API アプリをデプロイした経験があることを前提としています。

1. Azure プレビュー ポータルで、API アプリのブレードを開きます。ブレードで URL をクリックしてブラウザーで開きます。開いたら、ブラウザーのアドレス バーから、API アプリの URL をコピーします。 

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/12-open-api-app-from-blade.png)

1. JavaScript コードの **$scope.baseUrl** プロパティで、API アプリの URL を貼り付けて以前の値を上書きします。

		$scope.baseUrl = 'https://microsoft-apiappf7e042ba8e5233ab4312021d2aae5d86.azurewebsites.net';

	URL に HTTPS が指定されていることに注意してください。HTTPS の使用は任意ではありません。API Apps では、HTTP はサポートされません。

1. HTML/JavaScript Web プロジェクトを右クリックして、**[発行]** コンテキスト メニュー項目を選択します。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/13-publish-web-app.png)

1. [Web の発行] ダイアログ ボックスで、**[Microsoft Azure Web アプリ]** オプションを選択します。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/14-publish-web-dialog.png)

1. **[新規作成]** をクリックして、新しい Web アプリを作成します。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/15-new-web-app.png)

1. API アプリが既に実行されているのと同じアプリ ホスティング プランとリソース グループを選択します。

	> **注**: これは必須ではありませんが、デモンストレーションを目的として、すべてが 1 つのリソース グループに含まれていると、後でより簡単に Azure リソースをクリーンアップできます。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/16-new-web-app-creation-dialog.png)

1. Web の発行手順を完了し、HTML/JavaScript クライアントを App Service Web Apps にデプロイします。
1. Web アプリは、デプロイされると、Web ブラウザーで自動的に開かれ、API アプリのデータが表示されます。 

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/17-web-app-running-in-ie.png)

1. この時点で、リソース グループを参照すると、API アプリと共に実行されている新しい Web アプリが表示されます。

	![ソリューション エクスプローラーの apiapp.json と Metadata](./media/app-service-api-javascript-client/18-web-app-visible-in-resource-group.png)

## 次のステップ 

この例では、API アプリのバックエンドにアクセスするために、AngularJS を JavaScript プラットフォームとしてどのように使用できるかについて説明しました。REST アクセス機能を変更して、その他の JavaScript フレームワークを使用することもできます。

この例は、API アプリへの認証されていないアクセスを示します。App Service での認証については、[API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md)に関するページを参照してください。

<!---HONumber=Nov15_HO3-->