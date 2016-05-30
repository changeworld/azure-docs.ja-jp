<properties
	pageTitle="Azure Active Directory B2C プレビュー | Microsoft Azure"
	description="Azure Active Directory B2C を使用してサインイン、サインアップ、プロファイル管理を行う Windows デスクトップ アプリケーションを構築する方法。"
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="dastrock"/>

# Azure AD B2C プレビュー: Windows デスクトップ アプリを作成する


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Azure Active Directory (Azure AD) B2C を使用すると、強力なセルフサービス方式の ID 管理機能を、わずかな手順でデスクトップ アプリに追加できます。この記事では、ユーザーのサインアップ、サインイン、プロファイル管理などの処理を含む .NET Windows Presentation Foundation (WPF) の "To-Do List" アプリの作成方法について説明します。このアプリには、ユーザー名または電子メールを使用したサインアップとサインインのサポートが含まれます。また、Facebook や Google などのソーシャル アカウントを使用したサインアップとサインインのサポートも含まれます。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md)してください。

## アプリケーションの作成

次に、B2C ディレクトリにアプリを作成する必要があります。これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従ってください。次を行ってください。

- アプリケーションに**ネイティブ クライアント**を含めます。
- **[リダイレクト URI]** (`urn:ietf:wg:oauth:2.0:oob`) をコピーします。これはこのサンプル コードで使用する既定の URL です。
- アプリに割り当てられた**アプリケーション ID** をコピーしておきます。この情報は後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが[ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。このコード サンプルには、3 つの ID エクスペリエンス (サインアップ、サインイン、プロファイル編集) が含まれています。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとにポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- ID プロバイダーのブレードで、**[ユーザー ID サインアップ]** または **[電子メール サインアップ]** を選択します。
- サインアップ ポリシーで、**[表示名]** と他のサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション要求として **[表示名]** 要求と **[オブジェクト ID]** 要求を選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの **[名前]** をコピーしておきます。名前には、`b2c_1_` というプレフィックスが付加されています。これらのポリシー名は後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

## コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet) で管理されています。手順に従ってサンプルを構築するには、[スケルトン プロジェクトを .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)します。スケルトンを複製することもできます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

また、完成済みのアプリも、[.zip ファイルとして入手する](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)か、同じリポジトリの `complete` ブランチで利用できます。

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。ソリューション ファイルには、2 つのプロジェクト `TaskClient` と `TaskService` が含まれます。`TaskClient` は、ユーザーが対話する WPF デスクトップ アプリケーションです。`TaskService` は、各ユーザーの To-Do List を格納する、アプリのバックエンド Web API です。ここでは、`TaskClient` と `TaskService` の両方が単一のアプリケーション ID で表されます。これは、これらが 1 つの論理アプリケーションを構成するためです。

## タスク サービスを構成する

`TaskService` は `TaskClient` から要求を受け取ると、要求を認証するための有効なアクセス トークンを確認します。アクセス トークンを検証するには、アプリに関する情報を `TaskService` に提供する必要があります。`TaskService` プロジェクトで、プロジェクトのルートにある `web.config` ファイルを開き、`<appSettings>` セクションの値を次の内容に置き換えます。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Azure AD B2C を使用して、Web API で要求を安全に認証する方法については、[Web API の概要についての記事](active-directory-b2c-devquickstarts-api-dotnet.md)を参照してください。

## ポリシーの実行
`TaskService` で要求を認証する準備が完了したら、`TaskClient` を実装できます。アプリは、HTTP 認証要求を送信することで、Azure AD B2C と通信します。このとき、要求の一部として実行するポリシーを指定します。.NET デスクトップ アプリケーションでは、Active Directory 認証ライブラリ (ADAL) を使用して、OAuth 2.0 認証メッセージを送信し、ポリシーを実行して、Web API を呼び出すトークンを取得できます。

### ADAL をインストールする
Visual Studio Package Manager Console を使用して、ADAL を `TaskClient` プロジェクトに追加します。

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

### B2C の詳細情報を入力する
`Globals.cs` ファイルを開き、各プロパティ値を実際の値に置き換えます。このクラスは、共通に使用される値を参照するために `TaskClient` 全体で使用されます。

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### AuthenticationContext を作成する
ADAL のプライマリ クラスは `AuthenticationContext` です。これは B2C ディレクトリへのアプリの接続を表します。アプリの起動時に、`MainWindow.xaml.cs` 内の `AuthenticationContext` のインスタンスを作成します。これは、ウィンドウ全体で使用できます。

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default. Because we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

### サインアップ フローを開始する
ユーザーがサインアップを選択した場合、作成したサインアップ ポリシーを使用するサインアップ フローを開始します。ADAL を使用するので、`authContext.AcquireTokenAsync(...)` を呼び出すだけです。`AcquireTokenAsync(...)` に渡すパラメーターにより、受け取るトークン、認証要求で使用されるポリシーなどが決まります。

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that you want a token to our own back-end API.
		// Use the PromptBehavior. Always flag to indicate to ADAL that it should show a sign-up UI, no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information from AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign-up successfully completes, display the user's to-do list
		GetTodoList();
	}

	// Handle any exemptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
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

