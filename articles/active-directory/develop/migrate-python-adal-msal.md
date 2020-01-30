---
title: Python ADAL から MSAL への移行ガイド | Azure
description: Azure Active Directory 認証ライブラリ (ADAL) Python アプリを、Python 用の Microsoft 認証ライブラリ (MSAL) に移行する方法について説明します。
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696559"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Python 用の ADAL から MSAL への移行に関するガイド

この記事では、Microsoft Authentication Library (MSAL) を使用するために、Azure Active Directory Authentication Library (ADAL) を使っているアプリを移行するのに必要な変更について重点的に説明します。

## <a name="difference-highlights"></a>主な相違点

ADAL は、Azure Active Directory (Azure AD) v1.0 エンドポイントで動作します。 Microsoft Authentication Library (MSAL) は、以前は Azure Active Directory v2.0 エンドポイントと呼ばれていた、Microsoft ID プラットフォームと連動します。 Microsoft ID プラットフォームは、以下の点で Azure AD v1.0 とは異なります。

サポートするものは次のとおりです。
  - 職場または学校のアカウント (Azure AD によりプロビジョニングされるアカウント)
  - 個人アカウント (Outlook.com や Hotmail.com など)
  - Azure AD B2C サービスから自分のメールやソーシャル ID (LinkedIn、Facebook、Google など) を持ち込むカスタマー

- 標準は以下のものと互換性があります。
  - OAuth v2.0
  - OpenID Connect (OIDC)

詳細については、[Microsoft ID プラットフォーム (v2.0) エンドポイントについての違い](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)に関するページを参照してください。

### <a name="scopes-not-resources"></a>リソースではなくスコープ

ADAL Python ではリソースのトークンが取得されますが、MSAL Python ではスコープのトークンが取得されます。 MSAL Python の API サーフェスには、リソース パラメーターがなくなりました。 要求される必要なアクセス許可とリソースを宣言する文字列のリストとして、スコープを指定する必要があります。 スコープの例については、[Microsoft Graph のスコープ](https://docs.microsoft.com/graph/permissions-reference)に関するページを参照してください。

### <a name="error-handling"></a>エラー処理

Python 用 Azure Active Directory 認証ライブラリ (ADAL) では、例外 `AdalError` を使用して問題が発生したことを示します。 Python 用 MSAL では通常、代わりにエラー コードを使用します。 詳細については、[Python 用 MSAL のエラー処理](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python)に関するページを参照してください。

### <a name="api-changes"></a>API の変更

次の表は、Python 用 ADAL の API と、Python 用 MSAL でその代わりに使用する API の一覧です。

| Python 用 ADAL の API  | Python 用 MSAL の API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication or ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| 該当なし  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | 該当なし |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) および [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) および [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| 該当なし | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| 該当なし | 永続性を備えたキャッシュ、[MSAL 拡張機能](https://github.com/marstr/original-microsoft-authentication-extensions-for-python)で提供 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>MSAL Python の既存の更新トークンを移行する

Microsoft 認証ライブラリ (MSAL) では、更新トークンの概念が抽象化されています。 MSAL Python では、更新トークンを保存、検索、または更新する必要がないように、既定でメモリ内トークン キャッシュが提供されます。 更新トークンは通常、ユーザーの介入なしに更新できるため、ユーザーにサインイン プロンプトが表示される回数も少なくなります。 トークン キャッシュの詳細については、[Python 用 MSAL でのカスタム トークン キャッシュのシリアル化](msal-python-token-cache-serialization.md)に関するページを参照してください。

次のコードは、別の OAuth2 ライブラリ (ADAL Python を含むがこれに限らない) によって管理されている更新トークンを、Python 用 MSAL による管理に移行するために役立ちます。 これらの更新トークンを移行する理由の 1 つは、アプリを Python 用 MSAL に移行するときに既存のユーザーがサインインし直す必要をなくすことです。

更新トークンを移行するには、Python 用 MSAL と以前の更新トークンを使用して新しいアクセス トークンを取得します。 新しい更新トークンが返されると、Python 用 MSAL によってキャッシュに保存されます。 これを行う方法の例を次に示します。

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>次のステップ

詳細については、[v1.0 と v2.0 の比較](active-directory-v2-compare.md)に関するページを参照してください。
