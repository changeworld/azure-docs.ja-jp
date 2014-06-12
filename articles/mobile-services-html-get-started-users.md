<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="認証の使用 (HTML5)" pageTitle="認証の使用 (HTML5) | モバイル デベロッパー センター" metaKeywords="" description="モバイル サービスを使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを介して HTML アプリのユーザーを認証する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスでの認証の使用" authors=""  solutions="" writer="glenga" manager="" editor=""  />




# モバイル サービスでの認証の使用
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows ストア C#">Windows ストア C##</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-html" title="HTML" class="current">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>


このトピックでは、HTML アプリケーションから Azure のモバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

<h2><a name="register"></a><span class="short-header">アプリの登録</span>アプリを認証に登録し、モバイル サービスを構成する</h2>

ユーザーを認証できるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、プロバイダーによって生成されたクライアント シークレットをモバイル サービスに登録する必要があります。

1. [Azure 管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

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

4.管理ポータルに戻って **[識別]** タブをクリックし、アプリケーションの識別子と、ID プロバイダーから取得した共有シークレット値を入力して、**[保存]** をクリックします。

   	![][13]

これで、モバイル サービスとアプリケーションの両方が、選択した認証プロバイダーと連係するように構成されました。

<h2><a name="permissions"></a><span class="short-header">アクセス許可の制限</span>アクセス許可を認証されたユーザーだけに制限する</h2>

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][14]

2. **[アクセス許可]** タブで、すべてのアクセス許可を **[認証されたユーザーのみ]** に設定し、**[保存]** をクリックします。これにより、**TodoItem** テーブルに対するすべての操作には、認証されたユーザーが必要になります。また、次のチュートリアルのスクリプトは、匿名ユーザーの可能性を考慮する必要がなくなるため、簡素化されます。

   	![][15]

3. app ディレクトリで、**server** サブフォルダーから次のいずれかのコマンド ファイルを実行します。

	+ **launch-windows** (Windows コンピューター)
	+ **launch-mac.command** (Mac OS X コンピューター)
	+ **launch-linux.sh** (Linux コンピューター)

	<div class="dev-callout"><b>注</b>
		<p>Windows コンピューターでは、PowerShell からスクリプトの実行の確認を求められた場合は、「`R`」と入力します。Web ブラウザーでは、インターネットからダウンロードしたスクリプトであるため、実行しないよう警告されることがあります。その場合は、ブラウザーがスクリプトの読み込みを開始するよう要求する必要があります。</p>
	</div>

	これにより、新しいアプリケーションをホストする Web サーバーがローカル コンピューター上で起動します。

2. Web ブラウザーで URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> を開いて、アプリケーションを開始します。

	データの読み込みに失敗します。この問題は、非認証ユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、_TodoItem_ テーブルでは認証が要求されるために発生します。

3. (省略可能) Web ブラウザーのスクリプト デバッガーを開き、ページを再読み込みします。アクセス拒否エラーが発生することを確認します。

次に、モバイル サービスのリソースを要求する前に認証を許可するようにアプリケーションを更新します。

<h2><a name="add-authentication"></a><span class="short-header">認証の追加</span>アプリに認証を追加する</h2>

<div class="dev-callout"><b>メモ</b>
		<p>ログインはポップアップで行われるため、<strong>login</strong> メソッドはボタンのクリック イベントから呼び出す必要があります。そうしないと、多くのブラウザーではログイン ウィンドウが表示されません。</p>
</div>

1. プロジェクト ファイル index.html を開き、H1 要素を探して、その下に次のコード スニペットを追加します。

	    <div id="logged-in">
            現在のログイン名: <span id="login-name"></span>。
            <button id="log-out">ログアウト</button>
        </div>
        <div id="logged-out">
            現在ログインしていません。
            <button>ログイン</button>
        </div>

	これで、ページからモバイル サービスにログインできるようになります。

2. app.js ファイルの末尾で refreshTodoItems 関数を呼び出しているコード行を探し、次のコードに置き換えます。
	
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

    これで、認証プロセスを処理する関数のセットが作成されます。ユーザーは、Facebook ログインを使用して認証されます。

    <div class="dev-callout"><b>メモ</b>
	<p>Facebook 以外の ID プロバイダーを使用している場合は、上の <strong>login</strong> メソッドに渡す値を <em>microsoftaccount</em>、<em>facebook</em>、<em>twitter</em>、<em>google</em> のいずれかに変更します。</p>
    </div>

9. アプリケーションが実行されているブラウザーに戻り、ページを更新します。

   ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

	<div class="dev-callout"><b>メモ</b>
		<p>Internet Explorer を使用している場合は、ログイン後に、<code>Cannot reach window opener. It may be on a different Internet Explorer zone</code> というエラーが表示されることがあります。これは、ポップアップが localhost (イントラネット) とは異なるセキュリティ ゾーン (インターネット) で実行されているためです。このことがアプリケーションに影響するのは、localhost を使用する開発時だけです。回避策として、<strong>[インターネット オプション]</strong> の <strong>[セキュリティ]</strong> タブを開き、<strong>[ローカル イントラネット]</strong>、<strong>[サイト]</strong> の順にクリックして、<strong>[イントラネットのネットワークを自動的に検出する]</strong> をオフにします。テストが終了したら、この設定を必ず元に戻します。</p>
	</div>

## <a name="next-steps"> </a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス] で、HTML/JavaScript でモバイル サービスを使用する方法について説明します。

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[次のステップ]:#next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Microsoft アカウント ログイン]: /ja-jp/develop/mobile/how-to-guides/register-for-microsoft-authentication
[Facebook ログイン]: /ja-jp/develop/mobile/how-to-guides/register-for-facebook-authentication
[Twitter ログイン]: /ja-jp/develop/mobile/how-to-guides/register-for-twitter-authentication
[Google ログイン]: /ja-jp/develop/mobile/how-to-guides/register-for-google-authentication
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-html
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-html
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-html

[Azure 管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス HTML/JavaScript の使用方法の概念リファレンス]: /ja-jp/develop/mobile/how-to-guides/work-with-html-js-client

