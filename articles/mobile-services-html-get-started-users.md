<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# モバイル サービスでの認証の使用

[WACOM.INCLUDE [mobile-services-selector-get-started-users][mobile-services-selector-get-started-users]]

このトピックでは、HTML または PhoneGap アプリケーションから Azure のモバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1.  [アプリケーションを認証に登録し、モバイル サービスを構成する][アプリケーションを認証に登録し、モバイル サービスを構成する]
2.  [テーブルのアクセス許可を、認証されたユーザーだけに制限する][テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3.  [アプリケーションに認証を追加する][アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

## <a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][mobile-services-restrict-permissions-javascript-backend]]

1.  app ディレクトリで、**server** サブフォルダーから次のいずれかのコマンド ファイルを実行します。

    -   **launch-windows** (Windows コンピューター)
    -   **launch-mac.command** (Mac OS X コンピューター)
    -   **launch-linux.sh** (Linux コンピューター)

    > [WACOM.NOTE] Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「`R`」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。

    これにより、新しいアプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

2.  Web ブラウザーで URL <http://localhost:8000/> を開いて、アプリケーションを開始します。

    データの読み込みに失敗します。この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

3.  (省略可能) Web ブラウザーのスクリプト デバッガーを開き、ページを再読み込みします。アクセス拒否エラーが発生することを確認します。

次に、モバイル サービスのリソースを要求する前に認証を許可するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する

> [WACOM.NOTE] ログインはポップアップで行われるため、**login** メソッドはボタンのクリック イベントから呼び出す必要があります。そうしないと、多くのブラウザーではログイン ウィンドウが表示されません。

1.  プロジェクト ファイル index.html を開き、H1 要素を探して、その下に次のコード スニペットを追加します。

        <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

    これで、ページからモバイル サービスにログインできるようになります。

2.  app.js ファイルの末尾で refreshTodoItems 関数を呼び出しているコード行を探し、次のコードに置き換えます。

        function refreshAuthDisplay() {
            var isLoggedIn = client.currentUser !== null;
            $("#logged-in").toggle(isLoggedIn);
            $("#logged-out").toggle(!isLoggedIn);

            if (isLoggedIn) {
                $("#login-name").text(client.currentUser.userId);
                refreshTodoItems();
            }
        }

        function logIn() {
            client.login("facebook").then(refreshAuthDisplay, function(error){
                alert(error);
            });
        }

        function logOut() {
            client.logout();
            refreshAuthDisplay();
            $('#summary').html('<strong>You must login to access data.</strong>');
        }

        // On page init, fetch the data and set up event handlers
        $(function () {
            refreshAuthDisplay();
            $('#summary').html('<strong>You must login to access data.</strong>');          
            $("#logged-out button").click(logIn);
            $("#logged-in button").click(logOut);
        });

    これで、認証プロセスを処理する関数のセットが作成されます。ユーザーは、Facebook ログインを使用して認証されます。Facebook 以外の ID プロバイダーを使用している場合は、上の **login** メソッドに渡される値を *microsoftaccount*、*facebook*、*twitter*、*google* または *aad* のいずれかに変更します。

    > [WACOM.NOTE] PhoneGap アプリでは、次のプラグインもプロジェクトに追加する必要があります。
    >
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git`
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git`
    >
    > </p>

3.  アプリケーションが実行されているブラウザーに戻り、ページを更新します。

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

    > [WACOM.NOTE] Internet Explorer を使用している場合は、ログイン後に`Cannot reach window opener. It may be on a different Internet Explorer zone` を受信することがあります。これは、ポップアップが localhost (イントラネット) とは異なるセキュリティ ゾーン (インターネット) で実行されているためです。このことがアプリケーションに影響するのは、localhost を使用する開発時だけです。回避策として、**[インターネット オプション]** の **[セキュリティ]** タブを開き、**[ローカル イントラネット]**、**[サイト]** の順にクリックして、**[イントラネットのネットワークを自動的に検出する]** をオフにします。テストが終了したら、この設定を必ず元に戻します。

## <a name="next-steps"> </a>次のステップ

[スクリプトを使用したユーザーの認証][スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス][モバイル サービス HTML/JavaScript の使用方法の概念リファレンス] で、HTML/JavaScript でモバイル サービスを使用する方法について説明します。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
  [テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
  [アプリケーションに認証を追加する]: #add-authentication
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-html-get-started
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [スクリプトを使用したユーザーの認証]: /ja-jp/documentation/articles/mobile-services-html-authorize-users-in-scripts
  [モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/documentation/articles/mobile-services-html-how-to-use-client-library
