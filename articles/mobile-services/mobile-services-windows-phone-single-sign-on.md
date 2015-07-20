<properties 
	pageTitle="Live Connect によるアプリケーションの認証 (Windows Phone) | モバイル デベロッパー センター" 
	description="Windows Phone アプリケーションから、Azure Mobile Services で Live Connect シングル サインオンを使用する方法を示します。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Microsoft アカウントを使用して、クライアントによって管理される認証で Windows Phone アプリを認証します。

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]
##概要
このトピックでは、Windows Phone 8 または Windows Phone 8.1 の Silverlight アプリケーションから、Live SDK を使用して、Microsoft アカウントの認証トークンを取得する方法を示します。このトークンを使用して、Azure Mobile Services でユーザーを認証します。このチュートリアルでは、Live SDK を使用して、既存のプロジェクトに Microsoft アカウント認証を追加します。正常に認証されると、ログインしたユーザーの名前が認識され、ユーザー ID 値が表示されます。

>[AZURE.NOTE]このチュートリアルでは、クライアントによって管理される認証および Live SDK を使用した場合の利点について説明します。これにより、モバイル サービスに既にログオンしているユーザーの認証が容易になります。OneDrive のようなリソースにもアプリがアクセスできるようにするために、追加のスコープを要求することもできます。サービスによって管理される認証では、より汎用的なエクスペリエンスを提供し、複数の認証プロバイダーをサポートします。サービスによって管理される認証の詳細については、トピック「[アプリへの認証の追加](mobile-services-windows-phone-get-started-users.md)」を参照してください。

このチュートリアルには、次のものが必要です。

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 以降のバージョン
+ また、最初にチュートリアル「[既存のアプリケーションへの Mobile Services の追加]」を完了しておく必要があります。

##Microsoft アカウントを使用するためのアプリケーションの登録 

ユーザーを認証できるようにするには、Microsoft アカウント デベロッパー センターでアプリケーションを登録する必要があります。この登録をモバイル サービスと接続する必要があります。Microsoft アカウントの登録を作成し、モバイル サービスに接続するには、次のトピックの手順を完了してください。

+ [Microsoft アカウント ログインを使用するためのアプリケーションの登録](mobile-services-how-to-register-microsoft-authentication.md) 

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

次に、サインインしたユーザーのみがリソース (この場合は *TodoItems* テーブル) にアクセスできるようにリソースへのアクセスを制限する必要があります。

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>アプリケーションに認証を追加する

最後に、Live SDK を追加し、これを使用してアプリケーションでユーザーを認証します。

1. **ソリューション エクスプローラー**でソリューションを右クリックし、[**NuGet パッケージの管理**] を選択します。

2. 左側のウィンドウで、[**オンライン**] カテゴリを選択し、**LiveSDK** を探します。**LiveSDK** パッケージで [**インストール**] をクリックし、すべてのプロジェクトを選択して、使用許諾契約に同意します。

  	Live SDK がソリューションに追加されます。

5. mainpage.xaml.cs プロジェクト ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.Live;      

6. MainPage クラスに、次のコード スニペットを追加します。
	
        private LiveConnectSession session;
        private static string clientId = "<microsoft-account-client-id>";
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            // Create the authentication client using the client ID of the registration.
            LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

            while (session == null)
            {
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    MessageBox.Show(message, title, MessageBoxButton.OK);
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);
                }
            }
        }

    これにより、現在の Live Connect セッションを格納するためのメンバー変数と認証プロセスを処理するためのメソッドが作成されます。LiveLoginResult には、ユーザーを認証する Mobile Services に与えられる認証トークンが含まれています。

7. 上記のコード スニペットで、プレース ホルダー `<microsoft-account-client-id>` は、アプリケーション用の Microsoft アカウント登録から取得したクライアント ID に置き換えます。

5. 既存の **OnNavigatedTo** メソッドのオーバーライドで、**RefreshTodoItems** メソッドの呼び出しをコメントアウトするか、削除します。

	これを行うと、ユーザーが認証されるまでデータが読み込まれなくなります。次に、サインイン プロセスを開始するためのボタンを追加します。

6. MainPage クラスに、次のコード スニペットを追加します。

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = System.Windows.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
7. アプリケーション プロジェクトで、MainPage.xaml プロジェクト ファイルを開き、次の **Button** 要素を、**TitlePanel** の **TextBlock** 要素の後に追加します。

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
		
9. F5 キーを押してアプリケーションを実行し、Microsoft アカウントを使用してサインインします。

   ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

これで、登録された ID プロバイダーのいずれかによって認証されたユーザーは、*TodoItem* テーブルにアクセスできます。ユーザー固有データのセキュリティを強化するには、承認を実装する必要もあります。これを行うには、特定のユーザーのユーザー ID を取得します。この ID を使用することで、特定のリソースに対してユーザーが有する必要のあるアクセス権のレベルを特定できます。

## <a name="next-steps"> </a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。他の ID プロバイダーを認証に使用する方法については、「[モバイル サービスでの認証の使用]」を参照してください。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[既存のアプリケーションへの Mobile Services の追加]: mobile-services-windows-phone-get-started-data.md
[モバイル サービスでの認証の使用]: mobile-services-windows-phone-get-started-users.md
[スクリプトを使用したユーザーの認証]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->