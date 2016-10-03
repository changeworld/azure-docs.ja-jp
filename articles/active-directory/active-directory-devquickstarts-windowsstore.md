<properties
	pageTitle="Azure AD Windows ストアの概要 | Microsoft Azure"
	description="サインインし、OAuth を使用して Azure AD で保護されている API を呼び出すために Azure AD と連携する Windows ストア アプリケーションを構築する方法。"
	services="active-directory"
	documentationCenter="windows"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Azure AD と Windows ストア アプリの統合

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Windows ストア用アプリを開発する場合、Azure AD を使用すると、Active Directory アカウントを使用してユーザーの認証処理を容易に行うことができます。また、Office 365 API や Azure API などの Azure AD によって保護された任意の Web API をアプリケーションで安全に使用することもできます。

保護されたリソースにアクセスする必要がある Windows ストア デスクトップ アプリに対しては、Azure AD は Active Directory 認証ライブラリ (ADAL) を提供します。ADAL の唯一の目的は、アプリケーションがアクセス トークンを容易に取得できるようにすることです。どれほど簡単かを示すため、ここでは次のような機能を備えた「ディレクトリ検索」 Windows ストア アプリを作成します。

-	[OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して Azure AD Graph API を呼び出すためのアクセストークンを取得します。
-	指定された UPN を持つユーザーをディレクトリで検索します。
-	ユーザーのサインアウト処理を行います。

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

2. Azure AD にアプリケーションを登録する
3. ADAL をインストールおよび構成する
5. ADAL を使用して、Azure AD からトークンを取得する

最初に、[スケルトン プロジェクトをダウンロードするか](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip)、または[完全なサンプルをダウンロードします](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip)。それぞれが Visual Studio 2015 ソリューションです。また、ユーザーを作成し、アプリケーションを登録することを可能にするための Azure AD テナントも必要です。テナントを所有していない場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## *1.ディレクトリ検索アプリケーションを登録する*
アプリでトークンを取得できるようにするには、まず、アプリを Azure AD テナントに登録し、Azure AD Graph API にアクセスするためのアクセス許可を付与する必要があります。

-	[Azure 管理ポータル](https://manage.windowsazure.com)にサインインします。
-	左側のナビゲーションで **[Active Directory]** をクリックします。
-	アプリケーションの登録先となるテナントを選択します。
-	**[アプリケーション]** タブをクリックし、下部のドロアーで **[追加]** をクリックします。
-	画面の指示に従い、新しい**ネイティブ クライアント アプリケーション**を作成します。
    -	アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    -	**[リダイレクト URI]** には、Azure AD がトークン応答を返すために使用するスキームと文字列の組み合わせを設定します。ここでは、プレースホルダーの値を入力します (例: `http://DirectorySearcher`)。後でこの値を置き換えます。
-	登録が完了すると、AAD により、アプリケーションに一意のクライアント ID が割り当てられます。この値は次のセクションで必要になるので、**[構成]** タブからコピーします。
- また、**[構成]** タブで、[他のアプリケーションに対するアクセス許可] セクションに移動します。"Azure Active Directory" アプリケーションに対して、**[デリゲートされたアクセス許可]** で **[サインイン ユーザーとしてディレクトリにアクセスする]** アクセス許可を追加します。これにより、アプリケーションが Graph API を使用してユーザーをクエリできるようになります。

## *2.ADAL をインストールおよび構成する*
アプリケーションを Azure AD に登録したので、ADAL をインストールし、ID 関連のコードを記述できます。ADAL が Azure AD と通信できるようにするには、アプリの登録に関するいくつかの情報を ADAL に提供する必要があります。
-	最初に、パッケージ マネージャー コンソールを使用して、ADAL を DirectorySearcher プロジェクトに追加します。

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	DirectorySearcher プロジェクトで、`MainPage.xaml.cs` を開きます。Azure ポータルで入力した値が反映されるように、`Config Values` 領域の値を置き換えます。これらの値は、コードで ADAL を使用する際に常に参照されます。
    -	`tenant` は、Azure AD テナントのドメイン (たとえば、contoso.onmicrosoft.com) です。
    -	`clientId` は、ポータルからコピーしたアプリケーションのクライアント ID である必要があります。
-	ここで、Windows ストア アプリのコールバック URI を調べる必要があります。`MainPage` メソッドの次の行にブレークポイントを設定します。

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- ソリューションをビルドし、すべてのパッケージ参照が復元されていることを確認します。パッケージが不足している場合は、Nuget パッケージ マネージャーを開いてパッケージを復元します。
- アプリを実行し、ブレークポイントにヒットしたら `redirectUri` の値をコピーしておきます。次のような内容が表示されます。

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Microsoft Azure の管理ポータルでアプリケーションの **[構成]** タブに戻り、**RedirectUri** の値をこの値に置き換えます。

## *3.ADAL を使用して AAD からトークンを取得する*
ADAL を使用することの基本的なメリットは、アプリがアクセス トークンを必要とする場合、アプリは `authContext.AcquireToken(…)` を呼び出すだけで、残りの処理は ADAL が実行してくれることです。

-	最初の手順は、アプリの `AuthenticationContext` (ADAL のプライマリ クラス) を初期化することです。ここでは、ADAL が Azure AD と通信し、トークンをキャッシュする方法を通知するために必要な調整項目を ADAL に渡します。

```C#
public MainPage()
{
    ...

    authContext = new AuthenticationContext(authority);
}
```

- 次に、`Search(...)` メソッドを見つけます。このメソッドは、ユーザーがアプリの UI で [検索] ボタンをクリックすると呼び出されます。このメソッドは、指定された検索用語で UPN が始まるユーザーをクエリするための、Azure AD Graph API に対する GET 要求を実行します。ただし、Graph API をクエリするためには、要求の `Authorization` ヘッダーに access\_token を含める必要があります。この処理を ADAL が実行します。

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
        }
        return;
    }
    ...
}
```
- アプリが `AcquireTokenAsync(...)` を呼び出すことによってトークンを要求すると、ADAL はユーザーに資格情報を要求することなく、トークンを返そうとします。ADAL は、トークンを取得するにはユーザーのサインインが必要であると判断した場合、ログイン ダイアログを表示し、ユーザーの資格情報を収集し、認証が成功するとトークンを返します。また、何らかの理由により ADAL がトークンを返せない場合、`AuthenticationResult` ステータスはエラーになります。

- 次に、取得した access\_token を使用します。また、`Search(...)` メソッドで、Graph API GET 要求の Authorization ヘッダーにトークンを設定します。

```C#
// Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

