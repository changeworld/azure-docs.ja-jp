
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL を使用して Microsoft Graph API のトークンを取得する

このセクションでは、MSAL を使用して Microsoft Graph API のトークンを取得します。

1.  *MainWindow.xaml.cs* ファイルで、クラスに MSAL の参照を追加します。

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. `MainWindow` クラス コードを次のコードに置き換えます。

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] _scopes = new string[] { "user.read" };

        public MainWindow()
        {
            InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>詳細情報
#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する
`AcquireTokenAsync` メソッドを呼び出すと、ユーザーにサインインを求めるウィンドウが表示されます。 アプリケーションは通常、ユーザーが保護されたリソースに初めてアクセスするときに、対話形式でユーザーにサインインを求めます。 また、自動でのトークンの取得に失敗したとき (ユーザーのパスワードが期限切れになっている場合など) にもサインインを求めることがあります。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する
`AcquireTokenSilentAsync` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `AcquireTokenAsync` が実行された後、保護されたリソースへのアクセスに使用するトークンを取得するには、通常、`AcquireTokenSilentAsync` メソッドを使用します。トークンの要求や更新などの後続の呼び出しが自動で行われるからです。

`AcquireTokenSilentAsync` メソッドは最終的に失敗します。 失敗する原因としては、ユーザーがサインアウトした、別のデバイスでパスワードを変更したなどが挙げられます。 ユーザーの操作によって解決できる問題が MSAL によって検出された場合、MSAL は `MsalUiRequiredException` 例外を発行します。 アプリケーションでは、この例外を 2 つの方法で処理できます。

* すぐに `AcquireTokenAsync` を呼び出す。 この呼び出しにより、ユーザーにサインインを求めます。 ユーザーが使用できるオフライン コンテンツが含まれていないオンライン アプリケーションの場合は、通常、この方法で処理します。 このガイド付きのセットアップによって生成されるサンプルは、このパターンを使用します。サンプルの初回実行時に、実際の動作を確認できます。 
    * アプリケーションはユーザーによって使用されたことがないため、`PublicClientApp.Users.FirstOrDefault()` には null 値が含まれ、`MsalUiRequiredException` 例外がスローされます。 
    * サンプルのコードでは、`AcquireTokenAsync` を呼び出してユーザーにサインインを求めることにより、この例外を処理します。

* ユーザーに対して視覚的に対話形式でのサインインを求めることで、ユーザーが適切なタイミングでサインインできるようにする。 または、アプリケーションが後で `AcquireTokenSilentAsync` を再試行する。 アプリケーションにユーザーが使用できるオフライン コンテンツが含まれている場合など、アプリケーションの他の機能を中断せずに使用できる場合は、この方法がよく使用されます。 この場合、ユーザーは、保護されたリソースにアクセスしたり、古くなった情報を更新したりするためにサインインするタイミングを決定できます。 また、アプリケーションで、ネットワークが一時的に使用できなくなってから回復した場合に `AcquireTokenSilentAsync` を再試行できます。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

次の新しいメソッドを `MainWindow.xaml.cs` に追加します。 このメソッドは、Authorize ヘッダーを使用した Graph API に対する `GET` 要求の実行に使用されます。

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このサンプル アプリケーションでは、`GetHttpContentWithToken` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行し、呼び出し元にその内容を返します。 このメソッドは、取得したトークンを HTTP Authorization ヘッダーに追加します。 このサンプルで使用するリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>ユーザーをサインアウトさせるメソッドを追加する

ユーザーをサインアウトさせるには、次のメソッドを `MainWindow.xaml.cs` ファイルに追加します。

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>ユーザーのサインアウトに関する詳細情報

`SignOutButton_Click` メソッドは、MSAL ユーザー キャッシュからユーザーを削除します。これは実質的に MSAL に現在のユーザーを忘れさせることになり、以降の要求が対話形式で行われる場合のみトークンの取得が成功します。

このサンプルのアプリケーションは単一ユーザーに対応していますが、MSAL は複数のアカウントを使用して同時にサインインするケースに対応しています。 たとえば、メール アプリケーションで 1 人のユーザーが複数のアカウントを持っているケースです。
<!--end-collapse-->

## <a name="display-basic-token-information"></a>基本的なトークン情報を表示する

トークンについての基本的な情報を表示するには、次のメソッドを *MainWindow.xaml.cs* ファイルに追加します。

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>詳細情報

Microsoft Graph API の呼び出しに使用するアクセス トークンに加えて、MSAL はユーザーのサインイン後に ID トークンも取得します。 このトークンには、ユーザー関連情報の少量のサブセットが含まれています。 `DisplayBasicTokenInfo` メソッドは、このトークンに含まれている基本的な情報を表示します。 たとえば、トークンの有効期限やアクセス トークンそのものを表す文字列に加えて、ユーザーの表示名や ID です。 *[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンを複数回押すと、後の要求で同じトークンが再利用されてことが確認できます。 また、MSAL がトークンの更新時期だと判断したときに、有効期限が延長されることも確認できます。
<!--end-collapse-->

