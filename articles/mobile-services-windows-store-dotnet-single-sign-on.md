<properties 
	pageTitle="Live Connect による Windows ストア アプリの認証" 
	description="Windows ストア アプリケーションから、Azure Mobile Services で Live Connect シングル サインオンを使用する方法を示します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="glenga"/>

# Microsoft アカウントを使用して、クライアントによって管理される認証で Windows ストア アプリを認証します。

[AZURE.INCLUDE [mobile-services-selector-single-signon](../includes/mobile-services-selector-single-signon.md)] 

##概要
このトピックでは、ユニバーサル Windows アプリから Live SDK を使用して Microsoft アカウントの認証トークンを取得する方法を示します。このトークンを使用して、Azure Mobile Services でユーザーを認証します。このチュートリアルでは、Live SDK を使用して、既存のプロジェクトに Microsoft アカウント認証を追加します。正常に認証されると、ログインしたユーザーの名前が認識され、ユーザー ID 値が表示されます。

>[AZURE.NOTE]このチュートリアルでは、クライアント主導型認証および Live SDK を使用した場合の利点について説明します。これにより、モバイル サービスに既にログオンしているユーザーの認証が容易になります。OneDrive のようなリソースにもアプリがアクセスできるようにするために、追加のスコープを要求することもできます。サービス主導型認証では、より汎用的なエクスペリエンスを提供し、複数の認証プロバイダーをサポートします。サービス主導型認証の詳細については、トピック「[アプリへの認証の追加](mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md)」を参照してください。

このチュートリアルには、次のものが必要です。

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 以降のバージョン。
+ チュートリアル「[Mobile Services の使用](mobile-services-javascript-backend-windows-store-dotnet-get-started.md)」または「[既存のアプリケーションへのMobile Services の追加]」のいずれかを完了しておくことも必要です。

##認証で Microsoft アカウントを使用するためのアプリケーションの登録

ユーザーを認証できるようにするには、Microsoft アカウント デベロッパー センターでアプリケーションを登録する必要があります。この登録をモバイル サービスと接続する必要があります。Microsoft アカウントの登録を作成し、モバイル サービスに接続するには、次のトピックの手順を完了してください。

+ [Microsoft アカウント ログインを使用するためのアプリケーションの登録](mobile-services-how-to-register-microsoft-authentication.md)

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

次に、サインインしたユーザーのみがリソース (この場合は *TodoItems* テーブル) にアクセスできるようにリソースへのアクセスを制限する必要があります。

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../includes/mobile-services-restrict-permissions-windows.md)] 

##<a name="add-authentication"></a>アプリケーションに認証を追加する

最後に、Live SDK を追加し、これを使用してアプリケーションでユーザーを認証します。

1. **ソリューション エクスプローラー**でソリューションを右クリックし、[**NuGet パッケージの管理**] を選択します。

2. 左側のウィンドウで、[**オンライン**] カテゴリを選択し、**LiveSDK** を探します。**LiveSDK** パッケージで [**インストール**] をクリックし、すべてのプロジェクトを選択して、使用許諾契約に同意します。

  	Live SDK がソリューションに追加されます。

3. 共有プロジェクト ファイル MainPage.xaml.cs を開き、次の using ステートメントを追加します。

        using Microsoft.Live;        

4. **MainPage** クラスに、次のコード スニペットを追加します。
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {

            // Get the URL the mobile service.
            var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

            // Create the authentication client using the mobile service URL.
            LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);

            while (session == null)
            {
                // Request the authentication token from the Live authentication service.
				// The wl.basic scope is requested.
                LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;

                    // Get information about the logged-in user.
                    LiveConnectClient client = new LiveConnectClient(session);
                    LiveOperationResult meResult = await client.GetAsync("me");

                    // Use the Microsoft account auth token to sign in to Mobile Services.
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    // Display a personalized sign-in greeting.
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
        }
    
    これにより、現在の Live Connect セッションを格納するためのメンバー変数と認証プロセスを処理するためのメソッドが作成されます。

	>[AZURE.NOTE]サービスに新しいトークンを要求する前に、キャッシュされた Live 認証トークンまたは Mobile Services 認証トークンの使用を試みる必要があります。そうしないと、多くの顧客が同時にアプリケーションを開始した場合に、使用率に関連した問題が発生することがあります。このトークンのキャッシュ方法の詳細については、「[モバイル サービスでの認証の使用](mobile-services-windows-store-dotnet-get-started-users.md#tokens)」を参照してください。

5. 既存の **OnNavigatedTo** メソッドのオーバーライドで、**RefreshTodoItems** メソッドの呼び出しをコメントアウトするか、削除します。

	これを行うと、ユーザーが認証されるまでデータが読み込まれなくなります。次に、サインイン プロセスを開始するためのボタンを追加します。

6. MainPage クラスに、次のコード スニペットを追加します。

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
7. Windows ストア アプリ プロジェクトで、MainPage.xaml プロジェクト ファイルを開き、**[保存]** ボタンを定義する要素の直前に次の **Button** 要素を追加します。

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

8. Windows Phone ストア アプリの前の手順を繰り返しますが、今回は、**TextBlock** 要素の後の **TitlePanel** に**ボタン**を追加します。
		
9. F5 キーを押してアプリケーションを実行し、Microsoft アカウントを使用して Live Connect にサインインします。

	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

10. Windows Phone ストア アプリ プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックしてから、Windows Phone ストア アプリも正常に実行していることを確認する前の手順を繰り返します。

これで、登録された ID プロバイダーのいずれかによって認証されたユーザーは、*TodoItem* テーブルにアクセスできます。ユーザー固有データのセキュリティを強化するには、承認を実装する必要もあります。これを行うには、特定のユーザーのユーザー ID を取得します。この ID を使用することで、特定のリソースに対してユーザーが有する必要のあるアクセス権のレベルを特定できます。

## <a name="next-steps"> </a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。他の ID プロバイダーを認証に使用する方法については、「[モバイル サービスでの認証の使用]」を参照してください。.NET でモバイル サービスを使用する方法の詳細については、「[モバイル サービス .NET の使用方法の概念リファレンス]」を参照してください。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[既存のアプリケーションへのMobile Services の追加]: mobile-services-windows-store-dotnet-get-started-data.md
[モバイル サービスでの認証の使用]: mobile-services-windows-store-dotnet-get-started-users.md
[スクリプトを使用したユーザーの認証]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253

<!--HONumber=54-->