<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="Azure AD B2C を使用し、サインイン、サインアップ、プロファイル管理を備えた Windows デスクトップ アプリケーションを構築する方法。"
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
	ms.date="09/04/2015"
	ms.author="dastrock"/>

# Azure AD B2C プレビュー: Windows デスクトップ アプリを作成する

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

With Azure AD B2C を使用すると、簡単な手順で強力なセルフサービスの ID 管理機能をデスクトップ アプリに追加できます。この記事では、ユーザーのサインアップ、サインイン、プロファイル管理などの処理を含む .NET WPF "To-Do List" アプリの作成方法について説明します。サポート対象には、ユーザー名または電子メール アドレスを使用したサインアップおよびサインインのほか、Facebook や Google などのソーシャル アカウントも含まれます。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\.Azure AD B2C ディレクトリを取得する

Azure AD B2C を使用するには、ディレクトリまたはテナントを作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどのすべてを格納するコンテナーです。ディレクトリをまだ用意していない場合、先に進む前に [B2C ディレクトリの作成](active-directory-b2c-get-started.md)に関するページを参照してください。

## 2\.アプリケーションの作成

ここで、B2C ディレクトリにアプリを作成する必要があります。このディレクトリによって、アプリと安全に通信するために必要ないくつかの情報を Azure AD に提供します。アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従ってください。このとき、

- アプリケーションに**ネイティブ クライアント**を含めます。
- **リダイレクト URI** `urn:ietf:wg:oauth:2.0:oob` をコピーします。これは、このサンプル コードで使用する既定の URL です。
- アプリに割り当てられた**アプリケーション ID** をメモしておきます。このプロジェクトはすぐに必要になります。

## 3\.ポリシーを作成する

Azure AD B2C では、すべてのユーザー エクスペリエンスは[**ポリシー**](active-directory-b2c-reference-policies.md)によって定義されます。このコード サンプルには、3 つの ID エクスペリエンス (サインアップ、サインイン、プロファイル編集) が含まれています。[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- [ID プロバイダー] ブレードで、**[ユーザー ID サインアップ]** または **[電子メール サインアップ]** を選択します。
- サインアップ ポリシーで、**[表示名]** と他のいくつかのサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション クレームとして **[表示名]** と **[オブジェクト ID]** の各クレームを選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの **[名前]** をメモしておきます。名前には、プレフィックス `b2c_1_` が付加されます。これらのポリシー名はすぐに必要になります。 

3 つのポリシーの作成が正常に完了すると、アプリをビルドする準備が整います。

## 4\.コードをダウンロードする

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet) で管理されています。この後サンプルを構築するために、[スケルトン プロジェクトを .zip 形式でダウンロードする](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)か、次のようにスケルトンのクローンを作成できます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

また、完成したアプリは、[.zip 形式で利用する](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)か、同じリポジトリの `complete` ブランチで利用できます。

サンプル コードをダウンロードした後は、Visual Studio の `.sln` ファイルを開きます。ソリューションに `TaskClient` プロジェクトと `TaskService` プロジェクトという 2 つのプロジェクトがあることがわかります。`TaskClient` は、ユーザーと対話する WPF デスクトップ アプリケーションです。`TaskService` は、各ユーザーの To Do リストを格納する、アプリのバックエンド Web API です。ここでは、`TaskClient` と `TaskService` の両方が単一の**アプリケーション ID** で表されます。これは、どちらも 1 つの論理アプリケーションを構成するためです。

## 5\.タスク サービスを構成する

`TaskService` は `TaskClient` から要求を受け取ると、要求を認証するための有効なアクセス トークンを確認します。アクセス トークンを検証するには、アプリに関するいくつかの情報を `TaskService` に提供する必要があります。`TaskService` プロジェクトで、プロジェクトのルートで `web.config` ファイルを開き、`<appSettings>` セクションの値を以下で置き換えます。

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

If you want to learn how a web API securely authenticates requests using Azure AD B2C, check out our
[Web API Getting Started article](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Execute policies
Now that the `TaskService` is ready to authenticate requests, we can implement the `TaskClient`.  Your app communicates with Azure AD B2C by sending HTTP authentication requests,
specifying the policy it wishes to execute as part of the request.  For .NET desktop applications, you can use the **Active Directory Authentication Library (ADAL)**
to send OAuth 2.0 authentication messages, execute policies, and get tokens for calling web APIs.

#### Install ADAL
Begin by adding ADAL to the TaskClient project using the Visual Studio Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### B2C の詳細情報を入力する
`Globals.cs` ファイルを開き、各プロパティの値を実際の値に置き換えます。このクラスは、共通に使用される値を参照するために `TaskClient` 全体で使用されます。

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


#### AuthenticationContext を作成する
ADAL のプライマリ クラスは `AuthenticationContext` です。これは、B2C ディレクトリとのアプリの接続を表します。アプリが開始したら、`MainWindow.xaml.cs` 内の `AuthenticationContext` のインスタンスを作成します。これは、ウィンドウ全体で使用できます。

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

#### サインアップ フローを開始する
ユーザーがサインアップ ボタンをクリックしたら、作成したサインアップ ポリシーを使用してサインアップ フローを開始します。ADAL では、`authContext.AcquireTokenAsync(...)` を呼び出すだけで済みます。`AcquireTokenAsync(...)` に渡すパラメーターにより、受け取るトークン、認証要求で使用されるポリシーなどが決まります。

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
		// Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;
		
		// When the request completes successfully, you can get user information form the AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign up successfully completes, display the user's To-Do List
		GetTodoList();
	}
	
	// Handle any exeptions that occurred during execution of the policy.
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

