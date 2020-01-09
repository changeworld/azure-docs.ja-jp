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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423750"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET の AuthenticationResult プロパティ

トークンを取得するメソッドは `AuthenticationResult` (または、非同期メソッドの場合は `Task<AuthenticationResult>`) を返します。

MSAL.NET では、`AuthenticationResult` は次を公開します。

- Web API がリソースにアクセスするための `AccessToken`。 このパラメーターは文字列で、通常は base64 でエンコードされた JWT ですが、クライアントがアクセス トークン内を見ることはありません。 形式が変わらないことは保証されておらず、リソース用に暗号化できます。 クライアント上のアクセス トークンのコンテンツに応じてコードを記述している人は、エラーとクライアント ロジックの中断を起こす最大の原因の 1 つです。 [アクセス トークン](../articles/active-directory/develop/access-tokens.md)も参照してください。
- ユーザーの `IdToken` (このパラメーターはエンコードされた JWT です)。 [ID トークン](../articles/active-directory/develop/id-tokens.md)を参照してください。
- `ExpiresOn` は、トークンの有効期限が切れる日時を知らせます。
- `TenantId` には、ユーザーが存在するテナントが含まれています。 ゲスト ユーザー (Azure AD B2B のシナリオ) の場合、テナント ID は一意のテナントではなく、ゲスト テナントです。
ユーザーにトークンが配信されるときに、`AuthenticationResult` にはこのユーザーに関する情報も含まれます。 (アプリケーションの) ユーザーなしでトークンが要求される機密のクライアント フローの場合、このユーザー情報は null です。
- トークンが発行された `Scopes`。
- ユーザーの一意の ID。

### <a name="iaccount"></a>IAccount

MSAL.NET は、(`IAccount` インターフェイスを通じて) アカウントの概念を定義します。 この破壊的変更によって、適切なセマンティクスが提供されます。つまり、同じユーザーが、異なる Azure AD ディレクトリ内に複数のアカウントを持つことができます。 また、MSAL.NET では、ホーム アカウント情報が提供されるので、ゲスト シナリオについてはより詳細な情報が提供されます。
次の図は、`IAccount` インターフェイスの構造を示しています。

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` クラスは、特定のテナント内のアカウントを識別します。 これは次のプロパティを持ちます。

| プロパティ | [説明] |
|----------|-------------|
| `TenantId` | GUID (アカウントが存在するテナントの ID) の文字列表現。 |
| `ObjectId` | GUID (テナント内でアカウントを所有するユーザーの ID) の文字列表現。 |
| `Identifier` | アカウントの一意識別子。 `Identifier` はコンマで区切られた `ObjectId` と `TenantId` の連結で、base64 でエンコードされていません。 |

`IAccount` インターフェイスは 1 つのアカウントに関する情報を表します。 同じユーザーが異なるテナントに存在することができます。つまり、1 人のユーザーが複数のアカウントを持つことができます。 そのメンバーを次に示します。

| プロパティ | [説明] |
|----------|-------------|
| `Username` | UserPrincipalName (UPN) 形式で表示可能な値を含む文字列 (例: john.doe@contoso.com)。 この文字列は null にできますが、HomeAccountId と HomeAccountId.Identifier は null にすることはできません。 このプロパティは、MSAL.NET の以前のバージョンの `IUser` の `DisplayableId` プロパティを置き換えます。 |
| `Environment` | このアカウントの ID プロバイダーを含む文字列 (例: `login.microsoftonline.com`)。 ここでは値はホストだけですが、`IdentityProvider` が (クラウド環境に加え) テナントに関する情報も持っていた場合を除き、このプロパティは、`IUser` の `IdentityProvider` プロパティを置き換えます。 |
| `HomeAccountId` | ユーザーのホーム アカウントの AccountId。 このプロパティは、Azure AD テナント全体でユーザーを一意に識別します。 |

### <a name="using-the-token-to-call-a-protected-api"></a>トークンを使用して保護された API を呼び出す

MSAL によって `AuthenticationResult` が (`result` で) 返されたら、保護された Web API にアクセスするための呼び出しを行う前に、それを HTTP Authorization ヘッダーに追加する必要があります。

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```