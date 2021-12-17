---
title: Microsoft ID プラットフォームの更新トークン | Azure
titleSuffix: Microsoft identity platform
description: Azure AD によって出力される更新トークンについて説明します。
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: shermanouko
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: e4d2721905d1e344cd0825466e0b0eb08578ef47
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688120"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Microsoft ID プラットフォームの更新トークン

クライアントは保護されたリソースにアクセスするためのアクセス トークンを取得するときに、更新トークンも受け取ります。 更新トークンを使用して、現在のアクセス トークンの有効期限が切れたときに、新しいアクセス トークンと更新トークンのペアを取得します。 更新トークンは、他のリソースの追加のアクセス トークンを取得するのにも使用されます。 更新トークンは、ユーザーとクライアントの組み合わせにバインドされていますが、リソースまたはテナントには関連付けられていません。 このため、クライアントは更新トークンを使用して、リソースとテナントの任意の組み合わせで、権限があればアクセス トークンを取得することができます。 更新トークンは暗号化され、Microsoft ID プラットフォームでのみ読み取ることができます。

## <a name="prerequisites"></a>前提条件

この記事を読む前に、次の記事を参照することをお勧めします。

* Microsoft ID プラットフォームの [ID トークン](id-tokens.md)。
* Microsoft ID プラットフォームの[アクセス トークン](access-tokens.md)。

## <a name="refresh-token-lifetime"></a>更新トークンの有効期間

更新トークンの有効期間は、アクセス トークンよりも長くなります。 このトークンの既定の有効期間は 90 日で、使用されるたびに新しいトークンに置き換えられます。 このため、新しいアクセス トークンを取得するために更新トークンが使用されるたびに、新しい更新トークンも発行されます。 Microsoft ID プラットフォームでは、古い更新トークンが、新しいアクセス トークンを取得するために使用されるときに失効になりません。 古い更新トークンは、新しい更新トークンを取得した後に安全に削除します。 更新トークンは、アクセス トークンやアプリケーション資格情報と同様に安全に保存されている必要があります。 

## <a name="refresh-token-expiration"></a>トークンの有効期限を更新する

更新トークンは、タイムアウトと失効により、いつでも失効する可能性があります。 この場合、アプリはサインインサービスによる拒否を正常に処理する必要があります。 これを行うには、対話型のサインイン プロンプトをユーザーに表示して、もう一度サインインさせます。 

### <a name="token-timeouts"></a>トークンのタイムアウト

更新トークンの有効期間を構成することはできません。 有効期間を短縮したり延長したりすることはできません。 条件付きアクセスでのサインインの頻度を構成して、ユーザーが再度サインインするように求められるまでの時間を定義します。 [条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)方法を確認してください。

すべての更新トークンが、トークンの有効期間ポリシーで設定された規則に従うわけではありません。 具体的には、[シングル ページ アプリ](reference-third-party-cookies-spas.md)で使用される更新トークンは、あたかも 24 時間の `MaxAgeSessionSingleFactor` ポリシーが適用されているかのように常に 24 時間のアクティビティに固定されます。 

### <a name="revocation"></a>無効化

資格情報の変更、ユーザーの操作、または管理者の操作が原因で、更新トークンがサーバーによって取り消される場合があります。  更新トークンは、機密クライアント (右端の列) に発行されるトークンと、パブリック クライアント (他のすべての列) に発行されるトークンの 2 つのクラスに分類されます。

| Change | パスワードに基づくクッキー | パスワードに基づくトークン | パスワードに基づかないクッキー | パスワードに基づかないトークン | 機密のクライアントのトークン |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| パスワードが期限切れ | 存続 | 存続 | 存続 | 存続 | 存続 |
| ユーザーによるパスワードの変更 | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| ユーザーがSSPRである | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| 管理者によるパスワードのリセット | 取り消し | 取り消し | 存続 | 存続 | 存続 |
| ユーザーが [PowerShellによって、](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken)更新トークンを無効にする | 取り消し | 取り消し | 取り消し | 取り消し | 取り消し |
| 管理者が [PowerShell によって、](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)ユーザーのすべての更新トークンを無効にする | 取り消し | 取り消し |取り消し | 取り消し | 取り消し |
| [Web での](v2-protocols-oidc.md#single-sign-out)シングル サインアウト | 取り消し | 存続 | 取り消し | 存続 | 存続 |

## <a name="next-steps"></a>次のステップ

* [構成可能なトークンの有効期間](active-directory-configurable-token-lifetimes.md)について学ぶ
* プライマリ更新トークンの詳細については、[プライマリ更新トークン](../devices/concept-primary-refresh-token.md)をご覧ください。
