---
title: NIST Authenticator の種類と連携された Azure Active Directory の方法
description: Azure Active Directory の認証方法を NIST Authenticator の種類と連携させる方法についての説明。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ee6cc0c913533b83be1eb389e7d7402875e4dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012418"
---
# <a name="nist-authenticator-types-and-aligned-azure-active-directory-methods"></a>NIST Authenticator の種類と連携された Azure Active Directory の方法

請求者がサブスクライバーに関連付けられている複数の Authenticator のいずれかのコントロールをアサートすると、認証プロセスが開始されます。 サブスクライバーは、個人または別のエンティティである場合があります。

| National Institute of Standards and Technology (NIST) Authenticator の種類| Azure Active Directory (Azure AD) 認証方法 |
| - | - |
|  記憶シークレット <br> (自分が知っているもの)|  パスワード (クラウド アカウント)  <br>パスワード (フェデレーション)<br> パスワード (パスワード ハッシュ同期)<br>パスワード (パススルー認証) |
|ルックアップ シークレット <br> (自分が持っているもの)| なし。 ルックアップ シークレットは、システムに保持されていない定義データによるものです。 |
|帯域外 <br>(自分が持っているもの)| 電話 (SMS) - 推奨されません |
| 単一要素ワンタイム パスワード <br>‎(自分が持っているもの)| Microsoft Authenticator アプリ (ワンタイム パスワード)  <br>単一要素ワンタイム パスワード ‎(OTP 製造元経由)<sup data-htmlnode="">1</sup> | 
| 多要素ワンタイム パスワード<br>(自分が持っているもの + 自分が知っているものまたは自分自身)| 多要素ワンタイム パスワード ‎(OTP 製造元経由) <sup data-htmlnode="">1</sup>| 
|単一要素暗号化ソフトウェア<br>(自分が持っているもの)|準拠モバイル デバイス <br> Microsoft Authenticator アプリ (通知) <br> Hybrid Azure AD 参加済み<sup data-htmlnode="">2</sup> (ソフトウェア TPM を使用)<br> Azure AD 参加済み <sup data-htmlnode="">2</sup> (ソフトウェア TPM を使用) |
| 単一要素暗号化ハードウェア <br>(自分が持っているもの) | Azure AD 参加済み <sup data-htmlnode="">2</sup> (ハードウェア TPM を使用) <br> Hybrid Azure AD 参加済み <sup data-htmlnode="">2</sup> (ハードウェア TPM を使用)|
|多要素の暗号化ソフトウェア<br>(自分が持っているもの + 自分が知っているものまたは自分自身) | iOS 用の Microsoft Authenticator アプリ (パスワードレス)<br> ソフトウェア TPM と Windows Hello for Business |
|多要素の暗号化ハードウェア <br>(自分が持っているもの + 自分が知っているものまたは自分自身) |Android 用の Microsoft Authenticator アプリ (パスワードレス)<br> Windows Hello for Business (ハードウェア TPM を使用)<br> スマートカード (フェデレーション ID プロバイダー) <br> FIDO 2 セキュリティ キー |


<sup data-htmlnode="">1</sup> 30 秒または 60 秒の種類がある OATH-TOTP SHA-1 トークン。

<sup data-htmlnode="">2</sup> デバイスの参加状態の詳細については、[Azure AD デバイス ID のドキュメント](../devices/index.yml)を参照してください。 

## <a name="why-sms-isnt-recommended"></a>SMS が推奨されない理由 

SMS テキスト メッセージは NIST 標準を満たしていますが、NIST では推奨されません。 デバイス スワップ、SIM の変更、電話番号のポーティング、およびその他の動作のリスクによって、問題が発生する可能性があります。 これらのアクションが悪意を持って行われると、安全でないエクスペリエンスになる可能性があります。 SMS テキスト メッセージは推奨されていませんが、ハッカーにとってより多くの労力が必要になるため、パスワードのみを使用するよりは悪くありません。 

## <a name="next-steps"></a>次のステップ 

[NIST の概要](nist-overview.md)

[AAL について確認する](nist-about-authenticator-assurance-levels.md)

[認証の基本](nist-authentication-basics.md)

[NIST 認証システムの種類](nist-authenticator-types.md)

[Azure AD を使用して NIST AAL1 を達成する](nist-authenticator-assurance-level-1.md)

[Azure AD を使用して NIST AAL2 を達成する](nist-authenticator-assurance-level-2.md)

[Azure AD を使用して NIST AAL3 を達成する](nist-authenticator-assurance-level-3.md)
