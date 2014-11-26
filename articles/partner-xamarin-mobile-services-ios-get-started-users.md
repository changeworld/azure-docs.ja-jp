<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile"  services="mobile-services" title="Get started with authentication in Mobile Services" manager="dwrede" authors="donnam" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# モバイル サービスでの認証の使用

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する][アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する][テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する][アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

このチュートリアルを完了するには、[Xamarin.iOS]、XCode 5.0、および iOS 5.0 以降のバージョンが必要です。

## <a name="register"></a><span class="short-header">アプリの登録</span>アプリを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a><span class="short-header">アクセス許可の制限</span>アクセス許可を認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Xcode で、チュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

2.  **[実行]** を押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

    この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a><span class="short-header">認証の追加</span>アプリに認証を追加する

1.  **TodoService** プロジェクト ファイルを開き、次の変数を追加します。

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  **TodoService** に **Authenticate** という名前の新しいメソッドを追加し、次のように定義します。

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout">

    <b>注</b>
    <p>Microsoft アカウント以外の ID プロバイダーを使用している場合は、上の <b>LoginAsync</b> に渡される値を<i>Facebook</i>、<i>Twitter</i>、<i>Google</i>、<i>WindowsAzureActiveDirectory</i> のいずれかに変更します。

    </div>

3.  **TodoItem** テーブルに対する要求を、**TodoService** コンストラクターから **CreateTable** という名前の新しいメソッドに移動します。

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }

4.  **LoginAndGetData** という名前の新しい非同期パブリック メソッドを作成し、次のように定義します。

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5.  **TodoListViewController** で、**ViewDidAppear** メソッドをオーバーライドし、次のように定義します。これにより、ユーザーに対するハンドルが **TodoService** にまだない場合は、ユーザーにログインします。

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            } 

            RefreshAsync();
        }

6.  **RefreshAsync** の元の呼び出しを **TodoListViewController.ViewDidLoad** から削除します。

7.  **[Run]** を押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを起動して、選択した ID プロバイダーでログオンします。

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## 完成したサンプルの入手

[完成したサンプル プロジェクト][完成したサンプル プロジェクト]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。

## <a name="next-steps"></a>次のステップ

[スクリプトを使用したユーザーの認証][スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

 
 
<!-- URLs. TODO:: update completed example project link with project download -->

  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
  [完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
