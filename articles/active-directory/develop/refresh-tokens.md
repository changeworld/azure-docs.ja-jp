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
ms.openlocfilehash: 8b8bda71c637419dbd5b2bf7b181288abd9b965c
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112075243"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Microsoft ID プラットフォームの更新トークン

クライアントは保護されたリソースにアクセスするためのアクセス トークンを取得するときに、更新トークンも受け取ります。 更新トークンを使用して、現在のアクセス トークンの有効期限が切れたときに、新しいアクセス トークンと更新トークンのペアを取得します。 更新トークンは、他のリソースの追加のアクセス トークンを取得するのにも使用されます。 更新トークンは、ユーザーとクライアントの組み合わせにバインドされていますが、リソースまたはテナントには関連付けられていません。 このため、クライアントは更新トークンを使用して、リソースとテナントの任意の組み合わせで、権限があればアクセス トークンを取得することができます。 更新トークンは暗号化され、Microsoft ID プラットフォームでのみ読み取ることができます。

## <a name="prerequisites"></a>前提条件

この記事を読む前に、次の記事を参照することをお勧めします。

* Microsoft ID プラットフォームの [ID トークン](id-tokens.md)。
* Microsoft ID プラットフォームの[アクセス トークン](access-tokens.md)。

## <a name="refresh-token-lifetime"></a>更新トークンの有効期間

更新トークンの有効期間は、アクセス トークンよりも大幅に長くなります。 このトークンの既定の有効期間は 90 日で、使用されるたびに新しいトークンに置き換えられます。 これは、新しいアクセス トークンを取得するために更新トークンが使用されるたびに、新しい更新トークンも発行されることを意味します。 Microsoft ID プラットフォームでは、古い更新トークンが、新しいアクセス トークンを取得するために使用されるときに失効になりません。 古い更新トークンは、新しい更新トークンを取得した後に安全に削除します。 更新トークンは、アクセス トークンやアプリケーション資格情報と同様に安全に保存されている必要があります。 

## <a name="refresh-token-expiration"></a>トークンの有効期限を更新する

更新トークンは、タイムアウトと失効により、いつでも失効する可能性があります。 この場合、アプリはサインインサービスによる拒否を正常に処理する必要があります。 これを行うには、対話型のサインイン プロンプトをユーザーに表示して、もう一度サインインさせます。 

### <a name="token-timeouts"></a>トークンのタイムアウト

[トークンの有効期間の構成](active-directory-configurable-token-lifetimes.md#refresh-and-session-token-lifetime-policy-properties)を使用すると、更新トークンの有効期間を短縮または延長できます。 この設定で、更新トークンを使用せずに実行できる時間の長さを変更します。 たとえば、ユーザーが 90 日を超えてアプリを開かないシナリオを考えてみます。 アプリで 90 日より前の更新トークンを使用しようとしたときに、有効期限が切れていることが検出されます。 さらに、管理者が、第 2 の要素を定期的に使用することを要求して、特定の間隔でユーザーに手動でサインインさせることができます。 これらのシナリオは、次のとおりです。

* 非アクティブ: 更新トークンは、`MaxInactiveTime` で指定した期間のみ有効です。  トークンがその期間内に使用されない (新しいトークンに置き換えられない) と、そのトークンは使用できなくなります。
* セッション タイムアウト: `MaxAgeSessionMultiFactor` または `MaxAgeSessionSingleFactor` を既定値 (Until-revoked) 以外に設定している場合、MaxAgeSession* に設定した時間が経過すると、再認証が必要になります。  これは、ユーザーに第 1 または第 2 の要素を定期的に再認証させるために使用します。 
* 例 :
  * テナントの MaxInactiveTime を 5 日間に設定し、ユーザーが 1 週間の休暇をとりました。 そのため、Azure AD では、このユーザーからの新しいトークン要求が 7 日間検出されません。 ユーザーが次に新しいトークンを要求するとき、更新トークンが失効していることがわかります。資格情報を再び入力する必要があります。
  * 機密性の高いアプリケーションの `MaxAgeSessionMultiFactor` を 1 日に設定します。 ユーザーが 1 日経過後に再度サインインした場合、対話型プロンプトを介して MFA をもう一度実行する必要があります。 たとえば、ユーザーが月曜日にログインし、25 時間経過後の火曜日にログインした場合などです。 

すべての更新トークンが、トークンの有効期間ポリシーで設定された規則に従うわけではありません。 具体的には、[シングル ページ アプリ](reference-third-party-cookies-spas.md)で使用される更新トークンは、あたかも 24 時間の `MaxAgeSessionSingleFactor` ポリシーが適用されているかのように常に 24 時間のアクティビティに制限されます。 

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
