<properties 
	pageTitle="Xamarin Android でのモバイル アプリの認証の使用"
	description="モバイル アプリを使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin Android アプリのユーザーを認証する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="mahender"/>

# Xamarin.Android アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

このトピックでは、クライアント アプリケーションから Mobile App のユーザーを認証する方法について説明します。このチュートリアルでは、Azure Mobile Apps でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。Mobile App によって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル アプリのクイック スタートに基づいています。また、先に [Xamarin.Android アプリの作成]に関するチュートリアルを完了している必要があります。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

##<a name="create-gateway"></a>App Service ゲートウェイを作成する

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>アプリケーションを認証に登録し、App Services を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="4">
<li><p>Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターを使用してクライアント プロジェクトを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
   
   	<p>これは、認証されないユーザーとして Mobile App バックエンドにアプリがアクセスしようとするために生じます。<em>TodoItem</em> テーブルは今すぐ認証が必要です。</p></li>
</ol>

次に、認証されたユーザーで Mobile App のバックエンドからリソースを要求するように、クライアント アプリを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

1. **TodoActivity** クラスに次のプロパティを追加します。

			private MobileServiceUser user;

2. **TodoActivity** クラスに次のメソッドを追加します。

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    これにより、ユーザーを認証する新しいメソッドが作成されます。上記のサンプル コードでは、ユーザーは Facebook ログインを使用して認証されます。認証されると、ダイアログを使用してユーザー ID が表示されます。

    > [AZURE.NOTE]Facebook 以外の ID プロバイダーを使用している場合は、上記の例の **LoginAsync** に渡される値を _MicrosoftAccount_、_Twitter_、_Google_、_WindowsAzureActiveDirectory_ のいずれかに変更します。

3. **OnCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL, Gateway URL and key
		client = new MobileServiceClient (applicationURL, gatewayURL, applicationKey);
		
		await Authenticate(); // Added for authentication

	この呼び出しは、認証プロセスを開始し、認証プロセスを非同期に待機します。


4. Visual Studio または Xamarin Studio で、デバイスまたはエミュレーターでクライアント プロジェクトを実行し、選択した ID プロバイダーを使用してサインインします。

   	ログインに成功すると、アプリケーションにログイン ID と todo 項目の一覧が表示され、データを更新することができます。


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Xamarin.Android アプリの作成]: app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=August15_HO9-->