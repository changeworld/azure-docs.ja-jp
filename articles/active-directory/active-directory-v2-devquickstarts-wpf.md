<properties
	pageTitle="アプリ モデル v2.0 | Microsoft Azure"
	description="サインインに個人の Microsoft アカウントと職場/学校アカウントの両方を使用する .NET ネイティブ アプリを構築する方法を説明します。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: Windows デスクトップ アプリへのサインインの追加

v2.0 アプリ モデルを使用すると、Microsoft の個人および職場/学校アカウントの両方に対応したデスクトップ アプリに認証をすばやく追加できます。また、アプリとバックエンド Web API や一部の [Office 365 Unified API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) との安全な通信を可能にします。

> [AZURE.NOTE]この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。一般公開されている Azure AD サービスと連携する手順については、「[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)」を参照してください。

[デバイスで実行する .NET ネイティブ アプリ](active-directory-v2-flows.md#mobile-and-native-apps)に対しては、Azure AD により、Active Directory 認証ライブラリ (ADAL) が提供されます。ADAL の唯一の目的は、アプリが Web サービスを呼び出すためのトークンを容易に取得できるようにすることです。どれほど簡単かを示すために、ここで、次のような、.NET WPF To-Do List アプリを構築します。

-	ユーザーのサインイン処理を行い、[OAuth 2.0 認証プロトコル](active-directory-v2-protocols.md#oauth2-authorization-code-flow)を使用してアクセス トークンを取得します。
-	バックエンド To-Do List Web サービスを安全に呼び出します。これは、OAuth 2.0 でも保護されます。
-	ユーザーのサインアウト処理を行います。

完全に動作するアプリを構築するには、次の手順を行う必要があります。

2. アプリを登録します。
3. ADAL をインストールおよび構成する
5. ADAL を使用して、Azure AD からトークンを取得する

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet) で管理されています。追加の参考資料として、[アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip)したり、スケルトンを複製したりすることができます:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

The completed app is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.

## 2. Install & Configure ADAL
Now that you have an app registered with Microsoft, you can install ADAL and write your identity-related code.  In order for ADAL to be able to communicate the v2.0 endpoint, you need to provide it with some information about your app registration.

-	Begin by adding ADAL to the TodoListClient project using the Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease ```

-	TodoListClient プロジェクトで `app.config` を開きます。アプリ登録ポータルで入力した値が反映されるように、`<appSettings>` セクションの要素の値を置き換えます。これらの値は、コードで ADAL を使用する際に常に参照されます。
    -	`ida:ClientId` は、ポータルからコピーしたアプリの**アプリケーション ID** です。
    -	`ida:RedirectUri` は、ポータルの**リダイレクト URI** です。
- TodoList-Service プロジェクトで、プロジェクトのルートにある `web.config` を開きます。  
    - `ida:Audience` 値をポータルからの同じ**アプリケーション ID** に置き換えます。

## 3\.ADAL を使用してトークンを取得します
ADAL を使用することの基本的なメリットは、アプリがアクセス トークンを必要とする場合、必要な操作は `authContext.AcquireToken(...)` を呼び出すことだけで、残りの処理は ADAL が実行してくれることです。

-	`TodoListClient` プロジェクトで、`MainWindow.xaml.cs` を開き、`OnInitialized(...)` メソッドを見つけます。最初の手順は、アプリの `AuthenticationContext` (ADAL のプライマリ クラス) を初期化することです。ここでは、ADAL が Azure AD と通信し、トークンをキャッシュする方法を通知するために必要な調整項目を ADAL に渡します。

```C#
protected override async void OnInitialized(EventArgs e)
{
		base.OnInitialized(e);

		authContext = new AuthenticationContext(authority, new FileCache());
		AuthenticationResult result = null;
		...
}
```

- アプリの起動時に、ユーザーが既にアプリにサインインしているかどうかを確認する必要があります。ただし、まだサインイン UI は呼び出しません。これを行うには、ユーザーに [サインイン] をクリックさせます。また、`OnInitialized(...)` メソッドで次のコードを実行します:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
		result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

		// If we got here, a valid token is in the cache.  Proceed to
		// fetch the user's tasks from the TodoListService via the
		// GetTodoList() method.

		SignInButton.Content = "Clear Cache";
		GetTodoList();
}
catch (AdalException ex)
{
		if (ex.ErrorCode == "user_interaction_required")
		{
				// If user interaction is required, the app should take no action,
				// and simply show the user the sign in button.
		}
		else
		{
				// Here, we catch all other AdalExceptions

				string message = ex.Message;
				if (ex.InnerException != null)
				{
						message += "Inner Exception : " + ex.InnerException.Message;
				}
				MessageBox.Show(message);
		}
}
```

- ユーザーがサインインしておらず、[サインイン] ボタンをクリックした場合、ログイン UI を呼び出し、ユーザーに資格情報を入力させます。次のように、[サインイン] ボタン ハンドラーを実装します:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// TODO: Sign the user out if they clicked the "Clear Cache" button

		// If the user clicked the 'Sign-In' button, force
		// ADAL to prompt the user for credentials by specifying
		// PromptBehavior.Always.  ADAL will get a token for the
		// TodoListService and cache it for you.

		AuthenticationResult result = null;
		try
		{
				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
				SignInButton.Content = "Clear Cache";
				GetTodoList();
		}
		catch (AdalException ex)
		{
				// If ADAL cannot get a token, it will throw an exception.
				// If the user canceled the login, it will result in the
				// error code 'authentication_canceled'.

				if (ex.ErrorCode == "authentication_canceled")
				{
						MessageBox.Show("Sign in was canceled by the user");
				}
				else
				{
						// An unexpected error occurred.
						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}

						MessageBox.Show(message);
				}

				return;
		}

}
```

- ユーザーが正常にサインインすると、ADAL によりトークンが自動的に受信、キャッシュされ、ここでは確実に `GetTodoList()` メソッドを呼び出すことができます。ユーザーが行う必要があるのは、`GetTodoList()` メソッドの実装だけです。

```C#
private async void GetTodoList()
{
		AuthenticationResult result = null;
		try
		{
				// Here, we try to get an access token to call the TodoListService
				// without invoking any UI prompt.  PromptBehavior.Never forces
				// ADAL to throw an exception if it cannot get a token silently.

				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
		}
		catch (AdalException ex)
		{
				// ADAL couldn't get a token silently, so show the user a message
				// and let them click the Sign-In button.

				if (ex.ErrorCode == "user_interaction_required")
				{
						MessageBox.Show("Please sign in first");
						SignInButton.Content = "Sign In";
				}
				else
				{
						// In any other case, an unexpected error occurred.

						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}
						MessageBox.Show(message);
				}

				return;
		}

		// Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

		...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null) { // If the user clicked the 'clear cache' button, // clear the ADAL token cache and show the user as signed out. // It's also necessary to clear the cookies from the browser // control so the next user has a chance to sign in.

		if (SignInButton.Content.ToString() == "Clear Cache")
		{
				TodoList.ItemsSource = string.Empty;
				authContext.TokenCache.Clear();
				ClearCookies();
				SignInButton.Content = "Sign In";
				return;
		}

		...
```

Congratulations! You now have a working .NET WPF app that has the ability to authenticate users & securely call Web APIs using OAuth 2.0.  Run your both projects, and sign in with either a personal Microsoft account or a work or school account.  Add tasks to that user's To-Do list.  Sign out, and sign back in as another user to view their To-Do list.  Close the app, and re-run it.  Notice how the user's session remains intact - that is becuase the app caches tokens in a local file.

ADAL makes it easy to incorporate common identity features into your app, using both personal and work accounts.  It takes care of all the dirty work for you - cache management, OAuth protocol support, presenting the user with a login UI, refreshing expired tokens, and more.  All you really need to know is a single API call, `authContext.AcquireTokenAsync(...)`.

For reference, the completed sample (without your configuration values) [is provided as a .zip here](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), or you can clone it from GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## 次のステップ

これ以降は、さらに高度なトピックに進むことができます。次のチュートリアルを試してみてください。

- [ v2.0 アプリ モデルでの TodoListService Web API の保護 >>](active-directory-v2-devquickstarts-dotnet-api.md)

その他のリソースについては、以下を参照してください。 - [アプリ モデル v2.0 プレビュー >>](active-directory-appmodel-v2-overview.md) - [StackOverflow "adal" タグ >>](http://stackoverflow.com/questions/tagged/adal)

<!---HONumber=August15_HO7-->