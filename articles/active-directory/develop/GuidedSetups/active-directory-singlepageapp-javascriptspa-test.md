
## <a name="test-your-code"></a>コードのテスト

Visual Studio を使用している場合は、`F5` を押してプロジェクトを実行します。 ブラウザーが開き、*http://localhost:{ポート}* に移動します。*[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンが表示されます。

Visual Studio を使用していない場合は、Web サーバーが起動し、JavaScript Web アプリケーションを含むフォルダーが Web サーバーに構成されていることを確認してください。 ブラウザーを開き、「*http://localhost:{port}/path*」と入力します。*port* は Web サーバーがリッスンしているポート、*path* は index.html のパスです。

*[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンをクリックします。 初めての場合は、ユーザーにサインインを求めるポップアップ ウィンドウが表示されます。
 
![サンプル画面](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>同意
アプリケーションに初めてサインインすると、下記のような同意の画面が表示され、明示的に承認する必要があります。

 ![同意画面](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)

Microsoft Graph API のクエリを実行しているため、別の同意ページが表示される場合があります。 これは、アプリケーションから要求されるスコープごとに同意が必要な*動的同意*が原因で発生します。 このガイドで生成されたサンプル アプリケーションでは *user.read* のスコープが提供されるため、ユーザーのプロファイルを読み込むにはこのアプリケーションに対する同意が必要です。

> [!IMPORTANT]
> 現時点では、*msal* JavaScript の既知の問題により、Chrome や Firefox などのブラウザーでポップアップ ブロックを無効にする必要があるため、*動的同意*は正しく動作します。 初めてユーザーが `acquireTokenPopup` を使用して Microsoft Graph API を呼び出すときに、ポップアップ ブロックを無効にする必要があります。

### <a name="expected-results"></a>予想される結果
Microsoft Graph API の呼び出しと応答によって返されるユーザー プロファイル情報が表示されます。
 
 ![結果](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

取得したトークンに関する基本情報は *[アクセス トークン]* と *[ID Token Claims]\(ID トークン要求\)* のボックスにも表示されます。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API は、ユーザーのプロファイルを読み込むためにスコープ `user.read` を必要とします。 このスコープは既定では、登録ポータルに登録されるすべてのアプリケーションで自動的に追加されます。 バックエンド サーバーのカスタム API に加え、他のいくつかの Microsoft Graph API は、場合によっては、追加のスコープを必要とします。 たとえば、Microsoft Graph の場合、ユーザーの予定表を一覧表示するにはスコープ `Calendars.Read` が必要です。 アプリケーションのコンテキストでユーザーの予定表にアクセスするには、委任されたアクセス許可 `Calendars.Read` をアプリケーション登録の情報に追加し、`acquireTokenSilent` 呼び出しにスコープ `Calendars.Read` を追加する必要があります。 スコープの数を増やすと、ユーザーは追加で同意を求められる可能性があります。

バックエンド API でスコープを必要としない (推奨されません) 場合は、`acquireTokenSilent` や `acquireTokenPopup` の呼び出し内のスコープとして `clientId` を使用できます。

<!--end-collapse-->
