---
title: Microsoft ID プラットフォーム認証プロトコル
description: Microsoft ID プラットフォームでサポートされる認証プロトコルの概要
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884683"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Microsoft ID プラットフォーム認証プロトコル

Microsoft ID プラットフォームでは、最も広く使用されている認証および承認のプロトコルが複数サポートされています。 このセクションの各トピックでは、Microsoft ID プラットフォームでサポートされているプロトコルとその実装について説明します。 これらのトピックには、サポートされている要求の種類の確認、フェデレーション メタデータの使用の概要、OAuth 2.0 および SAML 2.0 プロトコルの詳細なリファレンス ドキュメント、トラブルシューティング セクションが含まれます。

## <a name="authentication-protocols-articles-and-reference"></a>認証プロトコルの記事とリファレンス

* [Microsoft ID プラットフォームでの署名キーのロールオーバーに関する重要な情報](active-directory-signing-key-rollover.md) - Microsoft ID プラットフォームの署名キーのロールオーバーの周期、キーを自動的に更新するために可能な変更、最も一般的なアプリケーション シナリオを更新する方法について説明します。
* [サポートされているトークンと要求の種類](id-tokens.md) - Microsoft ID プラットフォームによって発行されるトークン内の要求について説明します。
* [Microsoft ID プラットフォームでの OAuth 2.0](v2-oauth2-auth-code-flow.md) - Microsoft ID プラットフォームでの OAuth 2.0 の実装について説明します。
* [OpenID Connect 1.0](v2-protocols-oidc.md) - 認証に OAuth 2.0 (承認プロトコル) を使用する方法について説明します。
* [クライアント資格情報を使用したサービス間の呼び出し](v2-oauth2-client-creds-grant-flow.md) - サービス間の呼び出しに OAuth 2.0 クライアント資格情報許可フローを使用する方法について説明します。
* [On-Behalf-Of フローを使用したサービス間の呼び出し](v2-oauth2-on-behalf-of-flow.md) - サービス間の呼び出しに OAuth 2.0 On-Behalf-Of フローを使用する方法について説明します。
* [SAML プロトコル リファレンス](active-directory-saml-protocol-reference.md) - Microsoft ID プラットフォームのシングル サインオンおよびシングル サインアウト SAML プロファイルについて説明します。

## <a name="see-also"></a>関連項目

* [Microsoft ID プラットフォームの概要](v2-overview.md)
* [Active Directory のコード例](sample-v2-code.md)
