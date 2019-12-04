---
title: Azure AD で保護されている ASP.NET Web API の呼び出し - Microsoft ID プラットフォーム
description: このクイックスタートでは、Azure Active Directory によって保護された ASP.NET Web API を Windows デスクトップ (WPF) アプリケーションから呼び出す方法について説明します。 WPF クライアントは、ユーザーを認証し、アクセ ストークンを要求して、Web API を呼び出します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328551"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Microsoft ID プラットフォーム エンドポイントを使用してマルチテナント デーモンを作成する

このチュートリアルでは、Microsoft ID プラットフォームを使用して、Microsoft の企業顧客のデータに非対話型の長期プロセスでアクセスする方法について説明します。 サンプル デーモンでは、[OAuth2 クライアント資格情報の付与](v2-oauth2-client-creds-grant-flow.md)を使用してアクセス トークンを取得します。その後、これを使用して、[Microsoft Graph](https://graph.microsoft.io) を呼び出して組織のデータにアクセスします。

このアプリは ASP.NET MVC アプリケーションとして作成されており、ユーザーのサインインには OWIN OpenID Connect ミドルウェアを使用します。  このサンプルに含まれる "デーモン" コンポーネントは API コントローラーです。これを呼び出すと、顧客の Azure AD テナントに存在するユーザーのリストが Microsoft Graph からプルされます。  この `SyncController.cs` は、Web アプリケーションの AJAX 呼び出しによってトリガーされ、[Microsoft Authentication Library (MSAL) for .NET](msal-overview.md) を使用して Microsoft Graph のアクセス トークンを取得します。

よりシンプルなコンソール デーモン アプリケーションについては、[.NET Core デーモンのクイックスタート](quickstart-v2-netcore-daemon.md)をご覧ください。

## <a name="scenario"></a>シナリオ

このアプリは Microsoft のあらゆる企業顧客を対象としたマルチテナント アプリであるため、顧客が "サインアップ" (つまり、その会社のデータにアプリケーションを "接続") する手段を備えている必要があります。  接続フローの過程で、社内管理者はまず、サインイン済みのユーザーがいなくてもアプリが非対話型形式で企業データにアクセスできるよう、**アプリケーションのアクセス許可**を直接アプリに付与します。  このサンプルのロジックの大部分は、ID プラットフォームの[管理者の同意](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)エンドポイントを使用してこの接続フローを実現する方法を示します。

![トポロジ](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

このサンプルで使用されている概念の詳細については、[ID プラットフォーム エンドポイント クライアント資格情報プロトコルに関するドキュメント](v2-oauth2-client-creds-grant-flow.md)を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートのサンプルを実行するには、次のものが必要です。

- [Visual Studio 2017 または 2019](https://visualstudio.microsoft.com/downloads/)
- Azure Active Directory (Azure AD) テナント。 Azure AD テナントの取得方法の詳細については、[Azure AD テナントの取得方法](quickstart-create-new-tenant.md)に関するページを参照してください。
- Azure AD テナント内の 1 つ以上のユーザー アカウント。 このサンプルは、Microsoft アカウント (旧称 Windows Live アカウント) では動作しません。 したがって、Microsoft アカウントを使用して [Azure portal](https://portal.azure.com) にサインインしたものの、ディレクトリにユーザー アカウントを作成したことがなければ、この時点でその作業を行う必要があります。

## <a name="clone-or-download-this-repository"></a>このリポジトリをクローンまたはダウンロードする

シェルまたはコマンド ラインから:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

または、[サンプルを ZIP ファイルでダウンロード](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)します。

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>サンプル アプリケーションを Azure AD テナントに登録する

このサンプルには、プロジェクトが 1 つ存在します。 その登録方法は次のとおりです。

- [Azure Active Directory テナントにサンプルを登録する](#register-the-sample-application-with-your-azure-ad-tenant)手順と [Azure AD テナントを使用するようサンプルを構成する](#choose-the-azure-ad-tenant-for-the-applications)手順に従います。
- または、以下を実行する PowerShell スクリプトを使用します。
  - Azure AD アプリケーションとその関連オブジェクト (パスワード、アクセス許可、依存関係) を**自動的**に作成します。
  - Visual Studio プロジェクトの構成ファイルに変更を加えます。

この自動化を使用する場合:

1. Windows で PowerShell を実行し、クローンされたディレクトリのルートに移動します。
1. PowerShell で、次を実行します。

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. スクリプトを実行して Azure AD アプリケーションを作成し、サンプル アプリケーションのコードを適宜構成します。
1. PowerShell で、次を実行します。

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > スクリプトを実行するその他の方法については、[アプリの作成スクリプト](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)に関するページを参照してください。

1. Visual Studio ソリューションを開き、[開始] をクリックしてコードを実行します。

この自動化を使用しない場合は、以下の手順に従ってください。

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>アプリケーションの Azure AD テナントを選択する

最初の手順として、以下のことを実行する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントが複数の Azure AD テナントに存在する場合は、ページ上部のメニューの右上隅にあるプロファイルを選択し、 **[ディレクトリの切り替え]** を選択します。
   ポータル セッションを目的の Azure AD テナントに変更します。

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>クライアント アプリを登録する (dotnet-web-daemon-v2)

1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `dotnet-web-daemon-v2`)。
   - **[サポートされているアカウントの種類]** セクションで、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
   - [リダイレクト URI (省略可能)] セクションで、コンボボックスの **[Web]** を選択し、次のリダイレクト URI を入力します。
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` 複数のリダイレクト URI がある場合は、アプリが正常に作成された後、 **[認証]** タブからそれらを追加する必要があります。
1. **[登録]** を選択して、アプリケーションを作成します。
1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。 これは、このプロジェクトの Visual Studio 構成ファイルを構成するために必要になります。
1. アプリのページの一覧から **[認証]** を選択します。
   - **[詳細設定]** セクションの **[ログアウト URL]** を「`https://localhost:44316/Account/EndSession`」に設定します
   - このサンプルでは、ユーザーのサインインと API の呼び出しのために[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)が有効になっている必要があるため、 **[詳細設定]**  |  **[暗黙の付与]** セクションで **[アクセス トークン]** と **[ID トークン]** をチェックします。
1. **[保存]** を選択します。
1. **[証明書とシークレット]** ページの **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** を選択します。

   - (インスタンス `app secret` の) キーの説明を入力します。
   - キーの有効期間として **[1 年]** 、 **[2 年]** 、または **[有効期限なし]** を選択します。
   - **[追加]** ボタンを押し、キー値が表示されたら、その値をコピーして安全な場所に保存します。
   - このキーは、後から Visual Studio でプロジェクトを構成するために必要となります。 このキー値は、再度表示することも、その他の方法で取得することもできないため、Azure portal から参照できるようになったらすぐに記録してください。
1. アプリのページの一覧から **[API のアクセス許可]** を選択します
   - **[アクセス許可の追加]** をクリックします。さらに、
   - **[Microsoft API]** タブが選択されていることを確認します
   - *[よく使用される Microsoft API]* セクションで、 **[Microsoft Graph]** をクリックします
   - **[アプリケーションのアクセス許可]** セクションで、適切なアクセス許可がオンになっていることを確認します: **User.Read.All**
   - **[アクセス許可の追加]** ボタンを選択します

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Azure AD テナントを使用するようサンプルを構成する

以降の手順の "ClientID" は "アプリケーション ID" (または "AppId") と同じです。

Visual Studio でソリューションを開いてプロジェクトを構成します。

### <a name="configure-the-client-project"></a>クライアント プロジェクトを構成する

セットアップ スクリプトを使用した場合、次の変更が自動的に適用されます。

1. `UserSync\Web.Config` ファイルを開きます。
1. アプリ キー `ida:ClientId` を見つけて、Azure portal からコピーした `dotnet-web-daemon-v2` アプリケーションのアプリケーション ID (clientId) に既存の値を置き換えます。
1. アプリ キー `ida:ClientSecret` を見つけて、Azure portal で `dotnet-web-daemon-v2` アプリの作成時に保存したキーに既存の値を置き換えます。

## <a name="run-the-sample"></a>サンプルを実行する

ソリューションのクリーンを実行し、ソリューションをリビルドして、UserSync アプリケーションを実行します。最初に Azure AD テナントの管理者としてサインインしてください。  テスト用の Azure AD テナントをお持ちでない場合は、[こちらの手順](quickstart-create-new-tenant.md)に従って取得してください。

サインイン時には、まずアプリからサインインとユーザー プロファイルの読み取りのアクセス許可が求められます。  この同意によって、アプリケーションは、あなたがビジネス ユーザーであることを確認できます。

![ユーザーの同意](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

その後、アプリケーションは Microsoft Graph を介して、Azure AD テナントのユーザーのリストを同期しようとします。  それを実行できない場合、テナント管理者は、そのテナントをアプリケーションに接続するよう求められます。

その後、テナント内のユーザーのリストを読み取るためのアクセス許可がアプリケーションから求められます。

![管理者の同意](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**アクセス許可を付与した後、アプリからサインアウトされます**。 これは、トークン キャッシュから Graph の既存のアクセス トークンを確実に削除するために行われます。 再度サインインすると、MS Graph を呼び出すうえで必要なアクセス許可が、取得済みの最新のトークンに割り当てられます。
アクセス許可を付与すると、アプリケーションはいつでもユーザーを照会できるようになります。  このことは、ユーザー ページにある **[ユーザーの同期]** ボタンをクリックし、ユーザーのリストを最新の情報に更新することで確認できます。  ユーザーを追加または削除し、リストを再同期してみてください (ただし、同期されるのは最初のページのユーザーだけであることに注意してください)。

## <a name="about-the-code"></a>コードについて

このサンプルの主立ったコードは、次のファイルにあります。

- 初回サインイン: `App_Start\Startup.Auth.cs`、`Controllers\AccountController.cs`。  特に、コントローラーに対するアクションには Authorize 属性が存在し、これによってユーザーはサインインすることを強制されます。 このアプリケーションでは、ユーザーのサインインに[承認コード フロー](v2-oauth2-auth-code-flow.md)が使用されます。
- ローカルのメモリ内ストアに対してユーザーのリストを同期させる: `Controllers\SyncController.cs`
- ローカルのメモリ内ストアにあるユーザーのリストを表示する: `Controllers\UserController.cs`
- 管理者の同意エンドポイントを使用してテナント管理者からアクセス許可を取得する: `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>このサンプル アプリを再現する

1. Visual Studio で、新しい `Visual C#` `ASP.NET Web Application (.NET Framework)` プロジェクトを作成します。 次の画面で、`MVC` プロジェクト テンプレートを選択します。 後で Web API コントローラーを追加するので、`Web API` の主要な参照とフォルダーも追加してください。  プロジェクトに選択する認証モードは既定値 (`No Authentication`) のままにします。
2. **ソリューション エクスプローラー** ウィンドウでプロジェクトを選択し、**F4** キーを押してプロジェクトのプロパティを表示します。 プロジェクトのプロパティで、 **[SSL 有効]** を `True` に設定します。 **[SSL URL]** をメモしておいてください。 これは、このアプリケーションの登録を Azure portal で構成する際に必要になります。
3. ASP.Net OWIN ミドルウェアの NuGet を追加します (`Microsoft.Owin.Security.ActiveDirectory`、`Microsoft.Owin.Security.Cookies`、`Microsoft.Owin.Host.SystemWeb`、`Microsoft.IdentityModel.Protocol.Extensions`、`Microsoft.Owin.Security.OpenIdConnect`、`Microsoft.Identity.Client`)。 
4. `App_Start` フォルダーに `Startup.Auth.cs` クラスを作成します。 名前空間の名前からは、`.App_Start` を削除してください。  `Startup` クラスのコードを、サンプル アプリの同じファイルにあるコードに置き換えます。  必ずクラス定義全体を置き換えてください。  この定義は、`public class Startup` から `public partial class Startup` に変更されます。
5. `Startup.Auth.cs` で、Visual Studio の IntelliSense によって提示される `using` ステートメントを追加して、不足している参照を解決します。
6. プロジェクトを右クリックして [追加] を選択し、[クラス] を選択して、検索ボックスに「OWIN」と入力します。  "OWIN Startup クラス" が選択肢として表示されたら、それを選択し、クラスに `Startup.cs` という名前を付けます。
7. `Startup.cs` で、`Startup` クラスのコードを、サンプル アプリの同じファイルにあるコードに置き換えます。  この場合も定義は `public class Startup` から `public partial class Startup` に変更されることに注意してください。
8. フォルダーに、`MsGraphUser.cs` という新しいクラスを追加します。  その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
9. `AccountController` という新しい "**MVC 5 コントローラー - 空**" を追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
10. `UserController` という新しい "**MVC 5 コントローラー - 空**" を追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
11. `SyncController` という新しい "**Web API 2 コントローラー - 空**" を追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
12. ユーザー インターフェイスについては、**空の (モデルのない) ビュー**を `GrantPermissions`、`Index`、`UserMismatch` という名前で `Views\Account` フォルダーに 3 つ、`Index` という名前で `Views\User` フォルダーに 1 つ追加します。 その実装コードを、サンプルに含まれている同じ名前のファイルの内容に置き換えます。
13. `Shared\_Layout.cshtml` と `Home\Index.cshtml` を更新して、各種ビューを正しく関連付けます。

## <a name="deploy-this-sample-to-azure"></a>このサンプルを Azure にデプロイする

このプロジェクトには、WebApp プロジェクトと Web API プロジェクトがあります。 それらを Azure Web サイトにデプロイするには、それぞれについて次の作業を行う必要があります。

- Azure Web サイトを作成する
- Web アプリと Web API を Web サイトに発行する
- IIS Express ではなく Web サイトを呼び出すようにそのクライアントを更新する

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>`dotnet-web-daemon-v2` を作成して Azure Web サイトに発行する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左上隅の `Create a resource` をクリックして、 **[Web]**  -->  **[Web アプリ]** の順に選択し、Web サイトに名前を付けます (例: `dotnet-web-daemon-v2-contoso.azurewebsites.net`)。
1. その後、[`Subscription`]、[`Resource Group`]、[`App service plan and Location`] を選択します。 [`OS`] は **[Windows]** に、[`Publish`] は **[コード]** になります。
1. [`Create`] をクリックして、アプリ サービスが作成されるのを待ちます。
1. "`Deployment succeeded`" という通知が表示されたら、[`Go to resource`] をクリックして、新しく作成されたアプリ サービスに移動します。
1. Web サイトが作成されたら、 **[ダッシュボード]** でそれを探してクリックし、**App Services** の **[概要]** 画面を開きます。
1. アプリ サービスの **[概要]** タブで、 **[発行プロファイルの取得]** リンクをクリックして発行プロファイルをダウンロードし、保存します。  ソース管理などの他のデプロイ メカニズムを使用することもできます。
1. Visual Studio に切り替えて、dotnet-web-daemon-v2 プロジェクトに移動します。  ソリューション エクスプローラーでプロジェクトを右クリックし、 **[発行]** を選択します。  下部のバーにある **[プロファイルのインポート]** をクリックし、前にダウンロードした発行プロファイルをインポートします。
1. **[構成]** をクリックし、`Connection tab`で宛先 URL を更新してホーム ページの URL の `https` になるようにします ([https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net) など)。 **[次へ]** をクリックします。
1. [設定] タブで、[`Enable Organizational Authentication`] が選択されて "いない" ことを確認します。  **[Save]** をクリックします。 メイン画面で **[発行]** をクリックします。
1. Visual Studio によってプロジェクトが発行され、ブラウザーでプロジェクトの URL が自動的に開かれます。  プロジェクトの既定の Web ページが表示された場合、発行は成功しています。

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>`dotnet-web-daemon-v2` 用に Azure AD テナント アプリケーションの登録を更新する

1. [Azure ポータル](https://portal.azure.com)に戻ります。
左側のナビゲーション ペインで、 **[Azure Active Directory]** サービスを選択し、 **[アプリの登録]** を選択します。
1. 結果の画面で、`dotnet-web-daemon-v2` アプリケーションを選択します。
1. アプリケーションの **[認証]** ページで、[ログアウト URL] フィールドを実際のサービスのアドレスに更新します (例: [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net))。
1. *[ブランド]* メニューで、 **[ホーム ページ URL]** を実際のサービスのアドレスに更新します (例: [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net))。 構成を保存します。
1. 同じ URL を、 *[認証] -> [リダイレクト URI]* メニューの値のリストに追加します。 複数のリダイレクト URL がある場合は、そのアプリ サービスの URI を使用して、リダイレクト URL ごとに新しいエントリを確保してください。

## <a name="community-help-and-support"></a>コミュニティのヘルプとサポート

[Stack Overflow](http://stackoverflow.com/questions/tagged/msal) を利用すれば、コミュニティからサポートを受けることができます。
質問は最初に Stack Overflow で挙げて、既存の問題を参照して以前に同じ質問が挙がっていないかどうかを確認してください。
自分の質問またはコメントに [`adal` `msal` `dotnet`] タグが付けられていることを確認してください。

サンプルにバグを見つけた場合は、[GitHub の Issues](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues) に問題を提起してください。

MSAL.NET にバグを見つけた場合は、[MSAL.NET GitHub の Issues](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) に問題を提起してください。

ご提案をお寄せいただく場合は、次の [User Voice ページ](https://feedback.azure.com/forums/169401-azure-active-directory)にアクセスしてください。

## <a name="next-steps"></a>次の手順
Microsoft ID プラットフォームでサポートされるさまざまな[認証フローとアプリケーション シナリオ](authentication-flows-app-scenarios.md)について学習します。

詳細については、概念を説明した次のドキュメントを参照してください。

- [Azure Active Directory のテナント](single-and-multi-tenant-apps.md)
- [Azure AD アプリケーションの同意エクスペリエンスについて](application-consent-experience.md)
- [方法: すべての Azure Active Directory ユーザーがマルチテナント アプリケーション パターンを使用してサインインする](howto-convert-app-to-be-multi-tenant.md)
- [ユーザーおよび管理者の同意について](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)
- [クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)
- [クイック スタート:Web API にアクセスするようにクライアント アプリケーションを構成する](quickstart-configure-app-access-web-apis.md)
- [クライアント資格情報フローを使用してアプリケーションのトークンを取得する](msal-client-applications.md)

よりシンプルなマルチテナント コンソール デーモン アプリケーションについては、[.NET Core デーモンのクイックスタート](quickstart-v2-netcore-daemon.md)を参照してください。
