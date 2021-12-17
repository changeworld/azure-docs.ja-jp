---
title: Healthcare APIs FHIR サービスのための Azure Active Directory ID の構成
description: FHIR サービスの ID、認証、および承認の原則について説明します
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: cavoeg
ms.openlocfilehash: 43d206c30bd4a3ee043dab3d96247c010f4cdc92
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814644"
---
# <a name="azure-active-directory-identity-configuration-for-fhir-service"></a>FHIR サービスのための Azure Active Directory ID の構成

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

医療データを扱う場合、データがセキュリティで保護されており、承認されていないユーザーやアプリケーションがアクセスできないようにすることが重要です。 FHIR サーバーは [OAuth 2.0](https://oauth.net/2/) を使用して、このデータのセキュリティを確保します。 Azure Healthcare API (ここでは FHIR サービスと呼ばれる) の FHIR サービスは[、OAuth](../../active-directory/index.yml)2.0 ID プロバイダーの例である Azure Active Directory を使用してセキュリティ保護されます。 この記事では、FHIR サーバーの承認の概要と、FHIR サーバーにアクセスするためのトークンを取得するために必要な手順について説明します。 これらの手順は FHIR サーバーと任意の ID プロバイダーに適用されます。この記事では、ID プロバイダーとして FHIR サービスと Azure Active Directory (Azure AD) について説明します。

## <a name="access-control-overview"></a>アクセス制御の概要

クライアント アプリケーションが FHIR サービスにアクセスするには、アクセス トークンを提示する必要があります。 アクセス トークンは、クライアントの ID とロール、およびクライアントに与えられた特権に関する情報を伝達する、署名付きの、[Base64](https://en.wikipedia.org/wiki/Base64) でエンコードされたプロパティ (要求) のコレクションです。

トークンを取得する方法は多数ありますが、トークンが正しい要求を持つ適切に署名されたトークンである限り、FHIR サービスはトークンの取得方法を気にしません。 

[承認コード フロー](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)を例として使用すると、FHIR サーバーへのアクセスでは、次の 4 つの手順が実行されます。

![FHIR の承認](media/azure-active-directory-fhir-service/fhir-authorization.png)

1. クライアントは Azure AD の `/authorize` エンドポイントに要求を送信します。 Azure AD は、クライアントをサインイン ページにリダイレクトします。そこで、ユーザーは適切な資格情報 (ユーザー名とパスワード、または 2 要素認証など) を使用して認証されます。 [承認コードの取得](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)に関する詳細を参照してください。 認証が成功すると、"*承認コード*" がクライアントに返されます。 この承認コードの返却先として Azure AD で許可されるのは、クライアント アプリケーションの登録で構成された登録済みの応答 URL のみになります (下記参照)。
1. クライアント アプリケーションは、Azure AD の `/token` エンドポイントで "*アクセス トークン*" の承認コードを交換します。 クライアント アプリケーションは、トークンを要求するときに、クライアント シークレット (アプリケーション パスワード) を提供する必要がある場合があります。 [アクセス トークンの取得](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)に関する詳細を参照してください。
1. クライアントは、すべての患者を検索する `GET /Patient` など、FHIR サービスに対して要求を行います。 要求を行うときには、HTTP 要求ヘッダーにアクセス トークンが含まれます (`Authorization: Bearer eyJ0e...` など)。この場合、`eyJ0e...` は Base64 でエンコードされたアクセス トークンを表します。
1. FHIR サービスは、トークンに適切な要求 (トークン内のプロパティ) が含まれていることを検証します。 すべてがチェックされると、要求が完了し、結果と共に FHIR バンドルがクライアントに返されます。

FHIR サービスはユーザーの資格情報の検証には関与せず、トークンの発行も行わないことに注意してください。 認証とトークンの作成は Azure AD によって行われます。 FHIR サービスは、トークンが正しく署名されている (本物である) こと、および適切な要求が含まれていることを検証するだけです。

## <a name="structure-of-an-access-token"></a>アクセス トークンの構造

FHIR アプリケーションの開発には、多くの場合、アクセスに関する問題のデバッグが伴います。 クライアントが FHIR サービスへのアクセスを拒否された場合に、アクセス トークンの構造と、トークンの内容 (要求) を検査するためにそれをデコードする方法について理解しておくと役立ちます。 

FHIR サーバーでは、通常、[JSON Web トークン](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT、"ジョット" と発音する場合がある) が想定されています。 これは、次の 3 つの部分で構成されています。

**パート 1**: ヘッダー。次のようになります。
```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
```

**パート 2**: ペイロード (要求)。次に例を示します。
```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
```

**パート 3**: 署名。これは、Base64 でエンコードされたヘッダーとペイロードのコンテンツを連結し、ヘッダーに指定されたアルゴリズム (`alg`) に基づいてそれらの暗号化ハッシュを計算することによって計算されます。 サーバーは、ID プロバイダーから公開キーを取得し、このトークンが特定の ID プロバイダーによって発行されたものであり、改ざんされていないことを検証できます。

完全なトークンは、これら 3 つのセグメントの Base64 でエンコードされた (実際には Base64 URL でエンコードされた) バージョンで構成されます。 これらの 3 つのセグメントは連結され、`.` (ドット) で区切られます。

トークンの例を次に示します。

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

トークンは、[https://jwt.ms](https://jwt.ms) などのツールを使用してデコードおよび検査できます。 トークンをデコードした結果は次のようになります。

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>アクセス トークンを取得する

前述のように、Azure AD からトークンを取得するにはいくつかの方法があります。 詳細については、[Azure AD 開発者向けドキュメント](../../active-directory/develop/index.yml)を参照してください。

Azure AD には、`v1.0` および `v2.0` と呼ばれる、2 つの異なるバージョンの OAuth 2.0 エンドポイントがあります。 これらのバージョンはいずれも OAuth 2.0 エンドポイントであり、`v1.0` と `v2.0` の指定は、Azure AD でのその標準の実装方法における違いを示しています。 

FHIR サーバーを使用する場合は、`v1.0` または `v2.0` エンドポイントのいずれかを使用できます。 その選択は、クライアント アプリケーションで使用している認証ライブラリによって異なる場合があります。

Azure AD ドキュメントの該当するセクションは次のとおりです。

* `v1.0` エンドポイント:
    * [承認コード フロー](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)。
    * [クライアントの資格情報フロー](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)。
* `v2.0` エンドポイント:
    * [承認コード フロー](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。
    * [クライアントの資格情報フロー](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。

トークンを取得するためのその他のバリエーション (フローの代わりなど) もあります。 詳細については、Azure AD のドキュメントを参照してください。 FHIR サービスを使用している場合、[Azure CLI を使用して](get-healthcare-apis-access-token-cli.md) (デバッグ目的で) アクセス トークンを取得するためのいくつかのショートカットもあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure AD を使用した FHIR サービスへのアクセスのセキュリティ保護に関連する基本的な概念をいくつか学習しました。 FHIR サービスをデプロイする方法の詳細については、以下を参照してください

>[!div class="nextstepaction"]
>[FHIR サービスをデプロイする](fhir-portal-quickstart.md)
