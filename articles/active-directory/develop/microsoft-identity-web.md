---
title: Microsoft Identity Web 認証ライブラリの概要
titleSuffix: Microsoft identity platform
description: Microsoft Identity Web (Azure Active Directory、Azure AD B2C、Microsoft Graph などの Web API と統合される ASP.NET Core アプリケーション用の認証および承認ライブラリ) について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8bc9773fa7e1b3db2e249e803f4d2a3cf39edb48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102548"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Identity Web 認証ライブラリ

Microsoft Identity Web は一連の ASP.NET Core ライブラリで、これにより、Microsoft ID プラットフォームと統合される Web アプリおよび Web API への認証および承認サポートの追加が簡略化されます。 ASP.NET Core、その認証ミドルウェア、[.NET 用の Microsoft Authentication Library (MSAL)](https://github.com/azuread/microsoft-authentication-library-for-dotnet) を結び付ける単一サーフェス API の便利なレイヤーを提供します。

## <a name="supported-application-scenarios"></a>サポートされているアプリケーション シナリオ

ASP.NET Core Web アプリまたは Web API をビルドしていて、ID とアクセスの管理 (IAM) に Azure Active Directory (Azure AD) または Azure AD B2C を使用する場合は、次のすべてのシナリオで Microsoft Identity Web を使用することをお勧めします。

- [ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)
- [ユーザーをサインインさせ、そのユーザーに代わって Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md)
- [認証されたユーザーのみアクセスできる、保護された Web API](scenario-protected-web-api-overview.md)
- [サインイン ユーザーの代わりに別の (ダウンストリーム) Web API を呼び出す、保護された Web API](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>ライブラリの入手

Microsoft Identity Web は [NuGet](#nuget)、[.NET Core プロジェクト テンプレート](#project-templates)、および [GitHub](#github) から入手できます。

#### <a name="nuget"></a>NuGet

Microsoft Identity Web は、アプリのニーズに応じたモジュール型機能を提供する一連のパッケージとして、NuGet で入手できます。 .NET CLI の `dotnet add` コマンドまたは Visual Studio の **NuGet パッケージ マネージャー** を使用して、プロジェクトに適したパッケージをインストールします。

- [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) - メイン パッケージ。 Microsoft Identity Web を使用するすべてのアプリに必要です。
- [Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) - 省略可能。 Web アプリに対するユーザーのサインインとサインアウトおよび関連付けられているコントローラーの UI を追加します。
- [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) - 省略可能。 Microsoft Graph API との簡単な対話機能を提供します。
- [Microsoft.Identity.Web.MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) - 省略可能。 Microsoft Graph API [ベータ版エンドポイント](/graph/api/overview?view=graph-rest-beta&preserve-view=true)との簡単な対話機能を提供します。

#### <a name="project-templates"></a>プロジェクト テンプレート

Microsoft Identity Web プロジェクト テンプレートは .NET 5.0 に含まれており、ASP.NET Core 3.1 プロジェクト用にダウンロードできます。

ASP.NET Core 3.1 を使用する場合は、.NET CLI を使用してテンプレートをインストールします。

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

次の図は、サポートされているアプリの種類とその関連する引数の概要を示しています。

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Microsoft Identity Web 用の利用可能な .NET CLI プロジェクト テンプレートの図":::
<br /><sup><b>*</b></sup> `MultiOrg` は `webapi2` ではサポートされていませんが、テナントを `common` または `organizations` に設定することにより、*appsettings.json* で有効にすることができます
<br /><sup><b>**</b></sup> `--calls-graph` は、Azure AD B2C ではサポートされていません

[Blazor Server チュートリアル](tutorial-blazor-server.md)から取得した次の .NET CLI コマンドの例では、適切なパッケージおよびスタート コード (プレースホルダー値を表示) を含む新しい Blazor Server プロジェクトを生成します。

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web は、GitHub でホストされているオープンソース プロジェクトです。<a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/microsoft-identity-web</a>

[リポジトリ wiki](https://github.com/AzureAD/microsoft-identity-web/wiki) には追加のドキュメントが含まれています。ヘルプが必要な場合、またはバグを発見した場合は、[問題を報告](https://github.com/AzureAD/microsoft-identity-web/issues)できます。

## <a name="features"></a>特徴

Microsoft Identity Web には、既定の ASP.NET 3.1 プロジェクト テンプレートを使用する場合に提供されないいくつかの機能が含まれています。

| 機能                                                                                  | ASP.NET Core 3.1                                                     | Microsoft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| Web アプリでの[ユーザーのサインイン](scenario-web-app-sign-user-app-configuration.md)             | <li>職場または学校アカウント<li>ソーシャル ID (Azure AD B2C) | <li>職場または学校アカウント<li>個人用 Microsoft アカウント<li>ソーシャル ID (Azure AD B2C)     |
| [Web API の保護](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>職場または学校アカウント<li>ソーシャル ID (Azure AD B2C) | <li>職場または学校アカウント<li>個人用 Microsoft アカウント<li>ソーシャル ID (Azure AD B2C)     |
| マルチテナント アプリでの発行者の検証                                                   | いいえ                                                                   | はい ([すべてのクラウド](authentication-national-cloud.md)と [Azure AD B2C](../../active-directory-b2c/index.yml)) |
| Web アプリ/API による [Microsoft Graph の呼び出し][scenario-api-call-graph]                             | いいえ                                                                   | はい                                                                                                     |
| Web アプリ/API による [Web API の呼び出し][scenario-api-call-api]                                       | いいえ                                                                   | はい                                                                                                     |
| 証明書資格情報のサポート                                                         | いいえ                                                                   | はい (Azure Key Vault を含む)                                                                          |
| Web アプリでの増分同意と条件付きアクセスのサポート                           | いいえ                                                                   | はい (MVC、Razor ページ、Blazor)                                                                    |
| Web API でのトークン暗号化証明書                                                | いいえ                                                                   | はい                                                                                                     |
| Web API での [スコープ/アプリ ロールの検証][scenario-api-validation]                        | いいえ                                                                   | はい                                                                                                     |
| Web API での `WWW-Authenticate` ヘッダーの生成                                         | いいえ                                                                   | はい                                                                                                     |

## <a name="next-steps"></a>次の手順

Microsoft ID Web の動作を確認するには、次の Blazor Server のチュートリアルをお試しください。

[チュートリアル: 認証に Microsoft ID プラットフォームを使用する Blazor Server アプリを作成する](tutorial-blazor-server.md)

GitHub の Microsoft Identity Web wiki には、ライブラリのさまざまな側面に関する広範なリファレンス ドキュメントが含まれています。 たとえば、証明書の使用状況、増分同意、条件付きアクセスのリファレンスは、次の場所にあります。

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Microsoft.Identity.Web での証明書の使用</a> (GitHub)
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">増分同意と条件付きアクセス</a> (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[scenario-api-call-api]: scenario-web-api-call-api-call-api.md#option-1-call-microsoft-graph-with-the-sdk  
[scenario-api-call-graph]: scenario-web-api-call-api-call-api.md#option-1-call-microsoft-graph-with-the-sdk  
[scenario-api-validation]: scenario-protected-web-api-verification-scope-app-roles.md  
