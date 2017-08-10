
## <a name="setting-up-your-web-server-or-project"></a>Web サーバーまたはプロジェクトの設定

> 代わりにこのサンプルの Visual Studio プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/master.zip)したら[構成](#create-an-application-express)手順に進み、実行前にコード サンプルを構成します。

## <a name="prerequisites"></a>前提条件
このガイド付きセットアップを実行するには、[http-server](https://www.npmjs.com/package/http-server/)、[Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core) または [Visual Studio 2017](https://www.visualstudio.com/downloads/) と統合された IIS Express などのローカル Web サーバーが必要です。 

このガイドの手順は Visual Studio 2017 に基づいていますが、他の開発環境または HTML/JavaScript エディターでも自由に使用できます。


## <a name="create-your-project-visual-studio-only"></a>プロジェクトを作成する (Visual Studio のみ)

Visual Studio を使用して新しいプロジェクトを作成している場合は、以下の手順に従って新しい Visual Studio ソリューションを作成します。
1.  Visual Studio で、`File` > `New` > `Project` を選択します。
2.  `Visual C#\Web` で、`ASP.NET Web Application (.NET Framework)` を選択します。
3.  アプリケーションに名前を付けて、*[OK]* をクリックします。
4.  `New ASP.NET Web Application` で、`Empty` を選択します。


## <a name="create-your-single-page-applications-ui"></a>Single Page Application の UI を作成する
1.  JavaScript SPA の index.html ファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして `Add` > `New Item` > `HTML page` を選択し、これに index.html の名前を付けます
2.  次のコードをページに追加します。
```html
<!DOCTYPE html>
<html>
    <head>
        <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
        <title>JavaScript SPA Guided Setup</title>
    </head>
    <body style="margin: 40px">
        <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphAPI()">Call Microsoft Graph API</button>
        <div id="errorMessage" class="text-danger"></div>
        <div class="hidden">
            <h3>Graph API Call Response</h3>
            <pre class="well" id="graphResponse"></pre>
        </div>
        <div class="hidden">
            <h3>Access Token</h3>
            <pre class="well" id="accessToken"></pre>
        </div>
        <div class="hidden">
            <h3>ID Token Claims</h3>
            <pre class="well" id="userInfo"></pre>
        </div>
        <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

        <script src="//secure.aadcdn.microsoftonline-p.com/lib/0.1.1/js/msal.min.js"></script>
        <script type="text/javascript" src="msalconfig.js"></script>
    
        <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
        <script src="//cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
        <script src="//cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>
    </body>
</html>
````
