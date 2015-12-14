<properties 
	pageTitle="Mobile Apps での Xamarin.Forms アプリの認証の使用" 
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
	ms.date="11/30/2015" 
	ms.author="wesmc"/>

# Xamarin.Forms アプリに認証を追加する

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##概要

このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、Xamarin.Forms クイック スタート プロジェクトに認証を追加します。モバイル アプリによって正常に認証され、承認されるとユーザー ID 値が表示され、制限付きのテーブル データにアクセスできます。

最初に [Xamarin.Forms クイック スタート チュートリアル](app-service-mobile-xamarin-forms-get-started.md)を完了しておく必要があります。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。


##アプリケーションを認証に登録し、App Services を構成する

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##アクセス許可を、認証されたユーザーだけに制限する

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##ポータブル クラス ライブラリに認証を追加する 

Mobile Apps では、ログイン インターフェイスの表示とデータのキャッシュを行うために、プラットフォーム固有の `MobileServiceClient.LoginAsync` メソッドを使用します。Xamarin Forms プロジェクトで認証するには、ポータブル クラス ライブラリに `IAuthenticate` インターフェイスを定義します。サポートするプラットフォームごとに、このインターフェイスをプラットフォーム固有のプロジェクト内に実装できます。ポータブル クラス ライブラリから制限付きのテーブルを呼び出す前に認証を実行するためのコードを追加します。

1. Visual Studio または Xamarin Studio で、**ポータブル** プロジェクトから App.cs を開きます。次の `using` ステートメントをファイルに追加します。

		using System.Threading.Tasks;

2. App.cs に、次の `IAuthenticate` インターフェイス定義を、`App` クラス定義の直前に追加します。

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. App.cs に、インターフェイスをプラットフォーム固有の実装で初期化するように次の静的メンバーを追加します。

		public class App : Application
		{
	
	        public static IAuthenticate Authenticator { get; private set; }
	
	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }
	
			...

4. **ポータブル** プロジェクトから TodoList.xaml.cs を開き、テーブルの項目を更新する前に認証を実行するように `OnAppearing` メソッドを更新します。


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            if (App.Authenticator != null)
                await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            await RefreshItems(true, syncItems: false);
        }


5. 変更を保存し、ポータル プロジェクトをビルドしてエラーがないことを確認します。


##Android アプリに認証を追加する

このセクションでは、droid プロジェクト用の認証を追加します。Android デバイスを使用していない場合は、このセクションをスキップできます。

1. Visual Studio または Xamarin Studio で、**droid** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. 操作を進めてプロジェクトをデバッガーで実行して、アプリの開始後に、状態コード 401 の未処理例外 (許可されていません) が発生することを確認します。これは、バックエンドでのアクセスが許可されたユーザーのみに制限されたために発生します。

3. 次に、droid プロジェクトのMainActivity.cs を開き、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. `MainActivity` クラスを、`IAuthenticate`インターフェイスを実装するように更新します。

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. `IAuthenticate` インターフェイスをサポートするために、次のように `MainActivity` クラスに `MobileServiceUser` フィールドと `Authenticate` メソッドを追加して更新します。
 
	Facebook ではなく別の `MobileServiceAuthenticationProvider` を使用する場合は、該当箇所も変更します。

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

6. アプリを読み込む前に Authenticator を初期化するように `MainActivity` クラスの `OnCreate` メソッドを更新します。

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. アプリケーションをリビルドして実行します。選択した認証プロバイダーを使用してログインし、認証ユーザーとしてテーブルにアクセスできることを確認します。



##iOS アプリに認証を追加する

このセクションでは、iOS プロジェクト用の認証を追加します。iOS デバイスを使用していない場合は、このセクションをスキップできます。

1. Visual Studio または Xamarin Studio で、**iOS** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. 操作を進めてプロジェクトをデバッガーで実行して、アプリの開始後に、状態コード 401 の未処理例外 (許可されていません) が発生することを確認します。これは、バックエンドでのアクセスが許可されたユーザーのみに制限されたために発生します。

3. 次に、iOS プロジェクトのAppDelegate.cs を開き、次の `using` ステートメントを追加します。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. `AppDelegate` クラスを、`IAuthenticate`インターフェイスを実装するように更新します。

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. `IAuthenticate` インターフェイスをサポートするために、次のように `AppDelegate` クラスに `MobileServiceUser` フィールドと `Authenticate` メソッドを追加して更新します。
 
	Facebook ではなく別の `MobileServiceAuthenticationProvider` を使用する場合は、該当箇所も変更します。

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. アプリを読み込む前に Authenticator を初期化するように `AppDelegate` クラスの `FinishedLaunching` メソッドを更新します。

        App.Init((IAuthenticate)this);

		LoadApplication (new App ());



7. アプリケーションをリビルドして実行します。選択した認証プロバイダーを使用してログインし、認証ユーザーとしてテーブルにアクセスできることを確認します。




##Windows アプリに認証を追加する

このセクションでは、WinApp プロジェクト用の認証を追加します。Windows デバイスを使用していない場合は、このセクションをスキップできます。

1. Visual Studio で、**WinApp** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

2. 操作を進めてプロジェクトをデバッガーで実行して、アプリの開始後に、状態コード 401 の未処理例外 (許可されていません) が発生することを確認します。これは、バックエンドでのアクセスが許可されたユーザーのみに制限されたために発生します。

3. 次に、WinApp プロジェクトのMainPage.xaml.cs を開き、次の `using` ステートメントを追加します。<*Your portable class library namespace*> を、ポータブル クラス ライブラリの名前空間に置き換えます。

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. `MainPage` クラスを、`IAuthenticate`インターフェイスを実装するように更新します。

	    public sealed partial class MainPage : IAuthenticate


5. `IAuthenticate` インターフェイスをサポートするために、次のように `MainPage` クラスに `MobileServiceUser` フィールドと `Authenticate` メソッドを追加して更新します。
 
	Facebook ではなく別の `MobileServiceAuthenticationProvider` を使用する場合は、該当箇所も変更します。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    var messageDialog = new Windows.UI.Popups.MessageDialog(
							string.Format("you are now logged in - {0}", user.UserId), "Authentication");
                    messageDialog.ShowAsync();
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. アプリを読み込む前に Authenticator を初期化するように `MainPage` クラスのコンストラクターを更新します。<*Your portable class library namespace*> を、ポータブル クラス ライブラリの名前空間に置き換えます。

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init((IAuthenticate)this);
            
            LoadApplication(new WesmcMobileAppGaTest.App());
        }



7. アプリケーションをリビルドして実行します。選択した認証プロバイダーを使用してログインし、認証ユーザーとしてテーブルにアクセスできることを確認します。




<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1203_2015--->