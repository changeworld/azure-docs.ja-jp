<properties
	pageTitle="Azure App Service での API Apps のユーザー認証 |Microsoft Azure"
	description="認証されたユーザーのみがアクセスできるよう Azure App Service で API アプリを保護する方法について説明します。"
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
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Azure App Service の API Apps でのユーザー認証

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## 概要

このチュートリアルでは、Azure App Service の認証および承認機能を使用し、API アプリを保護する方法と、エンド ユーザーが API アプリを使用する方法について説明します。このチュートリアルで取り上げている認証プロバイダーは Azure Active Directory で、サンプル クライアントは、ブラウザーで実行される AngularJS シングル ページ アプリケーションです。

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
これは、Azure App Service で API アプリを使用する方法を示すチュートリアルのシリーズの 3 回目です。このシリーズの詳細については、「[Azure App Service で API Apps と ASP.NET を使ってみる](app-service-api-dotnet-get-started.md)」を参照してください。

## Azure App Service での認証および承認

Azure App Service には、認証および承認用のサービスが組み込まれています。認証は独自のコードで処理することもできますが、App Service には記述および維持するコードを最小限に抑える、設定不要なソリューションがあります。

認証プロバイダーは、App Service でサポートされる、Azure Active Directory、Facebook、Google、Twitter、および Microsoft アカウントの 5 つのいずれも使用できます。また、App Service でサポートされているあらゆる言語で記述された API の保護が可能です。この場合、API にコードをまったく記述しなくても、アクセスに使用するユーザー ログインやトークンを要求することができます。

認証は、Azure App Service で処理され、承認はユーザーのコードで処理されるよう残されます。App Service を構成し、認証されたユーザーのみに API の呼び出しを許可することも、すべてのユーザーに呼び出しを許可することもできます。いずれの場合も、App Service は HTTP ヘッダーでアプリに認証情報を渡します。ユーザーのコードでは、その情報を使用して承認を選択するようにできます。

* .NET API では `Authorize` 属性を使用できます。より詳細に承認するには、簡単に要求に基づいてコードを記述できます。要求の情報は、.NET クラスに設定されます。

* API が他の言語で記述されている場合、App Service が、HTTP 要求の承認ヘッダーに含まれる JWT トークンを渡します。これに加え、最も重要な要求にアクセスしやすいように、Azure ではいくつかの特殊なヘッダーが設定されています (例: `x-ms-client-principal-id`)。

Azure App Service での認証および承認サービスの詳細については、「[App Service 認証/承認の展開](/blog/announcing-app-service-authentication-authorization/)」と「[App Service API Apps - 変更内容](app-service-api-whats-changed.md)」を参照してください。

## ContactsList.Angular.AAD サンプル プロジェクト

このチュートリアルでは、[このシリーズの 1 回目のチュートリアル](app-service-api-dotnet-get-started.md)でダウンロードしたサンプル プロジェクトと、1 回目のチュートリアルで作成した Azure リソース (API アプリと Web アプリ) を使用します。

ContactsList.Angular.AAD プロジェクトは、Azure Active Directory を操作するコードを含む AngularJS クライアントです。このコードは、[Azure-Samples/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) リポジトリにある AAD サンプルに基づいています。

ContactsList.Angular.AAD プロジェクト内のコードは、単純な ContactsLists.Angular プロジェクトとは構成が異なります。API を呼び出すコードは、ContactsList.Angular.AAD プロジェクト内の *app/scripts/contactsSvc.js* ファイルです。

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

ここで、`Get` メソッドは `getItems` とラベル付けされており、コントローラー (*app/scripts/contactsCtrl.js*) では `getItems` は `$scope.populate` に設定されています。

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

ビュー (*app/views/Contacts.html*) で、$scope.populate は初期化時に呼び出されます。

		<div ng-init="populate()">

## Azure での認証および承認の設定