```
- `AuthenticationResult` オブジェクトを使用してアプリでのユーザーに関する情報 (ユーザー ID など) を表示することもできます。

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- 最後に、ADAL を使用してアプリケーションからユーザーをサインアウトさせることができます。ユーザーが [サインアウト] ボタンをクリックした場合、次の `AcquireTokenAsync(...)` の呼び出しでサインイン ビューが表示されるようにする必要があります。ADAL を使用すると、この操作は、トークン キャッシュをクリアするのと同じぐらい容易に達成できます。

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

お疲れさまでした。 これで、ユーザー認証を処理でき、OAuth 2.0 を使用して Web API を安全に呼び出すことができ、ユーザーについての基本情報を取得できる、動作する Windows ストア アプリが完成しました。テナントに一連のユーザーを設定します (設定していない場合)。DirectorySearcher アプリを実行し、それらのユーザーの一人としてサインインします。UPN に基づいて、他のユーザーを検索します。アプリを閉じて、再び実行します。ユーザーのセッションがそのままに維持されていることに注意します。右クリックして下部のバーを表示することによりサインアウトし、別のユーザーとしてもう一度サインインします。

ADAL を使用することにより、これらの共通 ID 機能のすべてを容易にアプリケーションに組み込むことができます。キャッシュ管理、OAuth プロトコル サポート、ログイン UI を使用してのユーザーの提示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。習得する必要があるのは、単一の API 呼び出し、`authContext.AcquireToken*(…)` のみです。

完全なサンプル (構成値を除く) を取得するには、[ここ](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip)をクリックしてください。ここからは、さらに ID シナリオに進むことができます。次のチュートリアルを試してみてください。

[Protect a Web API using Bearer tokens from Azure AD (Azure AD からのベアラー トークンを使用することによる Web API の保護)](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->