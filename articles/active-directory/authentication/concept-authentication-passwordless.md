---
title: Azure Active Directory へのパスワードレスのサインイン (プレビュー)
description: FIDO2 セキュリティ キーまたは Microsoft Authenticator アプリを使用した Azure Active Directory へのパスワードレスのサインイン オプションについて説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba579d6da8c759a4653b729f1a471efdedc2baa7
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505754"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory のパスワードレス認証オプション

多要素認証 (MFA) は、組織をセキュリティで保護する優れた方法ですが、多くの場合、ユーザーはパスワードを覚えておく必要があるのに加え、セキュリティ層が増えることに不満を感じます。 パスワードが削除され、ユーザーが持っているものとユーザー自身または知っているものに置き換えられるため、パスワードレスの認証はより便利な方法です。

|   | ユーザーが持っているもの | ユーザー自身またはユーザーが知っているもの |
| --- | --- | --- |
| パスワードレス | Windows 10 デバイス、電話、またはセキュリティ キー | 生体認証または PIN |

認証については、組織ごとにさまざまなニーズがあります。 Microsoft では、次の 3 つのパスワードレス認証オプションを提供しています。

- Windows Hello for Business
- Microsoft Authenticator アプリ
- FIDO2 セキュリティ キー

![認証: セキュリティと利便性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello for Business

Windows Hello for Business は、指定した Windows PC を所有しているインフォメーション ワーカーに最適です。 生体認証と PIN はユーザーの PC に直接関連付けられるため、所有者以外のユーザーからのアクセスは防止されます。 公開キー基盤 (PKI) 統合およびシングル サインオン (SSO) の組み込みサポートにより、Windows Hello for Business では、オンプレミスおよびクラウドの会社のリソースにシームレスにアクセスできる便利な方法を提供します。

Windows Hello for Business の[計画ガイド](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)を使用すると、Windows Hello for Business の展開の種類と、検討する必要がある選択肢を決定することができます。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator アプリ

従業員の電話をパスワードレスの認証方法として使用できるようにします。 パスワードに加え、便利な多要素認証オプションとして Microsoft Authenticator アプリを既に使用されているかもしれません。 Authenticator アプリをパスワードレスのオプションとして使用することもできます。

![Microsoft Authenticator アプリを使用して Microsoft Edge にサインインする](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator アプリは、あらゆる iOS や Android フォンを、強力なパスワードレスの資格情報に変えます。 ユーザーは、自分の電話で通知を受け取り、画面に表示される番号と電話の番号を照合してから、生体認証 (指紋または顔) あるいは PIN を使用して確認できます。

## <a name="fido2-security-keys"></a>FIDO2 セキュリティ キー

FIDO2 セキュリティ キーは、フォーム ファクターとして提供される可能性のある、フィッシングできない標準ベースのパスワードレス認証方法です。 Fast Identity Online (FIDO) は、パスワードレス認証のオープン標準です。 FIDO は、ユーザーおよび組織が標準を利用し、外部のセキュリティ キーまたはデバイスに組み込まれているプラットフォーム キーを使用して、ユーザー名やパスワードレスでリソースにサインインできるようにします。

パブリック プレビューでは、従業員はセキュリティ キーを使用して、Azure AD またはハイブリッド Azure AD に参加済みの Windows 10 デバイスにサインインし、クラウドおよびオンプレミス リソースへのシングル サインオンを行うことができます。 ユーザーは、サポートされているブラウザーにサインインすることもできます。 FIDO2 セキュリティ キーは、セキュリティに非常に敏感であるか、2 番目のファクターとしての電話の使用を望まない、あるいは使用できないシナリオまたは従業員が存在する企業向けの優れたオプションです。

![セキュリティ キーを使用して Microsoft Edge にサインインする](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

FIDO Alliance によって FIDO2 認定されたキーが多数存在する場合、Microsoft では、最大限のセキュリティと最適なエクスペリエンスを保証するために、ベンダーによって実装される FIDO2 Client-to-Authenticator Protocol (CTAP) 仕様の一部の省略可能な拡張機能が必要になります。

セキュリティ キーでは、Microsoft との互換性を維持するために、FIDO2 CTAP プロトコルの以下の機能と拡張機能を実装する**必要があります**。

| # | 機能 / 拡張機能 (信頼) | この機能または拡張機能が必要な理由 |
| --- | --- | --- |
| 1 | Resident Key | この機能により、セキュリティ キーが可搬となり、セキュリティキー内に資格情報が保存されます。 |
| 2 | Client pin | この機能では、2 番目の認証要素で資格情報を保護できます。これは、ユーザー インターフェイスがないセキュリティ キーに適用されます。 |
| 3 | hmac-secret | この拡張機能では、オフライン時または機内モード時に確実にデバイスにサインインできます。 |
| 4 | RP あたりの複数のアカウント | この機能を利用すれば、Microsoft アカウントや Azure Active Directory などの複数のサービスで同じセキュリティ キーを使用できるようになります。 |

以下のプロバイダーでは、パスワードレスのエクスペリエンスと互換性があると認識されている、さまざまなフォーム ファクターの FIDO2 セキュリティ キーが提供されます。 Microsoft では、お客様に対し、これらのキーのセキュリティ プロパティを評価するために、FIDO Alliance だけでなく、ベンダーにも連絡するようお勧めしています。

| プロバイダー | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> NFC ベースのセキュリティ キーを購入して使用する予定の場合は、セキュリティ キーとしてサポートされている NFC リーダーが必要になります。 NFC リーダーは、Azure の要件でも制限事項でもありません。 使用している NFC ベースのセキュリティ キーでサポートされている NFC リーダーの一覧については、ベンダーにご確認ください。

ベンダーとして、サポートされているデバイスのリストにデバイスを載せたい場合は、[Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) にご連絡ください。

## <a name="what-scenarios-work-with-the-preview"></a>プレビューで動作するシナリオ

- 管理者は、テナントに対してパスワードレスの認証方法を有効にすることができます
- 管理者は、すべてのユーザーをターゲットにすることも、方法ごとにテナント内のユーザー/グループを選ぶこともできます
- エンド ユーザーは、アカウント ポータルでこれらのパスワードレスの認証方法を登録して管理することができます
- エンド ユーザーは、これらのパスワードレスの認証方法でサインインすることができます
   - Microsoft Authenticator アプリ:すべてのブラウザー間で、Windows 10 の Out Of Box (OOBE) セットアップ時に、また、オペレーティング システム上の統合されたモバイル アプリでなど、Azure AD 認証が使用されるシナリオで動作します。
   - セキュリティ キー:Windows 10 のロック画面、および Microsoft Edge などのサポートされているブラウザーの Web で動作します。

## <a name="next-steps"></a>次のステップ

[組織内で FIDO2 セキュリティ キーのパスワードレスオプションを有効にする](howto-authentication-passwordless-security-key.md)

[組織内で電話ベースのパスワードレスオプションを有効にする](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部リンク

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP の仕様](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
