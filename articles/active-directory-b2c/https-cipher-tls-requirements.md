---
title: TLS と暗号スイートの要件 - Azure AD B2C
titleSuffix: Azure AD B2C
description: Web API エンドポイントと対話する際の HTTPS 暗号スイートと TLS 要件に関する開発者向けのメモ。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/30/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 353ab6e58e0c8f563a75d2bf7fa48d48a5d1dfc2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036874"
---
# <a name="azure-active-directory-b2c-tls-and-cipher-suite-requirements"></a>Azure Active Directory B2C TLS と暗号スイートの要件

Azure Active Directory B2C (Azure AD B2C) は、[ユーザー フロー](user-flow-overview.md)内の [API コネクタ](api-connectors-overview.md)と [ID プロバイダー](oauth2-technical-profile.md)を介してエンドポイントに接続します。 この記事では、エンドポイントの TLS と暗号スイートの要件について説明します。

API コネクタと ID プロバイダーで構成されたエンドポイントは、パブリックにアクセス可能な HTTPS URI に公開する必要があります。 エンドポイントとのセキュリティで保護された接続が確立される前に、Azure AD B2C とエンドポイントの間で、接続の両側の機能に基づいて、プロトコルと暗号がネゴシエートされます。

この記事で説明されているように、Azure AD B2C は、トランスポート層セキュリティ (TLS) と暗号スイートを使用してエンドポイントに接続できる必要があります。

## <a name="tls-versions"></a>TLS バージョン

TLS バージョン 1.2 は、サーバーとクライアント間の認証とデータ暗号化を提供する暗号化プロトコルです。 エンドポイントは、**TLS バージョン 1.2** を介してセキュリティで保護された通信をサポートする必要があります。 以前の TLS バージョン 1.0 および 1.1 は非推奨です。 

## <a name="cipher-suites"></a>暗号スイート

暗号スイートは、暗号化アルゴリズムのセットです。 これらは TLS 経由で HTTPS プロトコルを使用する場合のデータの安全な通信方法に関する重要な情報を提供します。

エンドポイントは、次のいずれかの暗号を少なくとも 1 つサポートする必要があります。

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

## <a name="endpoints-in-scope"></a>スコープ内のエンドポイント

Azure AD B2C 環境で使用される次のエンドポイントは、この記事で説明されている要件に準拠している必要があります。

- [API コネクタ](api-connectors-overview.md) 
- OAuth1
    - Token endpoint (トークン エンドポイント) 
    - ユーザー情報エンドポイント
- OAuth2 と OpenID Connect ID プロバイダー
    - OpenID Connect 検出エンドポイント
    - OpenId Connect JWKS エンドポイント
    - Token endpoint (トークン エンドポイント) 
    - ユーザー情報エンドポイント
- [ID トークン ヒント](id-token-hint.md)
    - OpenID Connect 検出エンドポイント
    - OpenId Connect JWKS エンドポイント
- [SAML ID プロバイダー](saml-service-provider.md)のメタデータ エンドポイント
- [SAML サービス プロバイダー](identity-provider-generic-saml.md)のメタデータ エンドポイント

## <a name="check-your-endpoint-compatibility"></a>エンドポイントの互換性を確認する

エンドポイントがこの記事で説明されている要件に準拠していることを確認するには、TLS 暗号とスキャナー ツールを使用してテストを実行します。 [SSLLABS](https://www.ssllabs.com/ssltest/analyze.html) を使用してエンドポイントをテストします。


## <a name="next-steps"></a>次のステップ

次の記事もご覧ください。

- [TLS 1.2 をサポートしていないアプリケーションのトラブルシューティング](../cloud-services/applications-dont-support-tls-1-2.md)
- [TLS/SSL (Schannel SSP) の暗号スイート](/windows/win32/secauthn/cipher-suites-in-schannel)
- [TLS 1.2 を有効にする方法](/mem/configmgr/core/plan-design/security/enable-tls-1-2)
- [TLS 1.0 の問題の解決](/security/engineering/solving-tls1-problem)