---
title: "Azure Active Directory 認証プロトコル | Microsoft Docs"
description: "Azure Active Directory (AD) でサポートされる認証プロトコルの概要"
documentationcenter: dev-center-name
author: bryanla
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 51c6a8ae17821d4eb00bf26612bc6e1e434ebbd9
ms.lasthandoff: 04/18/2017


---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory の認証プロトコル
Azure Active Directory (Azure AD) では、最も広く使用されている認証および承認のプロトコルを複数サポートしています。 このセクションの各トピックでは、Azure AD でサポートされているプロトコルとその実装について説明します。 これらのトピックには、サポートされている要求の種類の確認、フェデレーション メタデータの使用の概要、OAuth 2.0  および SAML 2.0 プロトコルの詳細なリファレンス ドキュメント、トラブルシューティング セクションが含まれます。

## <a name="authentication-protocols-articles-and-reference"></a>認証プロトコルの記事とリファレンス
* [Azure AD での署名キーのロールオーバーに関する重要な情報](active-directory-signing-key-rollover.md) - Azure AD の署名キーのロールオーバーの周期、キーを自動的に更新するために可能な変更、最も一般的なアプリケーション シナリオを更新する方法について説明します。
* [サポートされているトークンと要求の種類](active-directory-token-and-claims.md) - Azure AD が発行するトークン内の要求について説明します。
* [フェデレーション メタデータ](active-directory-federation-metadata.md) - Azure AD が生成するメタデータ ドキュメントの検索と解釈の方法について説明します。
* [Azure AD での OAuth 2.0](active-directory-protocols-oauth-code.md) - Azure AD での OAuth 2.0 の実装について説明します。
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) - 認証に OAuth 2.0 (承認プロトコル) を使用する方法について説明します。
* [サービス間の呼び出し](active-directory-protocols-oauth-service-to-service.md) - サービス間の呼び出しのための OAuth 2.0 クライアント資格情報許可フローを使用する方法について説明します。
* [SAML プロトコル リファレンス](active-directory-saml-protocol-reference.md) - Azure AD のシングル サインオンおよびシングル サインアウト SAML プロファイルについて説明します。

## <a name="see-also"></a>関連項目
[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)

[認証に Azure AD を使用する](../../app-service-web/web-sites-authentication-authorization.md)

[Active Directory のコード例](active-directory-code-samples.md)