### サインイン フローを開始する
サインアップ フローの開始と同じ方法で、サインイン フローを開始できます。ユーザーがサインインしたら、ADAL に対して同じ呼び出しを行いますが、ここではサインイン ポリシーを使用します。

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...
```

### プロファイル編集フローを開始する
ここでも、同じ方法でプロファイル編集ポリシーを実行できます。

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

いずれの場合も、ADAL は `AuthenticationResult` でトークンを返すか、または例外をスローします。ADAL からトークンを取得するたびに、`AuthenticationResult.UserInfo` オブジェクトを使用して、アプリ内のユーザー データ (UI など) を更新できます。ADAL は、アプリケーションの他の部分で使用するために、トークンのキャッシュも行います。

## API を呼び出す
ADAL を使用してポリシーを実行し、トークンを取得してあります。しかし、ポリシーを実行することなく、既存のキャッシュされたトークンをチェックしたいことがよくあります。たとえば、アプリで `TaskService` からユーザーの To-Do List を取得するような場合です。そのためには同じ `authContext.AcquireTokenAsync(...)` メソッドを使用し、やはり `clientId` をスコープ パラメーターとして使用しますが、この場合は `PromptBehavior.Never` も使用します。

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token
		// could not be acquired from the cache, rather than automatically prompt the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

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
	...
```

`AcquireTokenAsync(...)` の呼び出しが成功し、トークンがキャッシュ内で見つかった場合、トークンを HTTP 要求の `Authorization` ヘッダーに追加します。これにより、`TaskService` は要求を認証してユーザーの To-Do List を読み取ることができます。

```C#
	...
	// After the token has been returned by ADAL, add it to the HTTP authorization header before the call is made to TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the to-do-list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

ユーザーにサインインを求めずにトークン キャッシュ内のトークンを確認する必要があるときはいつでも、このパターンを使用できます。たとえば、アプリの起動時に、`FileCache` で既存のトークンをチェックできます。これにより、ユーザーのサインイン セッションはアプリを実行するたびに維持されます。同じコードを `MainWindow` の `OnInitialized` イベントで確認できます。`OnInitialized` はこの初回実行のケースを処理します。

## ユーザーをサインアウトする
ユーザーが **[サインアウト]** を選択したら、ADAL を使用してアプリでのユーザーのセッションを終了できます。ADAL を使用すると、これはトークン キャッシュからすべてのトークンをクリアすることによって実現されます。

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses. It is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## サンプル アプリを実行する

最後に、`TaskClient` と `TaskService` を構築して実行します。電子メール アドレスまたはユーザー名を使用して、アプリにサインアップします。サインアウトし、同じユーザーとしてもう一度サインインします。そのユーザーのプロファイルを編集します。サインアウトし、別のユーザーとしてサインアップします。

## ソーシャル IDP を追加する

現時点では、このアプリは**ローカル アカウント**を使用するユーザーのサインアップとサインインのみをサポートしています。これらは、ユーザー名とパスワードを使用する B2C ディレクトリに格納されているアカウントです。Azure AD B2C を使用すると、コードを変更せずに、その他の ID プロバイダー (IDP) のサポートを追加することができます。

ソーシャル IDP をアプリに追加するには、まず、以下の記事に記載されている詳細な手順に従います。サポートする IDP ごとに、そのシステムでアプリケーションを登録してクライアント ID を取得する必要があります。

- [Facebook を IDP として設定する](active-directory-b2c-setup-fb-app.md)
- [Google を IDP として設定する](active-directory-b2c-setup-goog-app.md)
- [Amazon を IDP として設定する](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn を IDP として設定する](active-directory-b2c-setup-li-app.md)

ID プロバイダーを B2C ディレクトリに追加した後、3 つの各ポリシーを編集し、[ポリシーに関するリファレンス記事](active-directory-b2c-reference-policies.md)で説明されているように、新しい IDP を加える必要があります。ポリシーを保存した後、再度アプリを実行します。各 ID エクスペリエンスに、サインインおよびサインアップの選択肢として新しい IDP が追加されていることがわかります。

いろいろなポリシーを試して、サンプル アプリへの影響を確認できます。ID を追加または削除したり、アプリケーションの要求を操作したり、サインアップ属性を変更してみます。ポリシー、認証要求、および ADAL すべてが互いにどのように結び付いているかを理解できるまで、いろいろ試してみてください。

参照用に、完成したサンプルが[ここに .zip として提供されています](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)。GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0518_2016-->