<properties
	pageTitle="Azure AD Xamarin の概要 | Microsoft Azure"
	description="サインインし、OAuth を使用して Azure AD で保護されている API を呼び出すために Azure AD と連携する Xamarin アプリケーションを構築する方法。"
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Azure AD の Xamarin アプリへの統合

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin を使用すると、iOS、Android、および Windows (モバイル デバイスおよび PC) で実行できるモバイル アプリを C# で作成できます。Xamarin を使用してアプリを開発する場合、Azure AD を使用すると、Active Directory アカウントを使用してユーザーの認証処理を容易に行うことができます。また、Office 365 API や Azure API などの Azure AD によって保護された任意の Web API をアプリケーションで安全に使用することもできます。

保護されたリソースにアクセスする必要がある Xamarin アプリに対しては、Azure AD は Active Directory 認証ライブラリ (ADAL) を提供します。ADAL の唯一の目的は、アプリケーションがアクセス トークンを容易に取得できるようにすることです。どれほど簡単かを示すため、ここでは次のような機能を備えた「ディレクトリ検索」アプリを作成します。

-	iOS、Android、Windows デスクトップ、Windows Phone、Windows ストアで動作します。
- 1 つのポータブル クラス ライブラリ (PCL) を使用してユーザーを認証し、Azure AD Graph API 用のトークンを取得します。
-	指定された UPN を持つユーザーをディレクトリで検索します。

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

2. Xamarin の開発環境を設定します。
2. Azure AD にアプリケーションを登録する
3. ADAL をインストールおよび構成する
5. ADAL を使用して、Azure AD からトークンを取得する

