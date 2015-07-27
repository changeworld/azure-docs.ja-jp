<properties
	pageTitle="認証の使用 (Xamarin.iOS) - Mobile Services"
	description="Xamarin.iOS 向け Azure Mobile Services アプリケーションで認証を使用する方法について説明します。"
	documentationCenter="xamarin"
	services="mobile-services"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/14/2015"
	ms.author="donnam"/>

# Mobile Services アプリへの認証の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、Mobile Services を構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

このチュートリアルを実施するには、[Xamarin.iOS]、XCode 6.0、および iOS 7.0 以降のバージョンが必要です。

##<a name="register"></a>アプリケーションを認証に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. Xcode で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. **[実行]** を押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

   	この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

1. **ToDoService** プロジェクト ファイルを開き、次の変数を追加します。

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. **ToDoService** に **Authenticate** という名前の新しいメソッドを追加し、次のように定義します。

        private async Task Authenticate(MonoTouch.UIKit.UIViewController view)
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

	> [AZURE.NOTE]Microsoft アカウント以外の ID プロバイダーを使用している場合は、上の例の **LoginAsync** に渡される値を _Facebook_、_Twitter_、_Google_、または _WindowsAzureActiveDirectory_ のいずれかに変更します。

3. **ToDoItem** テーブルに対する要求を、**ToDoService** コンストラクターから **CreateTable** という名前の新しいメソッドに移動します。

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the ToDoItem table
            todoTable = client.GetSyncTable<ToDoItem>();
        }

4. **LoginAndGetData** という名前の新しい非同期パブリック メソッドを作成し、次のように定義します。

        public async Task LoginAndGetData(MonoTouch.UIKit.UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. **TodoListViewController** で、**ViewDidAppear** メソッドをオーバーライドし、次のように定義します。これにより、ユーザーに対するハンドルが **￼ToDoService￼** にまだない場合は、ユーザーにログインします。

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (QSToDoService.DefaultService.User == null)
            {
                await QSToDoService.DefaultService.LoginAndGetData(this);
            }

            if (QSToDoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            }

            RefreshAsync();
        }
6. **RefreshAsync** の元の呼び出しを **TodoListViewController.ViewDidLoad** から削除します。

7. **[Run]** を押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを起動して、選択した ID プロバイダーでログオンします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## 完成したサンプルの入手
[完成したサンプル プロジェクト]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。

## <a name="next-steps"></a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

<!-- Anchors. -->
[アプリケーションを認証に登録し、Mobile Services を構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[モバイル サービスの使用]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[スクリプトを使用したユーザーの認証]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331328
[Xamarin.iOS]: http://xamarin.com/download
 

<!---HONumber=July15_HO3-->