1. [Azure ポータル](https://portal.azure.com/)で、最初のチュートリアルで作成した API アプリの **API App** ブレードに移動し、**[設定]** をクリックします。

2. **[機能]** セクションを探し **[認証/承認]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. **認証/承認**ブレードで、**[オン]** をクリックします。

4. **[要求が認証されていない場合のアクション]** ドロップダウン リストで、**[Azure Active Directory でログイン]** を選択します。

5. **[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. **Azure Active Directory 設定**ブレードで **[Express]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Azure が AAD テナントに AAD アプリケーションを自動的に作成します。新しい AAD アプリケーションのクライアント ID を取得するために Azure クラシック ポータルに後で移動して選択するために、新しい AAD アプリケーションの名前をメモします。

7. **[OK]** をクリックします。

10. **認証/承認**ブレードで、**[保存]** をクリックします。

8. API アプリが保護されたことを確認するには、Swagger UI を使用する最初のチュートリアルで実行したように、API アプリの URL + `/swagger` に移動します。

	今度はログオン ページにリダイレクトされます。

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[Azure Active Directory]** に進みます。

12. [ディレクトリ] タブで AAD テナントをクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. **[アプリケーション]、[自分の会社が所有するアプリケーション]** を順にクリックし、チェック マークをオンにします。

	新しいアプリケーションを確認するには、ページを更新する必要がある場合があります。

15. アプリケーションの一覧から、API アプリの認証を有効にしたときに Azure によって作成されたアプリケーション名をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. **[構成]** をクリックします。

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. ページ下部で **[マニフェストの管理]、[マニフェストのダウンロード]** をクリックし、編集できる場所にファイルを保存します。

16. ダウンロードしたマニフェスト ファイル内で、 `oauth2AllowImplicitFlow` プロパティを検索します。このプロパティの値を `false` から `true` に変更し、ファイルを保存します。

16. **[マニフェストの管理]、[マニフェストのアップロード]** をクリックし、前の手順で更新したファイルをアップロードします。

17. チュートリアルの後の手順で、値をコピーして貼り付けることができるよう、このページは開いたままにします。

## Https 用の Visual Studio プロジェクトの設定

1. **ソリューション エクスプローラー**で ContactList.API プロジェクトをクリックし、次いで **[プロパティ]** ウィンドウで **[SSL が有効]** を **[True]** に変更します。

2. [SSL URL] をコピーします。

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. ContactsList.API プロジェクトを右クリックし、**[プロパティ]** をクリックします。

5. **[Web]** タブをクリックし、**[プロジェクト URL]** フィールドに SSL URL を貼り付け、変更を保存します。

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. ContactsList.Angular.AAD プロジェクトで SSL を有効にするために同じ手順を繰り返します。

2. ContactsList.API プロジェクトと ContactsList.Angular.AAD プロジェクトをスタートアップ プロジェクトとして設定し、ContactsList.API プロジェクトが最初に起動されるように設定します。

## ContactsList.Angular.AAD プロジェクトでのエンドポイント URL と AAD 設定の更新

7. ContactsList.Angular.AAD プロジェクトで、*app/scripts/app.js* ファイルを開きます。

8. `endpoints` 変数を設定するコードの SSL URL が ContactsList.API プロジェクト用に正しいことを確認し、"yourclientid" の両方のインスタンスを AAD アプリケーションのクラシック ポータルの **[構成]**タブの AAD アプリケーションのクライアント ID と置き換えます。エンドポイント URL の末尾にスラッシュがあることを確認します。

	コードは、次の例のようになります。

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. また、*app.js* の `adalProvider.init` コードの "{your tenant url}" と "{your client id}" を実際の値に置き換えます。

	コードは、次の例のようになります。

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

10. *app/scripts/contactsSvc.js* の apiEndpoint が ContactsList.API プロジェクトの SSL URL に正しく設定されていることを確認します。

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## ContactsList.API プロジェクトでの AAD 設定の更新

1. ContactsList.API プロジェクトでアプリケーションの *Web.config* ファイルを開きます。

2. 次の例のように、appSettings 要素の ida:Authority を値 "https://login.windows.net/{your tenant url}" に設定し、ida:ClientId を AAD アプリケーションのクライアント ID に設定します。

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## localhost での AAD アプリケーションの構成 

1. クラシック ポータルの AAD アプリケーションの **[構成]** タブの **[サインオン URL]** フィールドに ContactsList.Angular.AAD プロジェクトの SSL URL を貼り付け、末尾のスラッシュを削除します。

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. **[構成]** タブの下方部の **[応答 URL]** フィールドの既にある値を、末尾のスラッシュはそのままに残し、ContactsList.Angular.AAD プロジェクトの SSL URL を貼り付けて置換します。

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. **[保存]** をクリックします。

## ローカルでの API およびクライアント プロジェクトの実行

HTTPS をローカルで実行すると、localhost の IIS Express SSL 証明書に関する警告メッセージが表示されます。ローカルで実行する場合、これらのメッセージはクリックして閉じることができます。必要に応じて、このセクションは省いて、Azure でアプリケーションと API を実行する準備を行う次のセクションに直接進むことができます。

ログオンで問題がある場合、別のブラウザー、または Incognito または InPrivate ウィンドウで AngularJS プロジェクトの URL を開くことを試してください (例: `https://localhost:44301`)。

5. Visual Studio で F5 キーを押して、ローカルで API および AngularJS プロジェクトを実行します。

	AngularJS アプリケーションのホーム タブが表示されます。

10. **[ログイン]** タブをクリックします。

	ログインを求められます。

7. AAD テナント内のユーザーの資格証明を使用してログインします。

10. **[連絡先]** タブをクリックします。

	**[連絡先]** ページが表示されます。

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. ブラウザー ウィンドウを閉じます。

ContactsList.Angular.AAD プロジェクトのコードでは、API を呼び出し、連絡先を表示する前に、ユーザーがログオンしている必要があります。ただし、認証されていないユーザーが、API を呼び出すことを阻止するものはありません。これは ContactsList.API プロジェクトの SSL URL が開いているブラウザー ウィンドウで、Swagger UI を実行して確認できます。API は、Azure App Service で実行される場合にのみ、認証されていないユーザーからの呼び出しから保護されます。

次のセクションでは、Azure App Service で API とクライアントを実行するためのプロジェクトと AAD を構成し、プロジェクトを Azure にデプロイし、それらを Azure でテストします。

## Azure API アプリを呼び出す ContactsList.Angular.AAD プロジェクトの構成

1. ContactsList.Angular.AAD プロジェクトで、*app/scripts/app.js* ファイルを開きます。

2. localhost エンドポイントをコメント アウトし、Azure のエンドポイントをコメント解除し、"{your api app name}" を API アプリの名前に置き換えます。

	コードは、現在次の例のようです。

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. *app/scripts/contactsSvc.js* ファイルで同じエンドポイント URL の変更を行います。

	コードは、現在次の例のようです。

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## Azure Web アプリの AAD アプリケーションの構成

1. クラシック ポータルの AAD アプリケーションの **[構成]** タブの **[サインオン URL]** フィールドで ContactsList.Angular.AAD プロジェクトの SSL URL を削除し、末尾のスラッシュなしで Web アプリのベース URL に置き換えます。(これは、API アプリの URL ではなく、Web アプリの URL であることに注意してください。)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. **[応答 URL]** フィールドの ContactsList.Angular.AAD プロジェクトの SSL URL を、末尾のスラッシュはそのままにして Web アプリのベース URL に置き換えます。

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. **[保存]** をクリックします。

## Azure への ContactsList.API プロジェクトのデプロイ

8. **ソリューション エクスプローラー**で ContactsList.API プロジェクトを右クリックし、**[発行]** をクリックします。

	**Web の発行**ウィザードで、前に作成した API アプリをデプロイするための、このプロジェクトの最後で使用した発行プロファイルが開きます。

7. **[発行]** をクリックします。

8. 自動的に開くブラウザー ウィンドウを閉じます。

## Azure への ContactsList.Angular.AAD プロジェクトのデプロイ

8. **ソリューション エクスプローラー**で ContactsList.Angular.API プロジェクトを右クリックし、**[発行]** をクリックします。

9. **[Microsoft Azure App Service]** をクリックします。

10. **[App Service]** ダイアログ ボックスの **[サブスクリプション]** ドロップダウン リストからサブスクリプションを選択します。

11. このチュートリアルで作成したリソース グループを展開し、2 回目のチュートリアルで作成した Web アプリを選択します。

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. **[OK]** をクリックします。

12. **Web の発行**ウィザードで **[接続]** タブをクリックし、**[宛先 URL]** ボックスで `http://` を `https://` に変更します。

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. **Web の発行**ウィザードで **[接続]** タブをクリックし、**[ファイル発行オプション]** を展開し、**[ターゲットの追加ファイルの削除]** チェック ボックスを選択します。

7. **[発行]** をクリックします。

	Visual Studio によってプロジェクトがデプロイされ、アプリのホーム ページがブラウザーで開きます。

## Azure での AngularJS Web アプリのテスト

8. **[連絡先]** タブをクリックします。

	ログインを求められます。

9. AAD テナントのユーザー資格情報でログインします。

10. **[連絡先]** ページが表示されます。

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. InPrivate または Incognito ブラウザー ウィンドウで Swagger UI URL に移動し、API アプリが保護されていることを確認します。

	ログオン ページにリダイレクトされます。

	これでフロントエンドは認証ユーザーに代わって API を呼び出すことができ、認証されていないユーザーは、API を呼び出すことはできません。

## 次のステップ

このチュートリアルでは、App Service の認証および承認を使用して、認証されたユーザーのみが呼び出すことができるよう、API アプリへのアクセスを制限しました。このシリーズの次のチュートリアルでは、[サービス間シナリオで API アプリへのアクセスを制限する](app-service-api-dotnet-service-principal-auth.md)方法を学習します。

<!---HONumber=AcomDC_1203_2015-->