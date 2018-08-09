---
title: Azure AD v2 UWP の概要 | Microsoft Docs
description: ユニバーサル Windows プラットフォーム アプリケーション (UWP) で、Azure Active Directory v2 エンドポイントによるアクセス トークンを必要とする API を呼び出す方法
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 48d03b590d718cf82d692dc177a69f6d54de43d1
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495916"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>ユニバーサル Windows プラットフォーム アプリケーション (XAML) から Microsoft Graph API を呼び出す

このガイドでは、ネイティブのユニバーサル Windows プラットフォーム (UWP) アプリケーションがアクセス トークンを要求し、Microsoft Graph API を呼び出す方法について説明します。 このガイドは、Azure Active Directory v2 エンドポイントのアクセス トークンを必要とする他の API にも適用されます。

このガイドの最後に、アプリケーションは個人のアカウントを使用して、保護されている API を呼び出します。 例としては、outlook.com、live.com などがあります。 アプリケーションは、Azure Active Directory を持つ会社または組織の職場および学校のアカウントも呼び出します。

>[!NOTE]
> このガイドでは、ユニバーサル Windows プラットフォーム開発がインストールされた Visual Studio 2017 が必要です。 ユニバーサル Windows プラットフォーム アプリを開発するために Visual Studio をダウンロードして構成する手順については、「[準備](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)」を参照してください。

### <a name="how-this-guide-works"></a>このガイドの利用法

![このガイドのしくみの図](./media/tutorial-v2-windows-uwp/uwp-intro.png)

このガイドでは、Azure Active Directory v2 エンドポイントからトークンを受け取る Microsoft Graph API または Web API に対してクエリを実行するサンプル UWP アプリケーションを作成します。 このシナリオでは、トークンは Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

### <a name="nuget-packages"></a>NuGet パッケージ

このガイドでは、次の NuGet パッケージを使用します。

|ライブラリ|説明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|


## <a name="set-up-your-project"></a>プロジェクトの設定

このセクションでは、Windows デスクトップ .NET アプリケーション (XAML) と "*Microsoft でサインイン*" を統合するための詳細な手順を説明します。 その後、トークンを必要とする Web API (Microsoft Graph API など) に対してクエリを実行することができます。

このガイドでは、Graph API に対してクエリを実行するためのボタン、サインアウト ボタン、および呼び出しの結果を表示するテキスト ボックスを表示するアプリケーションを作成します。

