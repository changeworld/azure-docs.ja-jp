---
title: Azure AD での認証と、OAuth 2.0 を使用した JWT トークンの取得
description: 組織内のセキュリティで保護された Web アプリケーションおよび Web API にアクセスするために OAuth 2.0 を使用して Azure Active Directory で認証を行う方法を示すコード例。
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: eb26101229ad60abae7a8a84f8dfa496488e84ba
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579005"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>Azure Active Directory によってセキュリティで保護された Web API およびアプリケーションにアクセスするための OAuth 2.0 を使用したアクセス トークンの要求

この記事では、Azure AD によってセキュリティで保護されたリソースにアクセスするために JSON Web トークン (JWT) を取得する方法を示します。 ここでは、ユーザーが許可したアクセス許可または[サービス プリンシパル](/azure/active-directory/develop/active-directory-application-objects)のいずれかから[認証トークン](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)を取得していることを前提としています。

## <a name="build-the-request"></a>要求を作成する

Azure AD によってセキュリティで保護された特定のアプリケーションまたは API にアクセスするための JWT を取得するには、次の `POST` HTTP 要求を使用します。

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>要求ヘッダー

次のヘッダーは必須です｡

|要求ヘッダー|説明|  
|--------------------|-----------------|  
| *Host:* | https://login.microsoftonline.com |
| *Content-Type:*| application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>URI パラメーター

| パラメーター     |                       | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | 必須              | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。 詳細については、 [プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。                                                                                                                                                   |
| client_id     | 必須              | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) によってアプリに割り当てられたアプリケーション ID。                                                                                                                                                                                                                             |
| grant_type    | 必須              | 承認コード フローでは `authorization_code` を指定する必要があります。                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | 必須              | スコープのスペース区切りリスト。 この段階で要求するスコープは、`/authorize` 呼び出し内のスコープと同じか、またはそのサブセットである必要があります。 この要求で指定したスコープが複数のリソース サーバーにまたがる場合、v2.0 エンドポイントからは、最初のスコープで指定したリソースのトークンが返されます。 スコープの詳細については、 [アクセス許可、同意、スコープ](v2-permissions-and-consent.md)に関するページを参照してください。 |
| code          | 必須              | `/authorize` 呼び出しで取得した authorization_code                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | 必須              | authorization_code の取得に使用された同じ redirect_uri 値。                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。 ネイティブ アプリでは使用しないでください。デバイスに client_secret を確実に保存することができません。 Web アプリや Web API では client_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。  クライアント シークレットは、送信前に URL エンコードされる必要があります。                                                                                 |
| code_verifier | 省略可能              | authorization_code を取得するために使用されたのと同じ code_verifier。 承認コード付与要求で PKCE が使用された場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>応答の処理

正常なトークン応答には、次のように JWT トークンが含まれます。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| パラメーター     | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | 要求されたアクセス トークン。 アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。                                                                                                                                                                                                                                                                                                                                    |
| token_type    | トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | アクセス トークンの有効期間 (秒)。                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | access_token が有効である範囲。                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | OAuth 2.0 更新トークン。 現在のアクセス トークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のアクセス トークンを取得します。 Refresh_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 詳細については、 [v2.0 トークン リファレンス](v2-id-and-access-tokens.md)を参照してください。 <br> **注:** `offline_access` スコープが要求された場合のみ提供されます。                                               |
| id_token      | 無署名の JSON Web トークン (JWT)。 アプリは、このトークンのセグメントをデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 id_token の詳細については、[v2.0 エンドポイント トークンのリファレンス](v2-id-and-access-tokens.md)を参照してください。 <br> **注:** `openid` スコープが要求された場合のみ提供されます。 |



