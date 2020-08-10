---
title: Azure AD B2C の ISV パートナー ギャラリー
titleSuffix: Azure AD B2C
description: ISV パートナーと統合して、エンドユーザー エクスペリエンスをニーズに合わせて調整する方法について説明します。 Microsoft のパートナー ネットワークは、ソリューションの機能を拡張し、MFA、セキュリティで保護されたカスタマー認証、ロールベースのアクセス制御を有効にし、ID の検証と証明を通じて不正行為を防止します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 986abbe6c124ce1323add5b8e854973416dfcbf4
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371211"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C の ISV パートナー

ISV パートナー ネットワークは、Microsoft のソリューションの機能を拡張し、シームレスなエンドユーザー エクスペリエンスを構築するのに役立ちます。 Azure AD B2C を使用することにより、ISV パートナーのサービスと統合し、多要素認証方式の有効化、セキュリティで保護されたカスタマー認証 (SCA) の有効化、ロールベースのアクセス制御の実施、本人確認と証明を通じた不正行為の防止を実現できます。 詳細なサンプルのチュートリアルを使用して、以下に一覧表示されている ISV パートナーのサービスとアプリを統合する方法を学習してください。

>[!NOTE]
>[GitHub の Azure Active Directory B2C コミュニティ サイト](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)にも、コミュニティからのサンプル カスタム ポリシーが提供されています。

## <a name="integration-isv-partners"></a>統合のための ISV パートナー

| ISV パートナー | 説明と統合のチュートリアル  |
| :--- | :--- |
| ![ロゴ](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) は、組織がボット攻撃、アカウント乗っ取り攻撃、および不正なアカウント開設を防ぐのに役立つ不正行為防止ソリューション プロバイダーです。
| ![ロゴ](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) は、詐欺を防ぐためにユーザー属性に基づいてリスク評価を行う、本人確認および証明プロバイダーです。|
| ![ロゴ](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) は、ID 検証ソリューション、不正行為防止ソリューション、コンプライアンス ソリューションなどを備えた ID の検証および証明プロバイダーです。|
| ![ロゴ](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) は、ユーザーがカード リーダー、パスワード、2 要素認証、および複数の PIN コードを使用せずに安全にサインインできる、eiDAS (Electronic Identification, Authentication and Trust Services) に準拠したデジタル ID ソリューションです。 |
| ![ロゴ](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) は、ユーザー ID を検証し、ユーザーのデバイスに基づいて包括的なリスク評価を提供する、プロファイルと ID 検証のプロバイダーです。 |
| ![ロゴ](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 統合を使用すると、安全にサインインし、パスワードレス認証、多要素認証、およびデジタル ライセンス スキャンを有効にすることができます。|
| ![ロゴ](./media/partner-gallery/twilio-logo.png) | [Twilio Verify App](./partner-twilio.md) は、SMS ワンタイム パスワード (OTP)、時間ベースのワンタイム パスワード (TOTP)、およびプッシュ通知を使用して多要素認証 (MFA) を有効にし、PSD2 (Payment Services Directive 2) の SCA 要件に準拠するための複数のソリューションを提供しています。|
| ![ロゴ](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) は、ユーザー入力パターンに基づく ID 検証および証明プロバイダーであり、多要素認証を強制する ID 検証ソリューションを提供し、PSD2 (Payment Services Directive 2) の SCA 要件に準拠するのに役立ちます。 |

## <a name="next-steps"></a>次のステップ

上の表のパートナーを選択して、Azure AD B2C とソリューション統合する方法を確認してください。

追加情報:

- [GitHub の B2C コミュニティ サイト](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)
- [Azure AD B2C のカスタム ポリシー](custom-policy-overview.md)