>[!NOTE]
> 代わりにこのサンプルの Visual Studio プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)し、[アプリケーション登録](#register-your-application "アプリケーション登録の手順")の手順までスキップして、実行前にコード サンプルを構成します。


### <a name="create-your-application"></a>アプリケーションの作成
1. Visual Studio で、**[ファイル]** > **[新規]** > **[プロジェクト]** の順に選択します。
2. **[テンプレート]** で **[Visual C#]** を選択します。
3. **[Blank App (Universal Windows)] (空のアプリ (ユニバーサル Windows))** を選択します。
4. アプリの名前を指定し、**[OK]** を選択します。
5. メッセージが表示されたら、"**ターゲット**" と "**最小**" バージョンに任意のバージョンを選択し、**[OK]** を選択します。

    >![最小バージョンとターゲット バージョン](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>プロジェクトへの Microsoft Authentication Library の追加
1. Visual Studio で、**[ツール]** > **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。
2. 以下のコマンドをコピーして、**パッケージ マネージャー コンソール**のウィンドウに貼り付けます。

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> このコマンドを実行すると、[Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) がインストールされます。 MSAL は、Azure Active Directory v2 で保護された API にアクセスするユーザー トークンを取得、キャッシュ、および更新します。

## <a name="initialize-msal"></a>MSAL の初期化
この手順は、トークンの処理など、MSAL とのやり取りを処理するクラスを作成するために役立ちます。

1. **App.xaml.cs** ファイルを開き、クラスに MSAL の参照を追加します。

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. 次の 2 行をアプリのクラス (<code>sealed partial class App : Application</code> ブロック内) に追加します。

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>アプリケーションの UI の作成

**MainPage.xaml** ファイルは、プロジェクト テンプレートの一部として自動的に作成されます。 このファイルを開き、以下の手順を実行します。

* アプリケーションの **Grid** ノードを次のコードに置き換えます。

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>MSAL を使用して Microsoft Graph API のトークンを取得する

このセクションでは、MSAL を使用して Microsoft Graph API のトークンを取得する方法を示します。

1.  **MainPage.xaml.cs** で、クラスに MSAL の参照を追加します。

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. <code>MainPage</code> クラスのコードを次のコードに置き換えます。

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

### <a name="more-information"></a>詳細情報
#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する
`AcquireTokenAsync` メソッドを呼び出すと、ユーザーにサインインを求めるウィンドウが表示されます。 通常、アプリケーションは、ユーザーが保護されたリソースに初めてアクセスするときに、対話形式でユーザーにサインインを求めます。 また、自動でのトークンの取得に失敗した場合にも、ユーザーはサインインする必要があります。 たとえば、ユーザーのパスワードが期限切れになっている場合などです。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する
`AcquireTokenSilentAsync` メソッドは、ユーザーの操作なしでトークンの取得と更新を処理します。 `AcquireTokenAsync` が初めて実行され、資格情報の入力を求めるメッセージが表示された後、後続の呼び出しでトークンを要求するには、`AcquireTokenSilentAsync` メソッドを使用する必要があります。これはトークンを自動的に取得するためです。 MSAL は、トークンのキャッシュと更新を管理します。

最終的に、`AcquireTokenSilentAsync` メソッドは失敗します。 この失敗は、ユーザーがサインアウトしたか、別のデバイスでパスワードを変更したことが原因と考えられます。 ユーザーの操作によって解決できる問題が MSAL によって検出された場合、MSAL は `MsalUiRequiredException` 例外を発行します。 アプリケーションでは、この例外を 2 つの方法で処理できます。

* すぐに `AcquireTokenAsync` を呼び出します。 この呼び出しにより、ユーザーにサインインを求めます。 通常、ユーザーが使用できるオフライン コンテンツがないオンライン アプリケーションでは、このパターンを使用します。 このガイドの設定で生成されるサンプルは、このパターンに従っています。 サンプルを初めて実行したときの動作で、それがわかります。 
    * アプリケーションはユーザーによって使用されたことがないため、`PublicClientApp.Users.FirstOrDefault()` には null 値が含まれ、`MsalUiRequiredException` 例外がスローされます。
    * その後、サンプルのコードは、`AcquireTokenAsync` を呼び出すことによって例外を処理します。 この呼び出しにより、ユーザーにサインインを求めます。

* または、代わりに、対話型サインインが必要であることをユーザーに視覚的に示します。 その場合、ユーザーは適切な時を選んでサインインすることができます。 または、アプリケーションが後で `AcquireTokenSilentAsync` を再試行します。 多くの場合、このパターンは、ユーザーが中断することなく他のアプリケーション機能を使用できる場合に使用されます。 たとえば、アプリケーションでオフラインのコンテンツを使用可能な場合です。 この場合、保護されたリソースにアクセスしたり、古くなった情報を更新したりするために、サインインするタイミングをユーザーが決定できます。 または、一時的に使用できなくなっていたネットワークが回復したときに、アプリケーションが `AcquireTokenSilentAsync` の再試行を決定することもできます。

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

* 次の新しいメソッドを **MainPage.xaml.cs** に追加します。 このメソッドは、[Authorize] ヘッダーを使用した Graph API に対する `GET` 要求の実行に使用されます。

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
            // Add the token in Authorization header
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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このサンプル アプリケーションでは、`GetHttpContentWithToken` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行します。 その後、メソッドは、呼び出し元にその内容を返します。 このメソッドは、取得したトークンを **HTTP Authorization** ヘッダーに追加します。 このサンプルで使用するリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API **me** エンドポイントです。
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>ユーザーをサインアウトするメソッドの追加

* ユーザーをサインアウトするには、次のメソッドを **MainPage.xaml.cs** に追加します。

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

### <a name="more-information-on-sign-out"></a>サインアウトの詳細

`SignOutButton_Click` メソッドは、MSAL ユーザー キャッシュからユーザーを削除します。 このメソッドは、実際には現在のユーザーを忘れるように MSAL に指示します。 トークンを取得するための以降の要求は、対話型で行われる場合にのみ成功します。
このサンプルのアプリケーションは、単一のユーザーをサポートしています。 しかし、MSAL では、複数のアカウントが同時にサインインできるシナリオがサポートされています。 例として、電子メール アプリケーションで 1 人のユーザーがいくつかのアカウントを持っている場合が挙げられます。

## <a name="display-basic-token-information"></a>基本的なトークン情報を表示する

* トークンについての基本的な情報を表示するには、次のメソッドを **MainPage.xaml.cs** ファイルに追加します。

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

### <a name="more-information"></a>詳細情報

**OpenID 接続**を使用して取得した ID トークンにも、ユーザー関連情報の少量のサブセットが含まれています。 `DisplayBasicTokenInfo` は、トークンに含まれている基本的な情報を表示します。 たとえば、ユーザーの表示名や ID、トークンの有効期限、アクセス トークン自体を表す文字列などです。 **[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** ボタンを数回選択すると、後続の要求で同じトークンが再利用されていることが確認できます。 また、MSAL がトークンの更新時期だと判断したときに、有効期限が延長されることも確認できます。

## <a name="register-your-application"></a>アプリケーションの登録

次の手順に従って Microsoft アプリケーション登録ポータルでアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します。
2. アプリケーションの名前を入力します。
3. **[ガイド付きセットアップ]** のオプションが "*選択されていない*" ことを確認します。
4. **[プラットフォームの追加]**、**[ネイティブ アプリケーション]**、**[保存]** の順に選択します。
5. **[アプリケーション ID]** の GUID をコピーし、Visual Studio に戻って **App.xaml.cs** を開き、登録したアプリケーション ID で `your_client_id_here` を置き換えます。

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>フェデレーション ドメインでの統合認証の有効化 (省略可能)

Windows 統合認証を、フェデレーション Azure Active Directory ドメインと共に使用する場合に有効にするには、アプリケーション マニフェストで追加機能を有効にする必要があります。

1. **Package.appxmanifest** をダブルクリックします。
2. **[機能]** タブを選択し、以下の設定が有効になっていることを確認します。

    - エンタープライズ認証
    - プライベート ネットワーク (クライアントとサーバー)
    - 共有ユーザー証明書

3. **App.xaml.cs** を開き、アプリのコンストラクターに次の行を追加します。

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> このサンプルには、既定では Windows 統合認証が構成されていません。 "*エンタープライズ認証*" または "*共有ユーザー証明書*" 機能を要求するアプリケーションでは、Windows ストアによるより高いレベルの検証が必要です。 また、すべての開発者がより高いレベルの検証を望むとは限りません。 この設定は、Windows 統合認証とフェデレーション Azure Active Directory ドメインが必要な場合にのみ有効にしてください。


## <a name="test-your-code"></a>コードのテスト

アプリケーションをテストするには、Visual Studio で F5 キーを押してプロジェクトを実行します。 メイン ウィンドウが表示されます。

![アプリケーションのユーザー インターフェイス](./media/tutorial-v2-windows-uwp/testapp-ui.png)

テストの準備ができたら、**[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)** を選択します。 次に、Microsoft Azure Active Directory 組織アカウントまたは Microsoft アカウント (live.com、outlook.com など) を使用してサインインします。 初めての場合は、ユーザーにサインインを求めるウィンドウが表示されます。

![サインイン ページ](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>同意
アプリケーションに初めてサインインすると、次のような同意画面が表示されます。 アクセスするには、**[はい]** を選択して、明示的に同意します。

![アクセス同意画面](./media/tutorial-v2-windows-uwp/consentscreen.png)
### <a name="expected-results"></a>予想される結果
**[API Call Results]\(API 呼び出しの結果\)** 画面に、Microsoft Graph API の呼び出しによって返されたユーザー プロファイル情報が表示されます。

![API 呼び出しの結果画面](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

`AcquireTokenAsync` または `AcquireTokenSilentAsync` によって取得したトークンに関する以下の基本的な情報も、**[Token Info]\(トークン情報\)** ボックスに表示されます。

|プロパティ  |形式  |説明 |
|---------|---------|---------|
|**名前** |ユーザーのフルネーム|ユーザーの姓と名。|
|**ユーザー名** |<span>user@domain.com</span> |ユーザーを識別するユーザー名。|
|**Token Expires** |Datetime |トークンの有効期限が切れる日時。 MSAL は、必要に応じてトークンを更新することで、有効期限日を延長します。|
|**Access Token** |String |*Authorization ヘッダー*を必要とする HTTP 要求に送信されるトークン文字列。|

#### <a name="see-whats-in-the-access-token-optional"></a>アクセス トークンの内容の確認 (省略可能)
必要に応じて、**Access Token** の値をコピーして https://jwt.ms に貼り付けると、デコードされ、要求の一覧を見ることができます。

### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 このスコープは、アプリケーション登録ポータルで登録されたすべてのアプリケーションで、既定で自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 Microsoft Graph API には、ユーザーの予定表を表示するための *Calendars.Read* スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、*Calendars.Read* の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、*Calendars.Read* スコープを `acquireTokenSilent` 呼び出しに追加します。 

> [!NOTE]
> スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

## <a name="known-issues"></a>既知の問題

### <a name="issue-1"></a>問題 1
フェデレーション Azure Active Directory ドメイン上のアプリケーションにサインインするときに、次のいずれかのエラー メッセージが表示されます。
 - 要求に有効なクライアント証明書が見つかりませんでした。
 - ユーザーの証明書ストアに有効な証明書が見つかりませんでした。
 - 別の認証方法を選択してやり直してください。

**原因:** エンタープライズ機能および証明書機能が有効になっていません。

**対処:** [フェデレーション ドメインでの統合認証](#enable-integrated-authentication-on-federated-domains-optional)に関するセクションの手順に従います。

### <a name="issue-2"></a>問題 2
[フェデレーション ドメインの統合認証](#enable-integrated-authentication-on-federated-domains-optional)を有効にし、Windows 10 コンピューターで Windows Hello を使用して、多要素認証が構成されている環境でサインインしようとします。 証明書の一覧が表示されます。 しかし、PIN の使用を選択すると、PIN ウィンドウが表示されません。

**原因:** この問題は、Windows 10 デスクトップで実行される UWP アプリケーションの Web 認証ブローカーに関する既知の制限です。 Windows 10 Mobile では正常に動作します。

**回避策:** **[Sign in with other options]\(他のオプションでサインイン\)** を選択します。 次に、**[Sign in with a username and password]\(ユーザー名とパスワードでサインイン\)** を選択します。 **[Provide your password]\(パスワードを指定\)** を選択します。 電話認証プロセスに進みます。
