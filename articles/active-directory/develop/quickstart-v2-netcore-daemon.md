---
title: 'クイックスタート: コンソール アプリを使用したトークンの取得と Microsoft Graph の呼び出し | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、.NET Core サンプル アプリでクライアント資格情報フローを使用してトークンを取得し、Microsoft Graph を呼び出す方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/10/2022
ROBOTS: NOINDEX
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, "scenarios:getting-started", "languages:aspnet-core", mode-api
ms.openlocfilehash: 417fb1f2c11822b8c475324c15c27a24a1ff49f0
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803933"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>クイックスタート: コンソール アプリの ID を使用してトークンを取得し、Microsoft Graph API を呼び出す

このクイックスタートでは、.NET Core コンソール アプリケーションでアクセス トークンを取得して Microsoft Graph API を呼び出し、ディレクトリ内の[ユーザーの一覧](/graph/api/user-list)を表示する方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、ユーザーの ID ではなく、アプリケーション ID を使用してジョブまたは Windows サービスを実行する方法も示します。 このクイックスタートのサンプル コンソール アプリケーションは、デーモン アプリケーションでもあるため、機密クライアント アプリケーションです。

## <a name="prerequisites"></a>前提条件

このクイックスタートには [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download) が必要ですが、.NET 5.0 SDK でも動作します。

> [!div class="sxs-lookup"]
### <a name="download-and-configure-your-quickstart-app"></a>クイックスタート アプリをダウンロードして構成する

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
このクイックスタート内のサンプル コードを動作させるには、クライアント シークレットを作成し、Graph API の **User.Read.All** アプリケーション アクセス許可を追加します。
> [!div class="nextstepaction"]
> [これらの変更を行います]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-netcore-daemon/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-your-visual-studio-project"></a>手順 2:Visual Studio プロジェクトをダウンロードする

> [!div class="sxs-lookup"]
> Visual Studio 2019 を使用してプロジェクトを実行します。
> [!div class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-admin-consent"></a>手順 3:管理者の同意

この時点でアプリケーションを実行しようとすると、"*HTTP 403 - 禁止*" エラー "操作を完了するのに十分な特権がありません" が表示されます。 このエラーが発生する理由は、すべてのアプリ専用アクセス許可では、ディレクトリの全体管理者がアプリケーションに同意を与える必要があるからです。 ご自身のロールに応じて、次のオプションのいずれかを選択します。

##### <a name="global-tenant-administrator"></a>グローバル テナント管理者

全体管理者の場合は、 **[API のアクセス許可]** ページに移動し、 **[Enter_the_Tenant_Name_Here に管理者の同意を与えます]** を選択します。
> [!div id="apipermissionspage"]
> [[API のアクセス許可] ページに移動する]()

##### <a name="standard-user"></a>標準ユーザー

テナントの標準ユーザーの場合は、お使いのアプリケーションに管理者の同意を与えるようグローバル管理者に依頼してください。 これを行うには、次の URL を管理者に知らせます。

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

前の URL を使用してアプリに同意を与えた後に、"AADSTS50011: No reply address is registered for the application" (AADSTS50011: アプリケーションの応答アドレスが登録されていません) というエラーが表示される場合があります。 このエラーは、このアプリケーションと URL にリダイレクト URI がないために発生します。 これは無視できます。

#### <a name="step-4-run-the-application"></a>手順 4:アプリケーションの実行

Visual Studio または Visual Studio for Mac を使用している場合は、**F5** を押してアプリケーションを実行します。 それ以外の場合は、コマンド プロンプト、コンソール、またはターミナルを使用してアプリケーションを実行します。

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
このコードでは:
* `{ProjectFolder}` は、.zip ファイルを抽出したフォルダーです。 たとえば `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2` です。

結果として、Azure Active Directory 内のユーザーの一覧が表示されます。

このクイックスタート アプリケーションでは、クライアント シークレットを使用して、それ自体を機密クライアントとして識別します。 クライアント シークレットは、プロジェクト ファイルにプレーン テキスト ファイルとして追加されます。 セキュリティ上の理由により、アプリケーションを運用アプリケーションと見なす前に、クライアント シークレットではなく、証明書を使用することをお勧めします。 証明書の使用方法の詳細については、このサンプルの GitHub リポジトリにある[これらの手順](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates)を参照してください。

## <a name="more-information"></a>詳細情報
このセクションでは、ユーザーをサインインさせるために必要なコードの概要を示します。 この概要は、コードの働きや主な引数、また既存の .NET Core コンソール アプリケーションにサインインを追加する方法を理解するのに役立ちます。

> [!div class="sxs-lookup"]
### <a name="how-the-sample-works"></a>このサンプルのしくみ

![このクイックスタートで生成されたサンプル アプリの動作を示す図。](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft Authentication Library (MSAL。[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) パッケージにある) は、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API にアクセスするためのトークンを要求するために使用されるライブラリです。 このクイックスタートでは、委任されたアクセス許可ではなく、アプリケーション自体の ID を使用してトークンを要求しています。 この場合の認証フローは、[クライアント資格情報 OAuth フロー](v2-oauth2-client-creds-grant-flow.md)と呼ばれます。 クライアント資格情報フローで MSAL.NET を使用する方法の詳細については、[こちらの記事](https://aka.ms/msal-net-client-credentials)を参照してください。

 MSAL.NET をインストールするには、Visual Studio のパッケージ マネージャー コンソールで次のコマンドを実行します。

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL への参照を追加するには、次のコードを追加します。

```csharp
using Microsoft.Identity.Client;
```

その後、次のコードを使用して MSAL を初期化します。

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | 要素 | 説明 |
 |---------|---------|
 | `config.ClientSecret` | Azure portal でアプリケーション用に作成されたクライアント シークレット。 |
 | `config.ClientId` | Azure portal に登録されているアプリケーションのアプリケーション (クライアント) ID。 この値は、Azure portal 内のアプリの **[概要]** ページで確認できます。 |
 | `config.Authority`    | (省略可能) ユーザーが認証するためのセキュリティ トークン サービス (STS) エンドポイント。 通常、パブリック クラウドの場合は `https://login.microsoftonline.com/{tenant}` です。`{tenant}` はテナントの名前またはテナント ID です。|

詳しくは、[`ConfidentialClientApplication` のリファレンス ドキュメント](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)をご覧ください。

### <a name="requesting-tokens"></a>トークンの要求

アプリの ID を使用してトークンを要求するには、`AcquireTokenForClient` メソッドを使用します。

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|要素| 説明 |
|---------|---------|
| `scopes` | 要求されたスコープが含まれています。 機密クライアントの場合、この値では `{Application ID URI}/.default` のような形式を使用する必要があります。 この形式は、要求されたスコープが、Azure portal に設定されたアプリ オブジェクトに静的に定義されているものであることを示します。 Microsoft Graph の場合、`{Application ID URI}` は `https://graph.microsoft.com` を指しています。 カスタム Web API の場合、`{Application ID URI}` は、Azure portal で **[アプリケーションの登録 (プレビュー)]**  >  **[API の公開]** に定義されます。 |

詳しくは、[`AcquireTokenForClient` のリファレンス ドキュメント](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)をご覧ください。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

デーモン アプリケーションの詳細については、シナリオの概要を参照してください。

> [!div class="nextstepaction"]
> [Web API を呼び出すデーモン アプリケーション](scenario-daemon-overview.md)
