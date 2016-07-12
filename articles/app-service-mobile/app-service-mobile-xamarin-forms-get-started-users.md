<properties
	pageTitle="Xamarin.Forms アプリでの Mobile Apps の認証の使用 | Microsoft Azure"
	description="Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin Forms アプリのユーザーを認証する方法について説明します。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="wesmc"/>

# Xamarin.Forms アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##概要

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、Xamarin.Forms クイック スタート プロジェクトに認証を追加します。モバイル アプリによって正常に認証、承認されると、ユーザー ID 値が表示され、制限付きのテーブル データにアクセスできます。

##前提条件

このチュートリアルで最善の結果が得られるように、最初にチュートリアル「[Xamarin.Forms アプリの作成](app-service-mobile-xamarin-forms-get-started.md)」を完了しておくことをお勧めします。このチュートリアルを完了すると、マルチプラットフォーム TodoList アプリである Xamarin.Forms プロジェクトを作成できます。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

##アプリケーションを認証に登録し、App Services を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##ポータブル クラス ライブラリに認証を追加する

Mobile Apps では、App Service 認証を使用したユーザーのサインインに、[MobileServiceClient] の [LoginAsync] 拡張メソッドを使います。このサンプルでは、アプリにプロバイダーのサインイン インターフェイスが表示される、サーバー側管理認証フローを使用します。詳細については、「[サーバー側管理認証](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow)」を参照してください。運用アプリのユーザー エクスペリエンスを向上させるためには、代わりに[クライアント側管理認証](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow)を使用することを検討してください。

Xamarin.Forms プロジェクトで認証するには、アプリのポータブル クラス ライブラリに **IAuthenticate** インターフェイスを定義します。また、ポータブル クラス ライブラリで定義したユーザー インターフェイスを更新して、認証を開始するためにユーザーがクリックする **[サインイン]** ボタンを追加します。認証が成功すると、モバイル アプリ バックエンドからデータが読み込まれます。

アプリでサポートされているプラットフォームごとに **IAuthenticate** インターフェイスを実装する必要があります。


1. Visual Studio または Xamarin Studio で、ポータブル クラス ライブラリ プロジェクトである **Portable** というプロジェクトから App.cs を開きます。その後で、次の `using` ステートメントを追加します。

		using System.Threading.Tasks;

2. App.cs に、次の `IAuthenticate` インターフェイス定義を、`App` クラス定義の直前に追加します。

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    }

3. インターフェイスをプラットフォーム固有の実装で初期化するように、次の静的メンバーを **App** クラスに追加します。

	    public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. ポータブル クラス ライブラリ プロジェクトから TodoList.xaml を開き、既存のボタンの後ろに、*buttonsPanel* レイアウト要素の次の **Button** 要素を追加します。

      	<Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
			Clicked="loginButton_Clicked"/>

	このボタンは、モバイル アプリ バックエンドによるサーバー側管理認証をトリガーします。

5. ポータブル クラス ライブラリ プロジェクトから TodoList.xaml.cs を開き、`TodoList` クラスに次のフィールドを追加します。

		// Track whether the user has authenticated. 
        bool authenticated = false;


6. **OnAppearing** メソッドを次のコードに置き換えます。

	    protected override async void OnAppearing()
	    {
	        base.OnAppearing();
	
	        // Refresh items only when authenticated.
	        if (authenticated == true)
	        {
	            // Set syncItems to true in order to synchronize the data 
	            // on startup when running in offline mode.
	            await RefreshItems(true, syncItems: false);

				// Hide the Sign-in button.
                this.loginButton.IsVisible = false;
	        }
	    }

	これにより、ユーザー認証の完了後にのみ、データがサービスによって更新されるようになります。

7. **Clicked** イベントの次のハンドラーを **TodoList** クラスに追加します。

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. 変更を保存し、ポータブル クラス ライブラリ プロジェクトをリビルドしてエラーがないことを確認します。


##Android アプリに認証を追加する

このセクションでは、Android アプリ プロジェクト内に **IAuthenticate** インターフェイスを実装する方法について説明します。Android デバイスをサポートしない場合、このセクションはスキップしてください。

1. Visual Studio または Xamarin Studio で、**droid** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. F5 キーを押してデバッガーでプロジェクトを開始し、アプリの開始後に、状態コード 401 のハンドルされない例外 (許可されていません) が発生することを確認します。これは、バックエンドへのアクセスが承認済みのユーザーのみに制限されているために発生します。

3. Android プロジェクトの MainActivity.cs を開き、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. 次のように、**MainActivity** クラスを更新して **IAuthenticate** インターフェイスを実装します。

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. 次のように、**MobileServiceUser** フィールドのほか、**IAuthenticate** インターフェイスに必要な **Authenticate** メソッドを追加して、**MainActivity** クラスを更新します。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


	Facebook 以外の ID プロバイダーを使用している場合、[MobileServiceAuthenticationProvider] には別の値を選択してください。

