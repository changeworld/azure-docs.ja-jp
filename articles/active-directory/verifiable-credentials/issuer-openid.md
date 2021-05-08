---
title: 発行者サービスの通信の例 (プレビュー) - Azure Active Directory Verifiable Credentials
description: ID プロバイダーと発行者サービスの間の通信の詳細
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 8771c61f96b244e0cc0bca1c61ceb8042b4a5b4c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220200"
---
# <a name="issuer-service-communication-examples-preview"></a>発行者サービスの通信の例 (プレビュー)

検証可能な資格情報の発行者サービスは、お客様の組織の OpenID に準拠している ID プロバイダーによって生成された ID トークンからクレームを取得することによって、検証可能な資格情報を発行できます。 この記事では、Authenticator が ID プロバイダーと通信し、発行サービスに渡す正しい ID トークンを取得できるように、ID プロバイダーを設定する方法について説明します。 

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


検証可能な資格情報を発行するために、Authenticator は、コントラクトのダウンロードを通して、ユーザーからの入力を収集してその情報を発行サービスに送信するように指示されます。 ID トークンを使用する必要がある場合は、Authenticator が OpenID Connect プロトコルを使用してユーザーをサインインできるように ID プロバイダーを設定する必要があります。 結果として得られる ID トークン内のクレームは、検証可能な資格情報の内容を設定するために使用されます。 Authenticator は、OpenID Connect 認可コード フローを使用してユーザーを認証します。 OpenID プロバイダーでは、次の OpenID Connect 機能がサポートされている必要があります。 

| 機能 | 説明 |
| ------- | ----------- |
| [付与タイプ] | 認可コードの付与タイプがサポートされている必要があります。 |
| トークンの形式 | 暗号化されていないコンパクト JWT を生成する必要があります。 |
| 署名アルゴリズム | RSA 256 を使用して署名された JWT を生成する必要があります。 |
| 構成ドキュメント | OpenID Connect 構成ドキュメントと `jwks_uri` がサポートされている必要があります。 | 
| クライアントの登録 | `vclient://openid/` の `redirect_uri` 値を使用したパブリック クライアントの登録がサポートされている必要があります。 | 
| PKCE | セキュリティ上の理由から推奨されますが、必須ではありません。 |

ID プロバイダーに送信される HTTP 要求の例は、下にあります。 ID プロバイダーは、OpenID Connect 認証標準に従って、これらの要求を受け入れて応答する必要があります。

## <a name="client-registration"></a>クライアントの登録

検証可能な資格情報を受け取るには、お客様のユーザーが Microsoft Authenticator アプリからお客様の IDP にサインインする必要があります。 

この交換を可能にするには、アプリケーションを ID プロバイダーに登録します。 Azure AD を使用している場合は、[こちら](../develop/quickstart-register-app.md)に手順があります。 登録時には、次の値を使用します。

| 設定 | 値 |
| ------- | ----- |
| アプリケーション名 | `<Issuer Name> Verifiable Credential Service` |
| リダイレクト URI | `vcclient://openid/ ` |


アプリケーションを ID プロバイダーに登録したら、そのクライアント ID を記録します。 これは、後述のセクションで使用します。 また、OIDC と互換性のある ID プロバイダーの既知のエンドポイントへの URL も書き留めておく必要があります。 発行サービスは、このエンドポイントを使用して、Authenticator によって送信される ID トークンを検証するために必要な公開キーをダウンロードします。

構成されたリダイレクト URI は Authenticator によって使用されるので、Authenticator がサインインが完了したタイミングを認識し、ID トークンを取得できます。 

## <a name="authorization-request"></a>Authorization request (承認要求)

ID プロバイダーに送信される認可要求には、次の形式が使用されます。

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| パラメーター | 値 |
| ------- | ----------- |
| `client_id` | アプリケーションの登録プロセス中に取得されるクライアント ID。 |
| `redirect_uri` | `vcclient://openid/` を使用する必要があります。 |
| `response_mode` | `query` がサポートされている必要があります。 |
| `response_type` | `code` がサポートされている必要があります。 |
| `scope` | `openid` がサポートされている必要があります。 |
| `state` | OpenID Connect 標準に従って、クライアントに返される必要があります。 |
| `nonce` | OpenID Connect 標準に従って、ID トークンのクレームとして返される必要があります。 |

ID プロバイダーは認可要求を受信すると、ユーザーを認証し、サインインを完了するために必要なすべての手順 (多要素認証など) を実行する必要があります。

ニーズに合わせてサインイン プロセスをカスタマイズできます。 追加情報の提供、サービス使用条件への同意、資格情報の料金の支払いなどを、ユーザーに求めることができます。 すべての手順が完了したら、下に示すようなリダイレクト URI にリダイレクトすることで、認可要求に応答します。 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| パラメーター | 値 |
| ------- | ----------- |
| `code` |  ID プロバイダーによって返される認可コード。 |
| `state` | OpenID Connect 標準に従って、クライアントに返される必要があります。 |

## <a name="token-request"></a>トークン要求

ID プロバイダーに送信されるトークン要求の形式は次のとおりです。

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| パラメーター | 値 |
| ------- | ----------- |
| `client_id` | アプリケーションの登録プロセス中に取得されるクライアント ID。 |
| `redirect_uri` | `vcclient://openid/` を使用する必要があります。 |
| `scope` | `openid` がサポートされている必要があります。 |
| `grant_type` | `authorization_code` がサポートされている必要があります。 |
| `code` | ID プロバイダーによって返される認可コード。 |

トークン要求の受信時、ID プロバイダーは ID トークンで応答する必要があります。

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

ID トークンは JWT コンパクト シリアル化形式を使用する必要があり、暗号化することはできません。 ID トークンには、次のクレームが含まれている必要があります。

| 要求 | 値 |
| ------- | ----------- |
| `kid` | OpenID プロバイダーの `jwks_uri` 内のエントリに対応する、ID トークンの署名に使用するキーのキー識別子。 |
| `aud` | アプリケーションの登録プロセス中に取得されるクライアント ID。 |
| `iss` | OpenID Connect 構成ドキュメントの `issuer` 値でなければなりません。 |
| `exp` | ID トークンの有効期限が含まれている必要があります。 |
| `iat` | トークンが発行された時刻が含まれている必要があります。 |
| `nonce` | 認可要求に含まれる値。 |
| その他の要求 | 発行される検証可能な資格情報に設定する予定の値が入っているその他のクレームがあれば、ID トークンに含める必要があります。 このセクションに、ユーザーに関する属性 (名前など) を含める必要があります。 |

## <a name="next-steps"></a>次の手順

- [Azure Active Directory Verifiable Credentials をカスタマイズする方法](credential-design.md)