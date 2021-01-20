---
title: Microsoft Graph API を使用してユーザーを管理する
titleSuffix: Azure AD B2C
description: Microsoft Graph API を呼び出し、アプリケーション ID を使用してプロセスを自動化して、Azure AD B2C テナントのユーザーを管理する方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff3cd858de86d21637f4a7a9ab9d9a83c7022f5a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178876"
---
# <a name="manage-azure-ad-b2c-user-accounts-with-microsoft-graph"></a>Microsoft Graph を使用して Azure AD B2C ユーザー アカウントを管理する

Microsoft Graph を使用すると、Microsoft Graph API で作成、読み取り、更新、および削除の各メソッドを指定することで、Azure AD B2C ディレクトリ内のユーザー アカウントを管理できます。 既存のユーザー ストアを Azure AD B2C テナントに移行し、Microsoft Graph API を呼び出すことによって、他のユーザー アカウント管理操作を実行することができます。

以下のセクションでは、Microsoft Graph API を使用した Azure AD B2C ユーザー管理の主要な側面について説明します。 ここで説明する Microsoft Graph API の操作、種類、およびプロパティは、Microsoft Graph API リファレンス ドキュメントに記載されているもののサブセットです。

## <a name="register-a-management-application"></a>管理アプリケーションを登録する

作成したユーザー管理アプリケーションやスクリプトが Azure AD B2C テナント内のリソースと対話できるようにするには、その操作を実行するためのアクセス許可を付与するアプリケーション登録が必要です。

この操作方法に関する記事の手順に従って、管理アプリケーションで使用できるアプリケーション登録を作成します。

[Microsoft Graph を使用して Azure AD B2C を管理する](microsoft-graph-get-started.md)

## <a name="user-management-microsoft-graph-operations"></a>Microsoft Graph のユーザー管理操作

[Microsoft Graph API](/graph/api/resources/user) では次のユーザー管理操作を使用できます。

- [ユーザーの一覧の取得](/graph/api/user-list)
- [ユーザーの作成](/graph/api/user-post-users)
- [ユーザーの取得](/graph/api/user-get)
- [ユーザーの更新](/graph/api/user-update)
- [ユーザーの削除](/graph/api/user-delete)


## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>コード サンプル:プログラムによってユーザー アカウントを管理する方法

このコード サンプルは、[Microsoft Graph SDK](/graph/sdks/sdks-overview) を使用して Microsoft Graph API と対話する .NET Core コンソール アプリケーションです。 このコードは、API を呼び出して、Azure AD B2C テナント内のユーザーをプログラムで管理する方法を示しています。
[サンプル アーカイブ (*.zip) をダウンロード](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)するか、GitHub の[リポジトリを参照する](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)か、リポジトリを複製することができます。

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

コード サンプルを入手したら、環境に合わせて構成し、プロジェクトをビルドします。

1. [Visual Studio](https://visualstudio.microsoft.com) または [Visual Studio Code](https://code.visualstudio.com) でプロジェクトを開きます。
1. `src/appsettings.json`を開きます。
1. 「`appSettings`」セクションで、`your-b2c-tenant` をテナントの名前に、`Application (client) ID` と `Client secret` を管理アプリケーション登録の値に置き換えます (この記事の「[管理アプリケーションを登録する](#register-a-management-application)」のセクションを参照してください)。
1. リポジトリのローカル複製内でコンソール ウィンドウを開き、`src` ディレクトリに切り替えてから、プロジェクトをビルドします。
    ```console
    cd src
    dotnet build
    ```
1. `dotnet` コマンドを使用してアプリケーションを実行します。

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

アプリケーションで、実行可能なコマンドの一覧が表示されます。 たとえば、すべてのユーザーの取得、単一ユーザーの取得、ユーザーの削除、ユーザーのパスワードの更新、一括インポートなどです。

### <a name="code-discussion"></a>コードの説明

このサンプル コードでは、Microsoft Graph にアクセスする高品質かつ効率的で回復性があるアプリケーションを簡単に構築できるように設計されている、[Microsoft Graph SDK](/graph/sdks/sdks-overview) を使用します。

Microsoft Graph API への要求には、認証のためのアクセス トークンが必要になります。 このソリューションでは、Microsoft Graph SDK で使用する Microsoft Authentication Library (MSAL) の認証シナリオベースのラッパーを提供する、 [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet パッケージを使用します。

_Program.cs_ ファイル内の `RunAsync` メソッド:

1. _appsettings.json_ ファイルからアプリケーション設定を読み取ります
1. [OAuth 2.0 クライアント資格情報の付与](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)フローを使用して、認証プロバイダーを初期化します。 クライアント資格情報の付与フローを使用すると、アプリは Microsoft Graph API を呼び出すためのアクセス トークンを取得できます。
1. 認証プロバイダーを使用して Microsoft Graph サービス クライアントを設定します。

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

次に、初期化された *GraphServiceClient* を _UserService.cs_ で使用して、ユーザー管理操作を実行します。 たとえば、次のようにしてテナント内のユーザー アカウントの一覧を取得します。

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Microsoft Graph SDK を使用した API 呼び出し](/graph/sdks/create-requests)に関する記事には、Microsoft Graph から情報を読み書きする方法、`$select` を使用して返されるプロパティを制御する方法、カスタム クエリ パラメーターを指定する方法、`$filter` および `$orderBy` のクエリ パラメーターを使用する方法に関する情報が含まれています。

## <a name="next-steps"></a>次のステップ

Azure AD B2C リソースでサポートされている Microsoft Graph API 操作の完全なインデックスについては、「[Azure AD B2C に使用可能な Microsoft Graph 操作](microsoft-graph-operations.md)」を参照してください。

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
