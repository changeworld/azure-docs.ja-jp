<properties
	pageTitle="Azure AD .NET の概要 | Microsoft Azure"
	description="認証と承認のために Azure AD と連携する .NET MVC Web API を構築する方法"
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Azure AD からのベアラー トークンの使用による Web API の保護

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

保護されているリソースにアクセスするアプリケーションを構築する場合、認められていないアクセスからこれらのリソースを保護する方法を習得する必要があります。Azure AD を使用すると、OAuth Bearer 2.0 アクセス トークンを使用し、わずか数行のコードを追加するだけで、Web API を簡単に保護することができます。

ASP.NET Web アプリでは、.NET Framework 4.5 に含まれるコミュニティ駆動の OWIN ミドルウェアの Microsoft 実装を使用することにより、これを達成できます。ここでは、OWIN を使用して、次の処理を実行する "To Do List" Web API を構築します。
-	保護対象の API を指定する。
-	Web API 呼び出しに有効なアクセス トークンが含まれているかどうかを検証する。

これを行うには、次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する
2. OWIN 認証パイプラインを使用するようにアプリをセットアップする
3. To Do List Web API を呼び出すように、クライアント アプリケーションを構成する

最初に、[アプリのスケルトンをダウンロードするか](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)、または[完全なサンプルをダウンロードします](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)。両方とも、Visual Studio 2013 ソリューションです。アプリケーションの登録先となる Azure AD テナントも必要です。テナントを所有していない場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。


## *1.アプリケーションを Azure AD に登録する*
アプリケーションをセキュリティで保護するには、まず、テナントでアプリケーションを作成し、Azure AD にいくつかの重要な情報を提供する必要があります。

-	[Microsoft Azure の管理ポータル](https://manage.windowsazure.com)にサインインします。
-	左側のナビゲーションで **[Active Directory]** をクリックします。
-	アプリケーションの登録先となるテナントを選択します。
-	**[アプリケーション]** タブをクリックし、下部のドロアーで **[追加]** をクリックします。
-	画面の指示に従い、新しい **Web アプリケーションまたは WebAPI** を作成します。
    -	アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。「To Do List Service」と入力します。
    -	**[リダイレクト URI]** には、Azure AD が、アプリが要求したトークンを返すために使用するスキームと文字列の組み合わせを設定します。この値として、「`https://localhost:44321/`」を入力します。
-	登録が完了したら、**[構成]** タブに移動し、**[アプリケーション ID/URI]** フィールドを見つけます。この値には、テナント固有の識別子 (たとえば、`https://contoso.onmicrosoft.com/TodoListService`) を入力します。
- 構成を保存します。ポータルを開いたままにします。この後すぐに、クライアント アプリケーションを登録することも必要になります。

## *2.OWIN 認証パイプラインを使用するようにアプリをセットアップする*

これまでに、アプリケーションを Azure AD に登録したので、受信要求と受信トークンを検証するために、Azure AD と通信するようにアプリケーションをセットアップする必要があります。

-	まず、ソリューションを開き、パッケージ マネージャー コンソールを使用して、OWIN ミドルウェア NuGet パッケージを TodoListService プロジェクトに追加する必要があります。

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-	OWIN Startup クラスを TodoListService プロジェクト (`Startup.cs`) に追加します。プロジェクトを右クリックし、**[追加]**、**[新しいアイテム]** の順にクリックして、"OWIN" を検索します。アプリが起動すると、OWIN ミドルウェアは `Configuration(…)` メソッドを呼び出します。
-	クラス宣言を `public partial class Startup` に変更します。このクラスの一部を別のファイルで既に実装しています。`Configuration(…)` メソッドで、ConfgureAuth(…) を呼び出して、Web アプリ用の認証をセットアップします。

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	ファイル `App_Start\Startup.Auth.cs` を開いて、`ConfigureAuth(…)` メソッドを実装します。`WindowsAzureActiveDirectoryBearerAuthenticationOptions` で提供されたパラメーターは、アプリが Azure AD と通信するための調整役として機能します。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-	次に、`[Authorize]` 属性と JWT ベアラー認証を使用して、コントローラーとアクションを保護します。認証タグを使用して、`Controllers\TodoListController.cs` クラスを修飾します。これにより、ユーザーはそのページにアクセスする前に、サインインが必要になります。

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- 承認呼び出し元が `TodoListController` API の 1 つを正常に呼び出すときに、呼び出し元についての情報にアクセスするアクションが必要な場合があります。OWIN は `ClaimsPrincpal` オブジェクトを通して、ベアラー トークン内のクレームにアクセスできるようにします。
- Web API の共通する要件は、トークンで提示される "範囲" を検証することです。これにより、Todo List Service へのアクセスに必要なアクセス許可に、エンド ユーザーが同意していることが確認されます。

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-	最後に、TodoListService プロジェクトのルートにある `web.config` ファイルを開いて、構成値を `<appSettings>` セクションで入力します。
  -	`ida:Tenant` は、Azure AD テナントの名前 (たとえば、"contoso.onmicrosoft.com") です。
  -	`ida:Audience` は、Azure ポータルで入力したアプリケーションのアプリ ID URI です。

## *3.クライアント アプリケーションを構成し、サービスを実行する*
Todo List Service の動作を確認できるようにするには、Todo List Client を構成して、Todo List Client が AAD からトークンを取得し、サービスを呼び出せるようにする必要があります。

- [Microsoft Azure の管理ポータル](https://manage.windowsazure.com)に戻ります。
- Azure AD テナントで新しいアプリケーションを作成し、表示されるプロンプトで **[ネイティブ クライアント アプリケーション]** を選択します。
    -	アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    -	**[リダイレクト URI]** 値には、「`http://TodoListClient/`」を入力します。
- 登録が完了すると、AAD により、アプリに一意の**クライアント ID** が割り当てられます。この値は次の手順で必要になるので、[構成] タブからコピーします。
- また、**[構成]** タブで、[他のアプリケーションに対するアクセス許可] セクションに移動します。[アプリケーションの追加] をクリックします。 [表示] ドロップダウンで [すべてのアプリ] を選択し、上のチェック マークをクリックします。To Do List Service を見つけてクリックし、下のチェック マークをクリックしてアプリケーションを追加します。[委任されたアクセス許可] ドロップダウンで [To Do List Service へのアクセス] を選択し、構成を保存します。


- Visual Studio で、TodoListClient プロジェクトの `App.config` を開いて、`<appSettings>` セクションに構成値を入力します。
  -	`ida:Tenant` は、Azure AD テナントの名前 (たとえば、"contoso.onmicrosoft.com") です。
  -	`ida:ClientId` は、Azure ポータルからコピーしたアプリ ID です。
  -	`todo:TodoListResourceId` は、Azure ポータルで入力した、To Do List Service アプリのアプリケーション ID URI です。

最後に、各プロジェクトをクリーンアップし、ビルドして、実行します。 *.onmicrosoft.com ドメインを使用して、テナントに新しいユーザーを作成します (作成していない場合)。そのユーザーとして To Do List クライアントにサインインし、いくつかのタスクを To Do List に追加します。

完全なサンプル (構成値を除く) を取得するには、[ここ](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)をクリックしてください。ここからは、さらに詳細な ID シナリオに進むことができます。

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->