---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76308904"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET の AuthenticationResult プロパティ

トークンを取得するメソッドでは、`AuthenticationResult` が返されます。 非同期メソッドでは、`Task<AuthenticationResult>` が返されます。

MSAL.NET では、`AuthenticationResult` は次を公開します。

- Web API がリソースにアクセスするための `AccessToken`。 このパラメーターは、通常は base 64 でエンコードされた JWT の文字列です。 クライアントがアクセス トークン内を見ることはありません。 この形式が変わらないことは保証されておらず、リソース用に暗号化できます。 クライアント上のアクセス トークンのコンテンツに応じてコードを記述することは、エラーとクライアント ロジックの中断を起こす最大の原因の 1 つです。 詳細については、「[アクセス トークン](../articles/active-directory/develop/access-tokens.md)」を参照してください。
- ユーザーの `IdToken`。 このパラメーターは、エンコードされた JWT です。 詳細については、[ID トークン](../articles/active-directory/develop/id-tokens.md)に関するページを参照してください。
- トークンの有効期限の日時は、`ExpiresOn` から知ることができます。
- `TenantId` には、ユーザーが存在するテナントが含まれています。 Azure Active Directory (Azure AD) B2B のシナリオのゲスト ユーザーの場合、テナント ID は一意のテナントではなく、ゲスト テナントです。
ユーザーにトークンが配信されるときに、`AuthenticationResult` にはこのユーザーに関する情報も含まれます。 アプリケーションのユーザーなしでトークンが要求される機密のクライアント フローの場合、このユーザー情報は null です。
- トークンが発行された `Scopes`。
- ユーザーの一意の ID。

### <a name="iaccount"></a>IAccount

MSAL.NET では、`IAccount` インターフェイスでアカウントの概念が定義されます。 この破壊的変更により、正しいセマンティクスが得られます。 同じユーザーは、異なる Azure AD ディレクトリに複数のアカウントを持つことができます。 また、MSAL.NET では、ホーム アカウント情報が提供されるので、ゲスト シナリオについてはより詳細な情報が提供されます。
次の図は、`IAccount` インターフェイスの構造を示しています。

![IAccount インターフェイスの構造](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` クラスでは、次の表のプロパティで、特定のテナントのアカウントを識別します。

| プロパティ | 説明 |
|----------|-------------|
| `TenantId` | GUID (アカウントが存在するテナントの ID) の文字列表現。 |
| `ObjectId` | GUID (テナント内でアカウントを所有するユーザーの ID) の文字列表現。 |
| `Identifier` | アカウントの一意識別子。 `Identifier` は `ObjectId` と `TenantId` をコンマで区切って連結したものです。 base 64 ではエンコードされていません。 |

`IAccount` インターフェイスは 1 つのアカウントに関する情報を表します。 同じユーザーが異なるテナントに存在することができます。つまり、1 人のユーザーが複数のアカウントを持つことができます。 そのメンバーは、次の表のとおりです。

| プロパティ | 説明 |
|----------|-------------|
| `Username` | UserPrincipalName (UPN) 形式の表示可能な値の文字列 (例: john.doe@contoso.com)。 null にすることができない HomeAccountId と HomeAccountId.Identifier とは異なり、この文字列は null にできます。 このプロパティは、MSAL.NET の以前のバージョンの `IUser` の `DisplayableId` プロパティを置き換えます。 |
| `Environment` | このアカウントの ID プロバイダーを含む文字列 (例: `login.microsoftonline.com`)。 `IdentityProvider` には、クラウド環境に加えテナントに関する情報もありますが、それを除き、このプロパティは、`IUser` の `IdentityProvider` プロパティと置き換えることができます。 ここでは値はホストのみです。 |
| `HomeAccountId` | ユーザーのホーム アカウントのアカウント ID。 このプロパティは、Azure AD テナント全体でユーザーを一意に識別します。 |

### <a name="use-the-token-to-call-a-protected-api"></a>トークンを使用して保護された API を呼び出す

MSAL によって `result` に `AuthenticationResult` が返された後、保護された Web API にアクセスする呼び出しを行う前に、これを HTTP Authorization ヘッダーに追加します。

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```