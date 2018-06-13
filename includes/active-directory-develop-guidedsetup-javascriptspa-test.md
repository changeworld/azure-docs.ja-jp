## <a name="test-your-code"></a>コードのテスト

### <a name="test-with-visual-studio"></a>Visual Studio でのテスト
Visual Studio を使用している場合は、 **F5** キーを押してプロジェクトを実行します。 ブラウザーで http://<span></span>localhost:{ポート} の場所を開くと、**[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** ボタンが表示されます。

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Python または別の Web サーバーでのテスト
Visual Studio を使用していない場合は、お使いの Web サーバーが開始されていることを確認します。 **index.html** ファイルの場所に基づく TCP ポートをリッスンするように、サーバーを構成します。 Python の場合、アプリケーション フォルダーからコマンド プロンプトのターミナルを実行することで、ポートのリッスンを開始します。
 
```bash
python -m http.server 8080
```
ブラウザーを開き、「http://<span></span>localhost:8080」または「http://<span></span>localhost:{ポート}」と入力します (ここで、**"ポート"** は Web サーバーがリッスンしているポートです)。 index.html ファイルのコンテンツと **[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** ボタンが表示されます。

## <a name="test-your-application"></a>アプリケーションのテスト

ブラウザーに index.html ファイルが読み込まれた後、**[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** を選択します。 アプリケーションを初めて実行すると、ブラウザーは Microsoft Azure Active Directory (Azure AD) v2.0 のエンドポイントにリダイレクトされ、サインインを求めるメッセージが表示されます。
 
![JavaScript SPA アカウントにサインインする](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションに初めてサインインするとき、アプリケーションがプロファイルにアクセスし、サインインできることへの同意を求められます。

![アプリケーションによるアクセスに同意する](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>アプリケーションの結果を表示する
サインインした後、**[Graph API Call Response]\(Graph API 呼び出しの応答\)** ボックスにユーザー プロファイル情報が表示されます。
 
![Microsoft Graph API 呼び出しから期待される結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

また、**[アクセス トークン]** および **[ID Token Claims]\(ID トークンの要求\)** ボックスで取得したトークに関する基本情報も確認できます。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための **user.read** スコープが必要です。 このスコープは既定で、登録ポータルに登録されているすべてのアプリケーションで自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 Microsoft Graph API には、ユーザーの予定表を表示するための **Calendars.Read** スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、**Calendars.Read** の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、**Calendars.Read** スコープを **acquireTokenSilent** 呼び出しに追加します。 

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

バックエンド API がスコープを必要としない場合は (非推奨)、**acquireTokenSilent** および **acquireTokenRedirect** 呼び出しのスコープとして **clientId** を使用できます。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
