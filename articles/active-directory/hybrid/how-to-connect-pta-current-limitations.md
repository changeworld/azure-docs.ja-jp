---
title: 'Azure AD Connect: パススルー認証 - 現在の制限事項 | Microsoft Docs'
description: この記事では、Azure Active Directory (Azure AD) パススルー認証の現在の制限事項について説明します
services: active-directory
keywords: Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dc639c5dd2098871e05e6ce39bfac3c529d2694
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063273"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory パススルー認証: 現在の制限事項

## <a name="supported-scenarios"></a>サポートされるシナリオ

次のシナリオがサポートされます。

- Web ブラウザー ベースのアプリケーションへのユーザー サインイン。
- Exchange ActiveSync、EAS、SMTP、POP、IMAP などの従来のプロトコルを使用した Outlook クライアントへのユーザー サインイン。
- [先進認証](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview)をサポートする旧 Office クライアント アプリケーションと Office アプリケーションへのユーザー サインイン: Office 2013 および 2016 バージョン。
- PowerShell バージョン 1.0 など、旧プロトコル アプリケーションへのユーザー サインイン。
- Azure AD の Windows 10 デバイスへの参加。
- Multi-Factor Authentication のアプリ パスワード。

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

次のシナリオはサポートされて _いません_。

- [資格情報が漏洩した](../identity-protection/overview-identity-protection.md)ユーザーの検出。
- Azure AD Domain Services を使用するには、パスワード ハッシュの同期をテナントで有効にする必要があります。 そのため、パススルー認証 _のみ_ を使用するテナントは、Azure AD Domain Services を必要とするシナリオに対応していません。
- パススルー認証は [Azure AD Connect Health](./whatis-azure-ad-connect.md) にはTS統合されていません。
- 一時的または期限切れのパスワードを使用して Azure AD 参加済み (AADJ) デバイスにサインインすることは、パススルー認証ユーザーではサポートされていません。 「使用しようとしているサインイン方法は許可されていません」というエラーが表示されます。  これらのユーザーは、ブラウザーにサインインして、自分の一時パスワードを更新する必要があります。

> [!IMPORTANT]
> サポートされていないシナリオ _のみ_ の対処法として (Azure AD Connect Health 統合を除く)、Azure AD Connect ウィザードの [[オプション機能]](how-to-connect-install-custom.md#optional-features) ページでパスワード ハッシュ同期を有効にします。
> 
> [!NOTE]
> パスワード ハッシュ同期を有効にすると、オンプレミスのインフラストラクチャが中断された場合に認証をフェールオーバーするオプションが提供されます。 パススルー認証からパスワード ハッシュ同期へのこのフェールオーバーは自動的には行われません。 Azure AD Connect を使用して手動でサインイン方法を切り替える必要があります。 Azure AD Connect を実行しているサーバーで障害が発生した場合、パススルー認証をオフにするには、Microsoft サポートに対処方法をお問い合わせください。

## <a name="next-steps"></a>次のステップ
- [クイック スタート](how-to-connect-pta-quick-start.md): Azure AD パススルー認証を起動および実行します。
- [AD FS からパススルー認証への移行](https://aka.ms/ADFSTOPTADPDownload) - AD FS (または他のフェデレーション テクノロジ) からパススルー認証に移行するための詳細なガイドです。
- [スマート ロックアウト](../authentication/howto-password-smart-lockout.md): ユーザー アカウントを保護するようにご利用のテナント上でスマート ロックアウト機能を構成する方法について説明します。
- [技術的な詳細](how-to-connect-pta-how-it-works.md): パススルー認証機能のしくみについて説明します。
- [よく寄せられる質問](how-to-connect-pta-faq.yml): パススルー認証機能に関してよく寄せられる質問への回答を見つけます。
- [トラブルシューティング](tshoot-connect-pass-through-authentication.md): パススルー認証機能に関する一般的な問題を解決する方法について説明します。
- [セキュリティの詳細](how-to-connect-pta-security-deep-dive.md): パススルー認証機能に関する詳細な技術情報を取得します。
- [Azure AD シームレス SSO](how-to-connect-sso.md): この補完的な機能の詳細について説明します。
- [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789): Azure Active Directory フォーラムを使用して、新しい機能の要求を行います。
