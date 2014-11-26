<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Live Connect シングル サインオンによる Windows Phone 8 アプリの認証

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

このトピックでは、Live Connect シングル サインオンを使用して、Windows Phone 8 アプリケーションから Azure のモバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、Live Connect を使用して、クイック スタート プロジェクトに認証を追加します。Live Connect によって正常に認証されると、ログインしたユーザーの名前が認識され、ユーザー ID 値が表示されます。

<div class="dev-callout"><b>注</b>
<p>このチュートリアルでは、Live Connect によって Windows Phone アプリケーションに提供されるシングル サインオンを使用する利点を説明します。これにより、モバイル サービスに既にログオンしているユーザーの認証が容易になります。複数の認証プロバイダーをサポートする、より汎用的な認証方法については、「<a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-wp8/">モバイル サービスでの認証の使用</a>」を参照してください。 </p>
</div>

このチュートリアルでは、Live Connect 認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する][アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する][テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する][アプリケーションに認証を追加する]

このチュートリアルには、次のものが必要です。

-   [Windows および Windows Phone 向け Live SDK][Windows および Windows Phone 向け Live SDK]
-   Microsoft Visual Studio 2012 Express for Windows Phone

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

## <a name="register"></a><span class="short-header">アプリケーションの登録</span>アプリケーションを Live Connect に登録する

ユーザーを認証できるようにするには、Live Connect デベロッパー センターでアプリケーションを登録する必要があります。その後、クライアント シークレットを登録して Live Connect をモバイル サービスと統合する必要があります。

1.  [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

    ![][0]

2.  **[ダッシュボード]** タブをクリックし、**[サイトの URL]** の値をメモしておきます。

    ![][1]

    後でこの値を使用して、リダイレクト ドメインを定義します。

3.  [Live Connect デベロッパー センター][Live Connect デベロッパー センター] アプリケーション管理のページに移動し、必要な場合は Microsoft アカウントでログオンします。

4.  **[アプリケーションの作成]** をクリックし、**[アプリケーション名]** に名前を入力して、**[同意する]** をクリックします。

    ![][2]

    これでアプリケーションが Live Connect に登録されます。

5.  **[設定の編集]**、**[API 設定]** の順にクリックし、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めておきます。

    ![][3]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
<p>クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットは、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>
</div>

6.  **[リダイレクト ドメイン]** ボックスに手順 2. で入手したモバイル サービスの URL を入力し、**[モバイル クライアント アプリ]** の **[はい]** をクリックして、**[保存]** をクリックします。

7.  管理ポータルに戻って **[ID]** タブをクリックし、Live Connect から入手した **[クライアント シークレット]** を入力して、**[保存]** をクリックします。

    ![][4]

これで、Live Connect と連携するようにモバイル サービスとアプリケーションが構成されました。

## <a name="permissions"></a><span class="short-header">アクセス許可の制限</span>アクセス許可を認証されたユーザーだけに制限する

1.  管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][5]

2.  **[アクセス許可]** タブで、すべてのアクセス許可を **[認証されたユーザーのみ]** に設定し、**[保存]** をクリックします。これにより、**TodoItem** テーブルに対するすべての操作には、認証されたユーザーが必要になります。また、次のチュートリアルのスクリプトは、匿名ユーザーの可能性を考慮する必要がなくなるため、簡素化されます。

    ![][6]

3.  Visual Studio 2012 Express for Windows Phone で、チュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4.  F5 キーを押してこのクイック スタート ベースのアプリケーションを実行します。その結果、状態コード 401 (許可されていません) の例外が発生することを確認します。

    この問題は、認証されていないユーザーとしてアプリケーションからモバイル サービスにアクセスしているのに、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスからリソースを要求する前に Live Connect を使用してユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a><span class="short-header">認証の追加</span>アプリケーションに認証を追加する

1.  [Windows および Windows Phone 向け Live SDK][Windows および Windows Phone 向け Live SDK] をダウンロードしてインストールします。

2.  Visual Studio で、**[プロジェクト]** メニューの **[参照の追加]** をクリックし、**[アセンブリ]** を展開して、**[拡張]** をクリックします。**[Microsoft.Live]** チェック ボックスをオンにし、**[OK]** をクリックします。

    ![][7]

    これにより、Live SDK への参照がプロジェクトに追加されます。

3.  mainpage.xaml.cs プロジェクト ファイルを開き、次の using ステートメントを追加します。

        using Microsoft.Live;      

4.  MainPage クラスに、次のコード スニペットを追加します。

        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

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

    これにより、現在の Live Connect セッションを格納するためのメンバー変数と認証プロセスを処理するためのメソッドが作成されます。

5.  前の手順に含まれる文字列 *\<\< INSERT CLIENT ID HERE \>\>* を、アプリケーションを Live Connect に登録した際に生成されたクライアント ID 値で更新します。

    <div class="dev-callout"><b>注</b>
<p>Windows Phone 8 アプリで、<strong>LiveAuthClient</strong> クラスのインスタンスは、クライアント ID 値をクラス コンストラクターに渡すことによって作成されます。<a href="/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/">Windows ストア アプリ</a>では、リダイレクト ドメインの URI を渡すことによって、同じクラスのインスタンスが作成されます。</p>
</div>

6.  既存の **OnNavigatedTo** メソッド オーバーライドを削除またはコメントアウトして、ページの **Loaded** イベントを処理する次のメソッドに置き換えます。

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

    このメソッドでは、新しい **Authenticate** メソッドが呼び出されます。

7.  MainPage コンストラクターを次のコードに置き換えます。

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

    このコンストラクターでは、Loaded イベントのハンドラーも登録されます。

8.  F5 キーを押してアプリケーションを実行し、Microsoft アカウントを使用して Live Connect にサインインします。

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## <a name="next-steps"> </a>次のステップ

[スクリプトを使用したユーザーの認証][スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。他の ID プロバイダーを認証に使用する方法については、「[モバイル サービスでの認証の使用][8]」を参照してください。

 
 


  [モバイル サービスでの認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8/
  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [Windows および Windows Phone 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-wp8
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png
  [Live Connect デベロッパー センター]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
  [3]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
  [4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
  [5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
  [7]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png
  [スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [8]: /ja-jp/develop/mobile/tutorials/get-started-with-users-wp8