#### サインイン フローを開始する
サインイン フローは、サインアップ フローと同じ方法で開始できます。ユーザーがサインイン ボタンがクリックしたら、ADAL に対して同じ呼び出しを行いますが、今度はサインイン ポリシーを使用します。

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

#### プロファイル編集フローを開始する
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

いずれの場合も、ADAL は `AuthenticationResult` でトークンを返すか、または例外をスローします。ADAL からトークンを取得するたびに、`AuthenticationResult.UserInfo` オブジェクトを使用してアプリ内の UI などのユーザー データを更新できます。ADAL は、アプリケーションの他の部分で使用するために、トークンのキャッシュも行います。

## 7\.API を呼び出す
ADAL を使用してポリシーを実行し、トークンを取得しました。しかし、ポリシーを実行することなく、既存のキャッシュされたトークンをチェックしたいことがよくあります。たとえば、アプリで `TaskService` からユーザーの To-Do List のフェッチを試みるような場合です。同じ `authContext.AcquireTokenAsync(...)` メソッドを使用し、やはり `clientId` をスコープ パラメーターとして使用しますが、この場合は `PromptBehavior.Never` を使用します。

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
		// could not be acquired from the cache, rather than automatically prompting the user to sign in. 
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);
	
	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign-in.
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

`AcquireTokenAsync(...)` の呼び出しが成功し、トークンがキャッシュ内に見つかった場合、トークンを HTTP 要求の `Authorization` ヘッダーに追加し、`TaskService` が要求を認証してユーザーの To-Do List を読み取れるようにすることができます。

```C#
	...
	// Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the To Do list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
``` 

ユーザーにサインインを求めずにトークン キャッシュ内のトークンを確認する必要があるときはいつでも、これと同じパターンを使用できます。たとえば、アプリの起動時に、`FileCache` で既存のトークンをチェックして、アプリが実行するたびにユーザーのサインイン セッションを維持することができます。この最初の実行を処理する同じコードが、`MainWindow` の `OnInitialized` イベントで使用されています。

## 8\.ユーザーをサインアウトさせる
最後に、ユーザーが "サインアウト" ボタンをクリックしたときに、ADAL を使用してアプリでのユーザーのセッションを終了させることができます。ADAL では、トークン キャッシュからすべてのトークンをクリアするだけの簡単な処理です。

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
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

## 9\.サンプル アプリを実行する

最後に、`TaskClient` と `TaskService` の両方をビルドして実行します。電子メール アドレスまたはユーザー名を使用して、アプリにサインアップします。サインアウトし、同じユーザーとしてサインインします。そのユーザーのプロファイルを編集します。サインアウトし、別のユーザーとしてサインアップします。

## 10\.ソーシャル IDP を追加する

現時点でアプリがサポートしているのは、**ローカル アカウント** (ユーザー名とパスワードによって B2C ディレクトリに格納されているアカウント) を使用したサインアップおよびサインインのみです。Azure AD B2C を使用すると、コードを変更せずに、その他の **ID プロバイダー** (IDP) のサポートを追加することができます。

ソーシャル IDP をアプリに追加するには、まず以下の記事のうち 1 つまたは 2 つを参照して詳細な手順に従います。サポートする各 IDP に対して、そのシステム上でアプリケーションを登録してクライアント ID を取得する必要があります。

- [Facebook を IDP として設定する](active-directory-b2c-setup-fb-app.md)
- [Google を IDP として設定する](active-directory-b2c-setup-goog-app.md)
- [Amazon を IDP として設定する](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn を IDP として設定する](active-directory-b2c-setup-li-app.md) 

ID プロバイダーを B2C ディレクトリに追加したら、3 つのポリシーに戻って各ポリシーを編集し、[ポリシーに関するリファレンス記事](active-directory-b2c-reference-policies.md)で説明されているように、新しい IDP を加える必要があります。ポリシーを保存したら、もう一度アプリを実行します。各 ID エクスペリエンスに、サインインおよびサインアップの選択肢として新しい IDP が追加されていることがわかります。

IDP の追加や削除、アプリケーション要求の操作、サインアップ属性の変更など、ポリシーを使用して自由に実験し、サンプル アプリに与える影響を観察できます。ポリシー、認証要求、および ADAL が互いに結び付くしくみを理解できるまで、実験を続けてみてください。

参照用に、完成したサンプルが[ここに .zip として用意されています](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)。あるいは、GitHub からクローンを作成できます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!----HONumber=Sept15_HO3-->