6. `LoadApplication()` の呼び出しの前にある **MainActivity** クラスの **OnCreate** メソッドに次のコードを追加します。

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

	これにより、アプリの読み込み前に Authenticator が初期化されるようになります。

7. アプリをリビルドして実行します。その後で、選択した認証プロバイダーを使用してサインインし、認証されたユーザーとしてデータにアクセスできることを確認します。

##iOS アプリに認証を追加する

このセクションでは、iOS アプリ プロジェクト内に **IAuthenticate** インターフェイスを実装する方法について説明します。iOS デバイスをサポートしない場合、このセクションはスキップしてください。

1. Visual Studio または Xamarin Studio で、**iOS** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. F5 キーを押してデバッガーでプロジェクトを開始し、アプリの開始後に、状態コード 401 のハンドルされない例外 (許可されていません) が発生することを確認します。これは、バックエンドへのアクセスが承認済みのユーザーのみに制限されているために発生します。

4. iOS プロジェクトの AppDelegate.cs を開き、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. 次のように、**AppDelegate** クラスを更新して **IAuthenticate** インターフェイスを実装します。

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. 次のように、**MobileServiceUser** フィールドのほか、**IAuthenticate** インターフェイスに必要な **Authenticate** メソッドを追加して、**AppDelegate** クラスを更新します。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

	Facebook 以外の ID プロバイダーを使用している場合、[MobileServiceAuthenticationProvider] には別の値を選択してください。

6. `LoadApplication()` の呼び出しの前の **FinishedLaunching** メソッドに、次のコード行を追加します。

        App.Init(this);

	これにより、アプリの読み込み前に Authenticator が初期化されるようになります。

7. アプリをリビルドして実行します。その後で、選択した認証プロバイダーを使用してサインインし、認証されたユーザーとしてデータにアクセスできることを確認します。


##Windows アプリ プロジェクトに認証を追加する

このセクションでは、Windows 8.1 と Windows Phone 8.1 のアプリ プロジェクト内に **IAuthenticate** インターフェイスを実装する方法について説明します。ユニバーサル Windows プラットフォーム (UWP) プロジェクトには、同一の手順が適用されます。Windows デバイスをサポートしない場合、このセクションはスキップしてください。

1. Visual Studio で、**WinApp** プロジェクトと **WinPhone81** プロジェクトのいずれかを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. F5 キーを押してデバッガーでプロジェクトを開始し、アプリの開始後に、状態コード 401 のハンドルされない例外 (許可されていません) が発生することを確認します。これは、バックエンドへのアクセスが承認済みのユーザーのみに制限されているために発生します。

3. Windows アプリ プロジェクトの MainPage.xaml.cs を開き、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.UI.Popups;
		using <your_Portable_Class_Library_namespace>;

	`<your_Portable_Class_Library_namespace>` を、ポータブル クラス ライブラリの名前空間に置き換えます。

4. 次のように、**MainPage** クラスを更新して **IAuthenticate** インターフェイスを実装します。

	    public sealed partial class MainPage : IAuthenticate


5. 次のように、**MobileServiceUser** フィールドのほか、**IAuthenticate** インターフェイスに必要な **Authenticate** メソッドを追加して、**MainPage** クラスを更新します。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
						success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

			// Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


	Facebook 以外の ID プロバイダーを使用している場合、[MobileServiceAuthenticationProvider] には別の値を選択してください。

6. `LoadApplication()` の呼び出しの前にある **MainPage** クラスのコンストラクター内に次のコード行を追加します。

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    `<your_Portable_Class_Library_namespace>` を、ポータブル クラス ライブラリの名前空間に置き換えます。WinApp プロジェクトの場合、手順 8. に進むことができます。次の手順は、ログイン コールバックの完了が必要な WinPhone81 プロジェクトにのみ適用されます。

7. (省略可能) **WinPhone81** アプリ プロジェクトの App.xaml.cs を開き、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.MobileServices;
		using <your_Portable_Class_Library_namespace>;

	`<your_Portable_Class_Library_namespace>` を、ポータブル クラス ライブラリの名前空間に置き換えます。

8.  次の **OnActivated** メソッドのオーバーライドを **App** クラスに追加します。

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);

			// We just need to handle activation that occurs after web authentication. 
		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
				// Get the client and call the LoginComplete method to complete authentication.
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}

	メソッドのオーバーライドが既に存在する場合は、上記のスニペットから条件付きコードのみ追加してください。

7. アプリをリビルドして実行します。その後で、選択した認証プロバイダーを使用してサインインし、認証されたユーザーとしてデータにアクセスできることを確認します。

##次のステップ

これで基本的な認証チュートリアルは完了しましたので、引き続き次のいずれかのチュートリアルのご利用を検討してください。

+ [アプリへのプッシュ通知の追加](app-service-mobile-xamarin-forms-get-started-push.md) アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使用してプッシュ通知を送信するようにモバイル アプリ バックエンドを構成する方法について説明します。

+ [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md) モバイル アプリ バックエンドを使用して、オフライン サポートをアプリに追加する方法について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリとやり取りできます。

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

<!---HONumber=AcomDC_0629_2016-->