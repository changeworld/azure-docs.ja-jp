## <a name="add-the-applications-registration-information-to-your-app"></a>アプリへのアプリケーション登録情報の追加

この手順では、アプリケーション登録情報のリダイレクト URL を構成してから、アプリケーション ID を JavaScript SPA アプリケーションに追加する必要があります。

### <a name="configure-redirect-url"></a>リダイレクト URL の構成

[`Redirect URL`] フィールドを Web サーバーに基づく index.html ページの URL に構成し、*[更新]* をクリックします。


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio で、リダイレクト URL を取得する手順
> リダイレクト URL を取得するには:
> 1.    *ソリューション エクスプローラー*で、プロジェクト選択して [`Properties`] ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は `F4` を押します)
> 2.    `URL` からクリップボードに値をコピーします<br/> ![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    このページの上部にある `Redirect URL` に値を貼り付け、`Update` をクリックします

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Python のリダイレクト URL の設定
> Python の場合、コマンド ラインで Web サーバー ポートを設定できます。 このガイドの設定では、参照用にポート 8080 を使用しますが、他の利用可能なポートを自由にお使いいただけます。 いずれの場合も、下記の手順に従って、アプリケーションの登録情報にリダイレクト URL を設定します。<br/>
> `http://localhost:8080/` を、このページ上部の `Redirect URL` に設定するか、カスタム TCP ポートを使用している場合 (*[ポート]* = カスタム TCP ポートの番号) は `http://localhost:[port]/` を使用して、[更新] をクリックします

### <a name="configure-your-javascript-spa-application"></a>JavaScript SPA アプリケーションの構成

1.  アプリケーション登録情報を含む `msalconfig.js` という名前のファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして `Add` > `New Item` > `JavaScript File` を選択します。 これに `msalconfig.js` という名前を付けます。
2.  次のコードを `msalconfig.js` ファイルに追加します。

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
