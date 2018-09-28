---
title: Azure AD v2 Windows UWP クイック スタート | Microsoft Docs
description: ユニバーサル Windows プラットフォーム (XAML) アプリケーションでアクセス トークンを取得し、Azure Active Directory v2.0 エンドポイントで保護されている API を呼び出す方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5241df87297fb2e293e2cc828821e66d6f2837b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970826"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>ユニバーサル Windows プラットフォーム (UWP) アプリケーションから Microsoft Graph API を呼び出す

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

このクイック スタートには、ユニバーサル Windows プラットフォーム (UWP) アプリケーションから個人や仕事、学校のアカウントへのユーザーのサインイン、アクセス トークンの取得、Microsoft Graph API の呼び出しを行う方法を示すコード サンプルが含まれています。

![このクイック スタートで生成されたサンプル アプリの動作](media/quickstart-v2-uwp/uwp-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>登録とダウンロード
> ### <a name="register-and-configure-your-application-and-code-sample"></a>アプリケーションとコード サンプルの登録と構成
> #### <a name="step-1-register-your-application"></a>手順 1: アプリケーションの登録
> アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加するには、次の手順を実行します。
> 1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します。
> 1. **[アプリケーション名]** ボックスに、アプリケーションの名前を入力します。
> 1. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオフになっていることを確認し、**[作成]** を選択します。
> 1. **[プラットフォームの追加]**、**[ネイティブ アプリケーション]**、**[保存]** の順に選択します。

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>手順 1: アプリケーションの構成
> このクイック スタートのコード サンプルを動作させるには、リダイレクト URL として **urn:ietf:wg:oauth:2.0:oob** を追加する必要があります。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-uwp/green-check.png) アプリケーションはこれらの属性で構成されています

#### <a name="step-2-download-your-visual-studio-project"></a>手順 2: Visual Studio プロジェクトのダウンロード

 - [Visual Studio 2017 プロジェクトのダウンロード](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>手順 3: Visual Studio プロジェクトの構成

1. ローカル フォルダー (例: **C:\Azure-Samples**) に zip ファイルを解凍します。
1. Visual Studio でプロジェクトを開きます
1. **App.Xaml.cs** を編集し、`private static string ClientId` で始まる行を以下に置き換えます。

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="more-information"></a>詳細情報

このクイック スタートの概要を以下に示します。

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) はユーザーにサインインし、Microsoft Azure Active Directory によって保護されている API にアクセスするトークンを要求するために使用するライブラリです。 これは、Visual Studio の*パッケージ マネージャー コンソール*で次のコマンドを実行してインストールできます。

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL への参照を追加するには、以下の行を追加します。

```csharp
using Microsoft.Identity.Client;
```

続いて、以下の行を使用して MSAL を初期化します。

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |各値の説明: ||
> |---------|---------|
> |ClientId | *portal.microsoft.com* に登録されているアプリケーションの Application ID |

### <a name="requesting-tokens"></a>トークンの要求

MSAL には、トークンの取得に使用する 2 つのメソッド `AcquireTokenAsync`、`AcquireTokenSilentAsync` があります。

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

 資格情報の検証または同意を行うポップアップ ウィンドウが表示され、Azure Active Directory v2 エンドポイントの操作を強制される場合があります。以下にいくつか例を示します。

- ユーザーが初めてアプリケーションにサインインする
- パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある
- ご使用のアプリケーションが、ユーザーによる同意が必要なリソースへのアクセスを要求している
- 2 要素認証が必須である

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
```

> |各値の説明:||
> |---------|---------|
> |スコープ | 要求するスコープを含む (つまり、Microsoft Graph 用の `{ "user.read" }` またはカスタム Web API 用の `{ "api://<Application ID>/access_as_user" }`) |

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

ユーザーがリソースにアクセスする必要があるたびに、そのユーザーに資格情報の検証を要求する必要はありません - 大抵の場合、ユーザーは何も操作することなく、トークンの取得と更新を行えます - `AcquireTokenSilentAsync` は、最初の `AcquireTokenAsync` の後に保護されたリソースにアクセスするために使用されるトークンを取得するために一般的に使用されるメソッドです:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |各値の説明: ||
> |---------|---------|
> |スコープ | 要求するスコープを含む (つまり、Microsoft Graph 用の `{ "user.read" }` またはカスタム Web API 用の `{ "api://<Application ID>/access_as_user" }`) |
> |accounts.FirstOrDefault() | キャッシュ内の最初のユーザー (MSAL は、1 つのアプリで複数のユーザーをサポート) |

## <a name="next-steps"></a>次の手順

アプリケーションや新機能の構築についての完全なステップ バイ ステップ ガイドは、Windows デスクトップ チュートリアルをお試しください。このクイック スタートの完全な説明も含まれています。

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>このクイック スタートで使用されているアプリケーションを作成する手順

> [!div class="nextstepaction"]
> [UWP -Graph API 呼び出しチュートリアル](tutorial-v2-windows-uwp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]