
## <a name="add-the-applications-registration-information-to-your-app"></a>アプリへのアプリケーション登録情報の追加

この手順では、アプリケーション登録情報のリダイレクト URL を構成してから、アプリケーション ID を JavaScript SPA アプリケーションに追加する必要があります。

### <a name="configure-redirect-url"></a>リダイレクト URL の構成

上記の [`Redirect URL`] フィールドを Web サーバーに基づく index.html ページの URL に構成し、*[更新]*をクリックします。

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url-using-ssl"></a>Visual Studio で、SSL を使用してリダイレクト URL を取得する手順
> Visual Studio を使用している場合は、SSL を使用するようにプロジェクトを構成した後、以下の手順で、SSL URL を使用してアプリケーションの登録情報を構成します。
> 1.    ソリューション エクスプローラーで、プロジェクト選択して [`Properties`] \(プロパティ) ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は F4 キーを押します)。
> 2.    [`SSL Enabled`] を [`True`] に変更します。
> 3.    [`SSL URL`] からクリップボードに値をコピーします。<br/> ![プロジェクトのプロパティ](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 4.    [`Project`] メニュー、[`{Project} Properties...`] の順に選択します ({Project} はプロジェクト名です)
> 5.    [`Web`] タブを開きます
> 6.    [`SSL URL`] の値を [`Project Url`] フィールドに貼り付けます
> 7.    このページの上部にある [`Redirect URL`] フィールドにも値を貼り付け、*[更新]* をクリックします


### <a name="configure-your-javascript-spa-application"></a>JavaScript SPA アプリケーションの構成

1.  アプリケーション登録情報を含む `msalconfig.js` という名前のファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして [`Add`] > [`New Item`] > [`JavaScript File`] を選択します。 これに `msalconfig.js` という名前を付けます。
2.  次のコードを `msalconfig.js` ファイルに追加します。

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
