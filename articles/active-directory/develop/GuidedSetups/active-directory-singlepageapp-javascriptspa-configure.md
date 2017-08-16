
### <a name="create-an-application-express"></a>アプリケーションの作成 (簡易)
次の手順に従って Microsoft アプリケーション登録ポータルでアプリケーションを登録する必要があります。

1.  [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)でアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します
3.  *ガイド付きセットアップ*のオプションがオンになっていることを確認します
4.  手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>ソリューションへのアプリケーション登録情報の追加 (上級)

1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します
2. アプリケーションの名前とお使いのメール アドレスを入力します 
3. *ガイド付きセットアップ*のオプションがオフになっていることを確認します
4.  [`Add Platform`] \(プラットフォームの追加) をクリックし、[`Web`] を選択します
5. リダイレクト URL をアプリケーションに追加します。 リダイレクト URL は、Web サーバーに基づいた `index.html` ページの URL です
6. *[保存]*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Visual Studio で、SSL を使用してリダイレクト URL を取得する手順
> Visual Studio を使用している場合は、SSL を使用するようにプロジェクトを構成した後、以下の手順で、SSL URL を使用してアプリケーションの登録情報を構成します。
> 1.    ソリューション エクスプローラーで、プロジェクト選択して [`Properties`] \(プロパティ) ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は F4 キーを押します)。
> 2.    `SSL Enabled` を `True` に変更します。
> 3.    `SSL URL` からクリップボードに値をコピーします<br/> ![プロジェクトのプロパティ](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    `Project` メニュー、`{Project} Properties...` の順に選択します ({Project} はプロジェクト名です)
> 5.    `Web` タブを開きます
> 6.    `SSL URL` の値を`Project Url` フィールドに貼り付けます
> 7.    アプリケーション登録ポータルに戻り、リダイレクト URL として `Redirect URL` の値を貼り付け、*[保存]* をクリックします


#### <a name="configure-your-javascript-spa-application"></a>JavaScript SPA アプリケーションの構成

1.  アプリケーション登録情報を含む `msalconfig.js` という名前のファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして `Add` > `New Item` > `JavaScript File` を選択します。 これに `msalconfig.js` という名前を付けます。
2.  次のコードを `msalconfig.js` ファイルに追加します。

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
<code>Enter_the_Application_Id_here</code> を、さきほど登録したアプリケーション ID に置き換えます
</li>
</ol>