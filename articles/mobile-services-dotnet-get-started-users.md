<properties linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="ユーザー認証の使用" pageTitle="認証の使用 (Windows ストア) | モバイル デベロッパー センター" metaKeywords="" description="モバイル サービスを使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを介して Windows ストア アプリのユーザーを認証する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスでの認証の使用" authors=""  solutions="" writer="" manager="" editor=""  />




# モバイル サービスでの認証の使用

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows ストア C#" class="current">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このトピックでは、アプリケーションから Azure のモバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。</p>
<p>このチュートリアルのビデオ バージョンを見るには、右側のクリップをクリックします。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">10:04</span></div>
</div> 

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

<div class="dev-callout"><b>メモ</b>
	<p>このチュートリアルでは、さまざまな ID プロバイダーを使用してユーザーを認証するためにモバイル サービスに用意されている基本的な方法を示します。この方法は構成が容易で、複数のプロバイダーをサポートしています。ただし、アプリケーションが開始されるたびに、ユーザーはログインする必要があります。Windows ストア アプリでシングル サインオンできるように Live Connect を使用するには、トピック「<a href="/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet">Live Connect シングル サインオンによる Windows ストア アプリの認証</a>」を参照してください。</p>
</div>

<h2><a name="register"></a><span class="short-header">アプリの登録</span>アプリを認証に登録し、モバイル サービスを構成する</h2>

ユーザーを認証できるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、プロバイダーによって生成されたクライアント シークレットをモバイル サービスに登録する必要があります。

1. [Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

   	![][4]

2. **[ダッシュボード]** タブをクリックし、**[モバイル サービス URL]** の値をメモしておきます。

   	![][5]

    アプリケーションを登録するときに、この値を ID プロバイダーに指定する必要が生じる場合があります。

3. 以下の一覧から、サポートされている ID プロバイダーを選択し、手順に従ってそのプロバイダーにアプリケーションを登録します。

 - <a href="/ja-jp/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Microsoft アカウント</a>
 - <a href="/ja-jp/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Facebook ログイン</a>
 - <a href="/ja-jp/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Twitter ログイン</a>
 - <a href="/ja-jp/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Google ログイン</a>
 - <a href="/ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    プロバイダーによって生成されるクライアント ID およびシークレット値をメモしておいてください。

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>プロバイダーによって生成されるシークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
    </div>

4. 管理ポータルに戻って **[識別]** タブをクリックし、アプリケーションの識別子と、ID プロバイダーから取得した共有シークレット値を入力して、**[保存]** をクリックします。

   	![][13]

5. (省略可能)「[Windows ストア アプリ パッケージを Microsoft 認証に登録する]」の手順を実行します。

    <div class="dev-callout"><b>メモ</b>
	<p>この手順は Microsoft アカウント ログイン プロバイダーのみに適用されるため、省略可能です。Windows ストア アプリのパッケージ情報をモバイル サービスに登録すると、クライアントはシングル サインオン サービス エクスペリエンスを実現するために Microsoft アカウント ログイン資格情報を再利用できます。この操作を行わない場合、login メソッドが呼び出されるたびに、Microsoft アカウント ログイン ユーザーにログイン プロンプトが表示されます。Microsoft アカウント ID プロバイダーを使用する場合は、この手順を実行してください。</p>
    </div>

これで、モバイル サービスとアプリケーションの両方が、選択した認証プロバイダーと連係するように構成されました。

<h2><a name="permissions"></a><span class="short-header">アクセス許可の制限</span>アクセス許可を認証されたユーザーだけに制限する</h2>

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][14]

2. **[アクセス許可]** タブで、すべてのアクセス許可を **[認証されたユーザーのみ]** に設定し、**[保存]** をクリックします。これにより、**TodoItem** テーブルに対するすべての操作には、認証されたユーザーが必要になります。また、次のチュートリアルのスクリプトは、匿名ユーザーの可能性を考慮する必要がなくなるため、簡素化されます。

   	![][15]

3. Visual Studio 2012 Express for Windows 8 で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。
   
   	この問題は、非認証ユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

<h2><a name="add-authentication"></a><span class="short-header">認証の追加</span>アプリに認証を追加する</h2>

5. mainpage.xaml.cs プロジェクト ファイルを開き、次の using ステートメントを追加します。

        using Windows.UI.Popups;

6. MainPage クラスに、次のコード スニペットを追加します。
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    これにより、現在のユーザーを格納するためのメンバー変数と認証プロセスを処理するためのメソッドが作成されます。ユーザーは、Facebook ログインを使用して認証されます。Facebook 以外の ID プロバイダーを使用している場合は、上の **MobileServiceAuthenticationProvider** の値を、プロバイダーに対応する値に変更してください。

    <div class="dev-callout"><b>メモ</b>
	<p>Windows ストア アプリ パッケージ情報をモバイル サービスに登録している場合は、<em>useSingleSignOn</em> パラメーターに値 <strong>true</strong> を指定して <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> メソッドを呼び出す必要があります。この操作を行わない場合、login メソッドが呼び出されるたびに、ユーザーにログイン プロンプトが表示されます。</p>
    </div>

8. 既存の **OnNavigatedTo** イベント ハンドラーを、新しい **Authenticate** メソッドを呼び出す次のハンドラーに置き換えます。

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }
		
9. F5 キーを押してアプリケーションを実行し、選択した ID プロバイダーでアプリケーションにサインインします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## <a name="next-steps"> </a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。.NET でモバイル サービスを使用する方法の詳細については、「[モバイル サービス .NET の使用方法の概念リファレンス]」を参照してください。

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[次のステップ]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-dotnet-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-dotnet-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-dotnet-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect を使用した Windows ストア アプリへのシングル サインオン]: ./mobile-services-single-sign-on-win8-dotnet.md
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet
[スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js

[Azure の管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス .NET の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-net-client-library
[Windows ストア アプリ パッケージを Microsoft 認証に登録する]: /ja-jp/develop/mobile/how-to-guides/register-windows-store-app-package

