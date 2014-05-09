<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="シングル サインオン認証" pageTitle="Live Connect による Windows ストア アプリケーションの認証 (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, モバイル サービス sso, Windows ストア アプリケーション sso, Azure Javascript SSO" description="Windows ストア アプリケーションから Azure モバイル サービス内で Live Connect シングル サインオンを使用する方法について説明します。" metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="" documentationCenter="Mobile" title="Live Connect シングル サインオンによる Windows ストア アプリケーションの認証" authors="" solutions="" manager="" editor="" />




# Live Connect シングル サインオンによる Windows ストア アプリケーションの認証
<div class="dev-center-tutorial-selector sublanding">
	<a href="/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>	


このトピックでは、Windows ストア アプリケーションから Azure モバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、Live Connect を使用して、クイック スタート プロジェクトに認証を追加します。Live Connect によって正常に認証されると、ログインしたユーザーの名前が認識され、ユーザー ID 値が表示されます。

<div class="dev-callout"><b>メモ</b>
	<p>このチュートリアルでは、Live Connect によって Windows ストア アプリに提供されるシングル サインオンを使用する利点を示します。これにより、モバイル サービスに既にログオンしているユーザーの認証が容易になります。複数の認証プロバイダーをサポートする、より汎用的な認証方法については、「<a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-js/">認証の使用</a>」を参照してください。</p>
</div>

このチュートリアルでは、Live Connect 認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルには、次のものが必要です。

+ [Windows 向け Live SDK]
+ Microsoft Visual Studio 2012 Express for Windows 8 RC 以降のバージョン

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

<h2><a name="register"></a><span class="short-header">アプリケーションの登録</span>アプリケーションを Windows ストアに登録する</h2>

ユーザーを認証できるようにするには、アプリケーションを Windows ストアに提出する必要があります。その後、クライアント シークレットを登録して Live Connect をモバイル サービスと統合する必要があります。

1. アプリケーションをまだ登録していない場合は、Windows ストア アプリケーションのデベロッパー センターで<a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">アプリケーション送信のページ</a>に移動し、Microsoft アカウントでログインして、**[アプリケーション名]** をクリックします。

   	![][0]

2. **[アプリケーション名]** にアプリケーションの名前を入力し、**[アプリケーション名の予約]** をクリックして、**[保存]** をクリックします。

   	![][1]

   	これでアプリケーションの新しい Windows ストア登録が作成されます。

3. Visual Studio 2012 Express for Windows 8 で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. ソリューション エクスプローラーでプロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

  	![][2]

   	**アプリケーションを Windows ストアと関連付ける**ウィザードが表示されます。

5. ウィザードで **[サインイン]** をクリックし、Microsoft アカウントでログインします。

6. 手順 2. で登録したアプリケーションを選択し、**[次へ]**、**[関連付け]** の順にクリックします。

   	![][3]

   	この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。

7. [Azure 管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

   	![][4]

8. **[ダッシュボード]** タブをクリックし、**[サイトの URL]** の値を書き留めます。

   	![][5]

    後でこの値を使用して、リダイレクト ドメインを定義します。

9. Live Connect デベロッパー センターの <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">[マイ アプリケーション]</a> ページに移動し、**[マイ アプリケーション]** の一覧でアプリケーションをクリックします。

   	![][6] 

10. **[設定の編集]**、**[API 設定]** の順にクリックし、**[クライアント ID]** と **[クライアント シークレット]** の値を書き留めます。

   	![][7]

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットは、他のユーザーと共有したり、アプリケーションで配信したりしないでください。</p>
    </div>

11. **[リダイレクト ドメイン]** で手順 8. のモバイル サービスの URL を入力し、**[保存]** をクリックします。

12. 管理ポータルに戻って **[ID]** タブをクリックし、Windows ストアから入手した **[クライアント シークレット]** を入力して、**[保存]** をクリックします。

   	![][13]

これで、Live Connect と連携するようにモバイル サービスとアプリケーションが構成されました。

<h2><a name="permissions"></a><span class="short-header">アクセス許可の制限</span>アクセス許可を認証されたユーザーだけに制限する</h2>

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][14]

2. **[アクセス許可]** タブで、すべてのアクセス許可を **[認証されたユーザーのみ]** に設定し、**[保存]** をクリックします。これにより、**TodoItem** テーブルに対するすべての操作には、認証されたユーザーが必要になります。また、次のチュートリアルのスクリプトは、匿名ユーザーの可能性を考慮する必要がなくなるため、簡素化されます。

   	![][15]

