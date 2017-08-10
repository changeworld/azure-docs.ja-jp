
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Microsoft Authentication Library (MSAL) を使用して Microsoft Graph API のトークンを取得する

次のコードを `index.html` の `<body>` タグ内に追加します。

```html
<script type="text/javascript">

    var graphAPIMeEndpoint = "https://graph.microsoft.com/v1.0/me";
    var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

    // Initialize application
    var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID);

    // Set redirect URI
    userAgentApplication.redirectUri = msalconfig.redirectUri;

    displayUserInfo();

    function displayUserInfo() {
        var user = userAgentApplication.getUser();
        if (user) {
            // Display the user info
            var userInfoElement = document.getElementById("userInfo");
            userInfoElement.parentElement.classList.remove("hidden");
            userInfoElement.innerHTML = JSON.stringify(user, null, 4);

            // Show Sign-Out button
            document.getElementById("signOutButton").classList.remove("hidden");
        }
    }

    function callGraphAPI() {
        if (userAgentApplication.getAllUsers().length === 0) {
            userAgentApplication.loginPopup()
                .then(function (token) {
                    console.log(token);
                    displayUserInfo();
                    callGraphAPI();
                }, function (error) {
                    showError("login", error, document.getElementById("errorMessage"));
                });
        } else {
            var responseElement = document.getElementById("graphResponse");
            responseElement.parentElement.classList.remove("hidden");
            responseElement.innerText = "Calling Graph ...";
            callWebApiWithScope(graphAPIMeEndpoint,
                graphAPIScopes,
                responseElement,
                document.getElementById("errorMessage"),
                document.getElementById("accessToken"));
        }
    }

    function callWebApiWithScope(endpoint, scope, responseElement, errorElement, showTokenElement) {
        userAgentApplication.acquireTokenSilent(scope)
            .then(function (token) {
                callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
            }, function (error) {
                if (error.indexOf("interaction_required" !== -1)) {
                    userAgentApplication.acquireTokenPopup(scope).then(function(token) {
                            callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement);
                        },
                        function(error) {
                            showError(endpoint, error, errorElement);
                        });
                } else {
                    showError(endpoint, error, errorElement);
                }
            });
    }

    function showAPIResponse(data, token, responseElement, showTokenElement) {
        console.log(data);
        responseElement.innerHTML = JSON.stringify(data, null, 4);
        if (showTokenElement) {
            showTokenElement.parentElement.classList.remove("hidden");
            showTokenElement.innerHTML = token;
        }
    }

    function showError(endpoint, error, errorElement) {
        console.error(error);
        var formattedError = JSON.stringify(error, null, 4);
        if (formattedError.length < 3) {
            formattedError = error;
        }
        errorElement.innerHTML = "Error calling " + endpoint + ": " + formattedError;
    }
</script>
```

<!--start-collapse-->
### <a name="more-information"></a>詳細情報
#### <a name="sign-in-the-user"></a>ユーザーのサインイン
最初に [Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\) ボタンをクリックすると、ユーザーがサインインするための `loginPopup` が実行されます。 このメソッドによって、ユーザーにサインインを促すブラウザー ウィンドウが表示されます。 サインインが成功した結果として、MSAL によってユーザー情報がキャッシュされ、トークンが返されます。 このトークンは *ID トークン*と呼ばれ、ユーザー表示名などのユーザーに関する基本情報が含まれます。 何らかの目的のためにこのトークンが提供する任意のデータを使用する予定がある場合、アプリケーションの有効なユーザーに対してトークンが発行されたことを保証するために、このトークンがバックグラウンド サーバーで確実に検証される必要があります。

このガイドで生成する SPA では、ID トークンを直接活用していません。代わりに、`acquireTokenSilent` および/または `acquireTokenPopup` を呼び出して、Microsoft Graph API の照会に使用される*アクセス トークン*を取得します。 ID トークンを検証するサンプルが必要な場合は、[こちら](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 sample") にある GitHub のサンプル アプリケーションを確認してください。このサンプルでは、トークンの検証に ASP.NET Web API を使用しています。

#### <a name="getting-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する
`acquireTokenPopup(scope)` メソッドを呼び出すと、ユーザーにサインインを求めるブラウザー ウィンドウが表示されます。 アプリケーションは通常、ユーザーが保護されたリソースに初めてアクセスするとき、または自動でのトークンの取得に失敗したとき (ユーザーのパスワードが期限切れになっている場合など) に、対話形式でユーザーにサインインを求めます。

#### <a name="getting-a-user-token-silently"></a>ユーザー トークンを自動で取得する
` acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `loginPopup` が実行されたあと、その後の呼び出しでは、保護されたリソースにアクセスするトークンを取得するために `acquireTokenSilent` メソッドが通常使用されます。トークンの要求や更新のための呼び出しは自動で行われるためです。
ユーザーが別のデバイスでパスワードを変更した場合などには、`acquireTokenSilent` は最終的に失敗します。 アプリケーションでは、この例外を 2 つの方法で処理できます。

1.  すぐに `acquireTokenPopup` を呼び出し、ユーザーにサインインを求める。 オンライン アプリケーション (ユーザーが使用できるオフライン コンテンツが含まれていないアプリケーション) の場合は、通常、この方法で処理します。 このガイドの設定で生成したサンプルでは、このパターンを使用しています。
2. ユーザーに対してアプリケーションで視覚的に対話形式でのサインインを求めることで、ユーザーが適切なタイミングでサインインできるようにし、アプリケーションがあとで `acquireTokenSilent` を再試行できるようにする。 アプリケーションにユーザーが使用できるオフライン コンテンツが含まれている場合など、アプリケーションの他の機能を中断せずに使用できる場合は、通常、この方法で処理します。 この方法では、ユーザーは保護されたリソースにサインインしたり、古い情報を更新したりするタイミングを決めることができます。また、アプリケーションで、ネットワークが一時的に使用できなくなってから回復した場合に `acquireTokenSilent` を再試行できます。

`acquireTokenSilent` が失敗する別のシナリオは、呼び出しによって要求されたスコープにユーザーがまだ同意していない場合です。 たとえば、このガイドで生成したアプリケーションをユーザーが初めて実行すると、*user.read* スコープがパラメーターとして提供されます。 サインイン ユーザーがプロファイルの読み取りに同意していないため、`acquireTokenSilent` が `interaction_required` を含む例外を生成し、通常は問題に対処する方法として `acquireTokenPopup` の呼び出しを指示します。 この場合、ポップアップ ウィンドウが作成され、要求されたスコープへの同意画面が表示されます。

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

次のコードを index.html の `<body>` タグ内に追加します。

```html
 <script type="text/javascript">
function callWebApiWithToken(endpoint, token, responseElement, errorElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    // Note that fetch API is not available in all browsers
    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showAPIResponse(data, token, responseElement, showTokenElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        showError(endpoint, data, errorElement);
                    })
                    .catch(function (error) {
                        showError(endpoint, error, errorElement);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error, errorElement);
        });
}
</script>
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このガイドで作成したサンプル アプリケーションでは、`callWebApiWithToken()` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行し、呼び出し元にその内容を返します。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー* に追加します。 このガイドで作成したサンプル アプリケーションのリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>ユーザーをサインアウトするメソッドの追加

次のコードを index.html の `<body>` タグ内に追加します。

```html
 <script type="text/javascript">
    function signOut() {
        userAgentApplication.logout();
    }
</script>
```