最初に、[スケルトン プロジェクトをダウンロードするか](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip)、または[完全なサンプルをダウンロードします](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)。両方とも、Visual Studio 2013 ソリューションです。また、ユーザーを作成し、アプリケーションを登録することを可能にするための Azure AD テナントも必要です。テナントを所有していない場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## *0.Xamarin の開発環境を設定する*
このチュートリアルには iOS、Android、および Windows 用のプロジェクトが含まれるため、Visual Studio と Xamarin が必要です。必要な環境を構築するには、MSDN の [Visual Studio と Xamarin のセットアップとインストール](https://msdn.microsoft.com/library/mt613162.aspx)に関するページの指示に従ってください。これらの手順では、インストーラーが完了するのを待っている間に確認できる、Xamarin の詳細情報が記載された資料を紹介しています。

必要なセットアップを完了した後、Visual Studio でソリューションを開いて開始します。6 つのプロジェクトが表示されます。5 つはプラットフォームに固有のプロジェクトであり、1 つはすべてのプラットフォームで共有されるポータブル クラス ライブラリ `DirectorySearcher.cs` です。

## *1.ディレクトリ検索アプリケーションを登録する*
アプリでトークンを取得できるようにするには、まず、アプリを Azure AD テナントに登録し、Azure AD Graph API にアクセスするためのアクセス許可を付与する必要があります。

-	[Microsoft Azure の管理ポータル](https://manage.windowsazure.com)にサインインします。
-	左側のナビゲーションで **[Active Directory]** をクリックします。
-	アプリケーションの登録先となるテナントを選択します。
-	**[アプリケーション]** タブをクリックし、下部のドロアーで **[追加]** をクリックします。
-	画面の指示に従い、新しい**ネイティブ クライアント アプリケーション**を作成します。
    -	アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    -	**[リダイレクト URI]** には、Azure AD がトークン応答を返すために使用するスキームと文字列の組み合わせを設定します。値を入力します (例: `http://DirectorySearcher`)。
-	登録が完了すると、AAD により、アプリケーションに一意のクライアント ID が割り当てられます。この値は次のセクションで必要になるので、**[構成]** タブからコピーします。
- また、**[構成]** タブで、[他のアプリケーションに対するアクセス許可] セクションに移動します。"Azure Active Directory" アプリケーションに対して、**[委任されたアクセス許可]** の下の **[組織のディレクトリにアクセス]** アクセス許可を追加します。これにより、アプリケーションが Graph API を使用してユーザーをクエリできるようになります。

## *2.ADAL をインストールおよび構成する*
アプリケーションを Azure AD に登録したので、ADAL をインストールし、ID 関連のコードを記述できます。ADAL が Azure AD と通信できるようにするには、アプリの登録に関するいくつかの情報を ADAL に提供する必要があります。
-	まず、パッケージ マネージャー コンソールを使用して、ADAL をソリューションの各プロジェクトに追加します。

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- ADAL の PCL 部分とプラットフォーム固有部分の 2 つのライブラリ参照が、各プロジェクトに追加されていることに注意してください。

-	DirectorySearcherLib プロジェクトで、`DirectorySearcher.cs` を開きます。Azure ポータルで入力した値が反映されるように、クラス メンバーの値を変更します。これらの値は、コードで ADAL を使用する際に常に参照されます。
    -	`tenant` は、Azure AD テナントのドメイン (たとえば、contoso.onmicrosoft.com) です。
    -	`clientId` は、ポータルからコピーしたアプリケーションのクライアント ID である必要があります。
    - `returnUri` は、ポータルで入力した redirectUri です (例: `http://DirectorySearcher`)。

## *3.ADAL の使用による AAD からのトークンの取得*
*ほとんど*すべてのアプリの認証ロジックは、`DirectorySearcher.SearchByAlias(...)` 内にあります。プラットフォーム固有プロジェクトで必要なのは、`DirectorySearcher` PCL にコンテキスト パラメーターを渡すことだけです。

- 最初に、`DirectorySearcher.cs` を開き、`SearchByAlias(...)` メソッドに新しいパラメーターを追加します。`IPlatformParameters` はコンテキスト パラメーターであり、認証を実行するために ADAL が必要とするプラットフォーム固有のオブジェクトをカプセル化しています。

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	次に、ADAL のプライマリ クラスである `AuthenticationContext` を初期化します。ここでは、ADAL が Azure AD と通信するために必要な調整項目を ADAL に渡します。次に、`AcquireTokenAsync(...)` を呼び出します。このメソッドは `IPlatformParameters` オブジェクトを受け取り、アプリにトークンを返すために必要な認証フローを呼び出します。

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)` は最初に、(古いトークンのキャッシュまたは更新によって) ユーザーに資格情報の入力を求めずに、要求されたリソース (この場合は Graph API) 用のトークンを返そうとします。必要な場合にのみ、要求されたトークンを取得する前に、Azure AD のサインイン ページをユーザーに表示します。


- その後、Graph API 要求の Authorization ヘッダーにアクセス トークンを設定します。

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

`DirectorySearcher` PCL およびアプリの ID に関連するコードはこれですべてです。残っているのは、各プラットフォームのビューで `SearchByAlias(...)` メソッドを呼び出すことと、必要に応じて UI のライフサイクルを適切に処理するためのコードを追加することです。

####Android:
- `MainActivity.cs` で、ボタン クリック ハンドラーに `SearchByAlias(...)` の呼び出しを追加します。

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- また、`OnActivityResult` ライフサイクル メソッドをオーバーライドして、認証リダイレクトを適切なメソッドに転送する必要もあります。ADAL には、Android でこれを行うためのヘルパー メソッドが用意されています。

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Windows デスクトップ:
- `MainWindow.xaml.cs` で、`SearchByAlias(...)` を呼び出して、デスクトップの `PlatformParameters` オブジェクトで `WindowInteropHelper` を渡します。

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS:
- `DirSearchClient_iOSViewController.cs` で、iOS の `PlatformParameters` オブジェクトは単にビュー コントローラーへの参照を受け取ります。

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Windows ユニバーサル:
- Windows ユニバーサルでは、`MainPage.xaml.cs` を開き、`Search` メソッドを実装します。このメソッドは、共有プロジェクトのヘルパー メソッドを使用し、必要に応じて UI を更新します。

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

お疲れさまでした。 以上で、5 種類のプラットフォームで OAuth 2.0 を使用して、ユーザーを認証し、安全に Web API を呼び出す機能を備えた、動作する Xamarin アプリの完成です。テナントに一連のユーザーを設定します (設定していない場合)。DirectorySearcher アプリを実行し、それらのユーザーの一人としてサインインします。UPN に基づいて、他のユーザーを検索します。

ADAL を使用することにより、共通 ID 機能を容易にアプリに組み込むことができます。キャッシュ管理、OAuth プロトコル サポート、ログイン UI を使用してのユーザーの提示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。習得する必要があるのは、単一の API 呼び出し、`authContext.AcquireToken*(…)` のみです。

完全なサンプル (構成値を除く) を取得するには、[ここ](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)をクリックしてください。ここからは、さらに ID シナリオに進むことができます。次のチュートリアルを試してみてください。

[Protect a Web API using Bearer tokens from Azure AD (Azure AD からのベアラー トークンを使用することによる Web API の保護)](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->