3. Visual Studio 2012 Express for Windows 8 で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. F5 キーを押してこのクイック スタート ベースのアプリケーションを実行します。その結果、状態コード 401 (許可されていません) の例外が発生することを確認します。
   
   	この問題は、認証されていないユーザーとしてアプリケーションからモバイル サービスにアクセスしているのに、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、モバイル サービスからリソースを要求する前に Live Connect を使用してユーザーを認証するようにアプリケーションを更新します。

<h2><a name="add-authentication"></a><span class="short-header">認証の追加</span>アプリに認証を追加する</h2>

1. [Windows 向け Live SDK] をダウンロードしてインストールします。

2. Visual Studio で、**[プロジェクト]** メニューの **[参照の追加]** をクリックし、**[Windows]** を展開して、**[拡張]** をクリックします。**[Live SDK]** チェック ボックスをオンにし、**[OK]** をクリックします。

  	![][16]

  	これにより、Live SDK への参照がプロジェクトに追加されます。

3. default.html プロジェクト ファイルを開き、次の &lt;script&gt; 要素を &lt;head&gt; 要素に追加します。

        <script src="///LiveSDKHTML/js/wl.js"></script>

   	これにより、default.html ファイル内で Microsoft IntelliSense が有効になります。


4. default.js プロジェクト ファイルを開き、次のコメントをファイルの先頭に追加します。

        /// <reference path="///LiveSDKHTML/js/wl.js" />

   	これにより、default.js ファイル内で Microsoft IntelliSense が有効になります。

5. **app.OnActivated** メソッド オーバーロードで、**refreshTodoItems** メソッドの呼び出しを次のコードに置き換えます。
	
        var session = null;   

        var logout = function () {
            return new WinJS.Promise(function (complete) {
                WL.getLoginStatus().then(function () {
                    if (WL.canLogout()) {
                        WL.logout(complete);                            
                    }
                    else {
                        complete();
                    }
                });
            });
        };                  

        var login = function () {
            return new WinJS.Promise(function (complete) {                    
                WL.login({ scope: "wl.basic"}).then(function (result) {
                    session = result.session;

                    WinJS.Promise.join([
                        WL.api({ path: "me", method: "GET" }),
                        client.login(result.session.authentication_token)
                    ]).done(function (results) {
                        var profile = results[0];
                        var mobileServicesUser = results[1];
                        refreshTodoItems();
                        var title = "Welcome " + profile.first_name + "!";
                        var message = "You are now logged in as: " + mobileServicesUser.userId;
                        var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                        dialog.showAsync().done(complete);                                
                    });                       
                }, function (error) {                        
                    session = null;
                    var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                    dialog.showAsync().done(complete);                        
                });
            });
        }

        var authenticate = function () {
            // Force a logout to make it easier to test with multiple Microsoft Accounts
            logout().then(login).then(function () {
                if (session === null) {
                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        WL.init({
            redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
        });           
            
        authenticate();

    これにより、Live Connect クライアントの初期化、ログアウトの強制、Live Connect への新しいログイン要求の送信、モバイル サービスへの返された認証トークンの送信、およびログイン済みユーザーに関する情報の表示が行われます。

    <div class="dev-callout"><b>注</b>
	<p>このコードでは、アプリケーションの実行ごとに資格情報をユーザーに求めるために、可能な場合にはログアウトを強制します。これにより、認証が正常に動作していることを確認するために、複数の Microsoft アカウントを使用してアプリケーションをテストすることが容易になります。このメカニズムは、ログインしたユーザーが接続済みの Microsoft アカウントを持っていない場合のみ有効です。</p>
    </div>
	
7. 前の手順に含まれる文字列 _<< INSERT REDIRECT DOMAIN HERE >>_ を、Live Connect でアプリケーションを設定する際に指定されたリダイレクト ドメイン (**https://_service-name_.azure-mobile.net/** 形式) で更新します。
		
8. F5 キーを押してアプリケーションを実行し、Microsoft アカウントを使用して Live Connect にサインインします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## <a name="next-steps"> </a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。他の ID プロバイダーを認証に使用する方法については、「[モバイル サービスでの認証の使用]」を参照してください。

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[次のステップ]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png

<!-- URLs. -->
[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-js
[モバイル サービスでの認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js
[スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript と HTML]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js

[Azure 管理ポータル]: https://manage.windowsazure.com/

