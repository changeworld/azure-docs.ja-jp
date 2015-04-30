<properties 
	pageTitle="Xamarin iOS でのモバイル アプリの認証の使用" 
	description="モバイル アプリを使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin iOS アプリのユーザーを認証する方法について説明します。" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Xamarin.iOS アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル アプリによって正常に認証され、承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル アプリのクイック スタートに基づいています。先に、[Xamarin.iOS アプリの作成]に関するチュートリアルを完了している必要があります。 

## <a name="register"></a>アプリケーションを認証に登録し、App Services を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>Visual Studio または Xamarin Studio で、デバイスまたはシミュレーターを使用してクライアント プロジェクトを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。</p>
   
   	<p>この問題は、認証されないユーザーとしてアプリケーションがモバイル アプリ コードにアクセスしようとしても、 <em>TodoItem</em> テーブルでは認証が要求されるために発生します。</p></li>
</ol>

次に、App Service のリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

ここでは、データを表示する前にログイン画面を表示するようにアプリケーションを変更します。アプリケーションが起動したときには、App Service には接続されず、データも表示されません。ユーザーが最初に更新操作を実行した後で、ログイン画面が表示されます。ログインに成功すると、Todo 項目の一覧が表示されます。

1. クライアント プロジェクトで、ファイル **QSTodoService.cs** を開き、QSTodoService に次の宣言を追加します。

		// Logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. 次の定義を使用して、**QSTodoService** に新しいメソッド **Authenticate** を追加します。

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

> [AZURE.NOTE] Facebook 以外の ID プロバイダーを使用している場合は、上の **LoginAsync** メソッドに渡される値を、_MicrosoftAccount_、_Twitter_、_Google_、_WindowsAzureActiveDirectory_ のいずれかに変更します。

3. **QSTodoListViewController.cs** を開きます。**ViewDidLoad** のメソッド定義を変更して、終わり近くにある **RefreshAsync()** の呼び出しを削除します。

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();
		}


4. メソッド **RefreshAsync** を変更して、**User** プロパティが null の場合にはログイン画面を表示して認証を行うようにします。メソッド定義の先頭に次のコードを追加します。

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. **[実行]** ボタンを押してプロジェクトをビルドし、iPhone シミュレーターでアプリケーションを開始します。アプリケーションにデータが表示されないことを確認します。 

	項目の一覧をプルダウンして更新操作を実行すると、ログイン画面が表示されます。有効な資格情報を正しく入力すると、Todo 項目の一覧が表示され、データを更新できるようになります。

 
<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin.iOS アプリの作成]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[Azure 管理ポータル]: https://portal.azure.com

<!--HONumber=49-->