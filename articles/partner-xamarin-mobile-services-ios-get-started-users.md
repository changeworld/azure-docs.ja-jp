<properties urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="認証の使用 (Xamarin.iOS) - Mobile Services" metaKeywords="アプリケーションを登録する Azure, Azure 認証, アプリケーション認証, モバイル サービスの認証, Mobile Services Xamarin.iOS" description="Xamarin.iOS 向け Azure Mobile Services アプリケーションで認証を使用する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile"  services="mobile-services" title="Get started with authentication in Mobile Services" manager="dwrede" authors="donnam" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Mobile Services アプリへの認証の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、アプリケーションから Azure のモバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。  

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。 

このチュートリアルを完了するには、[Xamarin.iOS]、XCode 5.0、および iOS 5.0 以降のバージョンが必要です。

<h2><a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する</h2>


[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. Xcode で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。 

4. **[実行]** を押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 
   
   	この問題は、非認証ユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

<h2><a name="add-authentication"></a>アプリケーションに認証を追加する</h2>

1. **TodoService** プロジェクト ファイルを開き、次の変数を追加します。

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. **TodoService** に **Authenticate** という名前の新しいメソッドを追加し、次のように定義します。

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

    <div class="dev-callout"><b>注</b>
	<p>Microsoft アカウント以外の ID プロバイダーを使用している場合は、上の <strong>LoginAsync</strong> メソッドに渡される値を<i>Facebook</i>、<i>Twitter</i>、<i>Google</i>、<i>WindowsAzureActiveDirectory</i> のいずれかに変更します。</p>
    </div>

3. **TodoItem** テーブルに対する要求を、**TodoService** コンストラクターから **CreateTable** という名前の新しいメソッドに移動します。

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }
	
4. **LoginAndGetData** という名前の新しい非同期パブリック メソッドを作成し、次のように定義します。

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. **TodoListViewController** で、**ViewDidAppear** メソッドをオーバーライドし、次のように定義します。これにより、ユーザーに対するハンドルが **TodoService** にまだない場合は、ユーザーにログインします。

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
6. **RefreshAsync** の元の呼び出しを **TodoListViewController.ViewDidLoad** から削除します。
		
7. **[実行]** を押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを起動して、選択した ID プロバイダーでログオンします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## 完成したサンプルの入手
[完成したサンプル プロジェクト]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。 

## <a name="next-steps"></a>次のステップ

次の[スクリプトを使用したユーザーの認証に関するチュートリアル]では、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。 

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[次のステップ]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-ios
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios

[Azure 管理ポータル]: https://manage.windowsazure.com/
[完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331328
