<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Live Connect シングル サインオンによる Windows ストア アプリの認証

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows ストア JavaScript" class="current">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>

このトピックでは、Windows ストア アプリケーションから Azure モバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、Live Connect を使用して、クイック スタート プロジェクトに認証を追加します。Live Connect によって正常に認証されると、ログインしたユーザーの名前が認識され、ユーザー ID 値が表示されます。

> [WACOM.NOTE]このチュートリアルでは、Live Connect によって Windows ストア アプリに提供されるシングル サインオンを使用する利点を示します。これにより、モバイル サービスに既にログオンしているユーザーの認証が容易になります。複数の認証プロバイダーをサポートする、より汎用的な認証方法については、「[モバイル サービスでの認証の使用][モバイル サービスでの認証の使用]」を参照してください。

このチュートリアルでは、Live Connect 認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する][アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する][テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する][アプリケーションに認証を追加する]

このチュートリアルには、次のものが必要です。

-   [Windows 向け Live SDK][Windows 向け Live SDK]
-   Microsoft Visual Studio 2012 Express for Windows 8 RC 以降のバージョン

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

> [WACOM.NOTE]このチュートリアルでは JavaScript バックエンド モバイル サービスを使用します。Live Connect を使用するクライアントによって管理される認証やその他の認証クライアントは、.NET バックエンド モバイル サービスではまだサポートされていません。

## <a name="register"></a>アプリケーションを Windows ストアに登録する

ユーザーを認証できるようにするには、アプリケーションを Windows ストアに提出する必要があります。その後、クライアント シークレットを登録して Live Connect をモバイル サービスと統合する必要があります。

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Visual Studio 2012 Express for Windows 8 で、チュートリアル「[モバイル サービスの使用][1]」を実行したときに作成したプロジェクトを開きます。

2.  F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

    この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

1.  [Windows 向け Live SDK][Windows 向け Live SDK] をダウンロードしてインストールします。

2.  Visual Studio で、**[プロジェクト]** メニューの **[参照の追加]** をクリックし、**[Windows]** を展開して、**[拡張]** をクリックします。**[Live SDK]** チェック ボックスをオンにし、**[OK]** をクリックします。

    ![][0]

    これにより、Live SDK への参照がプロジェクトに追加されます。

3.  default.html プロジェクト ファイルを開き、次の \<script\> 要素を \<head\> 要素に追加します。

        <script src="///LiveSDKHTML/js/wl.js"></script>

    これにより、default.html ファイル内で Microsoft IntelliSense が有効になります。

4.  default.js プロジェクト ファイルを開き、次のコメントをファイルの先頭に追加します。

        /// <reference path="///LiveSDKHTML/js/wl.js" />

    これにより、default.js ファイル内で Microsoft IntelliSense が有効になります。

5.  **app.OnActivated** メソッド オーバーロードで、**refreshTodoItems** メソッドの呼び出しを次のコードに置き換えます。

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

    これにより、Live Connect クライアントの初期化、ログアウトの強制、Live Connect への新しいログイン要求の送信、モバイル サービスへの返された認証トークンの送信、およびログイン済みユーザーに関する情報の表示が行われます。このコードでは、アプリケーションの実行ごとに資格情報をユーザーに求めるために、可能な場合にはログアウトを強制します。これにより、認証が正常に動作していることを確認するために、複数の Microsoft アカウントを使用してアプリケーションをテストすることが容易になります。このメカニズムは、ログインしたユーザーが接続済みの Microsoft アカウントを持っていない場合のみ有効です。

    > [WACOM.NOTE]アプリケーションを実行するたびに Live Connection 認証トークンまたはモバイル サービス認証トークンをリクエストしないようにします。これは非効率であるだけなく、多くの顧客が同時にアプリケーションを開始すると、使用率に関連した問題が発生する場合があります。よって、トークンをキャッシュし、キャッシュされたモバイル サービス トークンをまず試してから、**LoginWithMicrosoftAccountAsync**を呼び出す方が効果的です。このトークンのキャッシュ方法の詳細については、「[モバイル サービスでの認証の使用][2]」を参照してください。

6.  前の手順に含まれる文字列 *\<\< ここにリダイレクト ドメインを挿入 \>\>* を、Live Connect でアプリケーションを設定する際に指定されたリダイレクト ドメイン (**https://_service-name_.azure-mobile.net/** 形式) で更新します。

7.  F5 キーを押してアプリケーションを実行し、Microsoft アカウントを使用して Live Connect にサインインします。

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## <a name="next-steps"> </a>次のステップ

[スクリプトを使用したユーザーの認証][スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。他の ID プロバイダーを認証に使用する方法については、「[モバイル サービスでの認証の使用][3]」を参照してください。



  [モバイル サービスでの認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png
  [2]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens
  [スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-js
  [3]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
