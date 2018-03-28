---
title: 'Azure AD Connect: パススルー認証 - 現在の制限事項 | Microsoft Docs'
description: この記事では、Azure Active Directory (Azure AD) パススルー認証の現在の制限事項について説明します
services: active-directory
keywords: Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 3e533b8b23c095a3de845d9b26a96aea9d8ee086
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory パススルー認証: 現在の制限事項

>[!IMPORTANT]
>Azure Active Directory (Azure AD) のパススルー認証は無料の機能で、使用するために Azure AD の有料エディションは必要ありません。 パススルー認証は、Azure AD のワールドワイド インスタンスでのみ使用できます。[Microsoft Azure Germany クラウド](http://www.microsoft.de/cloud-deutschland)や [Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/)では使用できません。

## <a name="supported-scenarios"></a>サポートされるシナリオ

次のシナリオは完全にサポートされています。

- すべての Web ブラウザー ベースのアプリケーションへのユーザー サインイン
- [先進認証](https://aka.ms/modernauthga)をサポートする Office アプリケーションへのユーザー サインイン: Office 2016、および Office 2013 (先進認証_あり_)。
- Exchange ActiveSync、SMTP、POP、IMAP などの従来のプロトコルを使用した Outlook クライアントへのユーザー サインイン。
- 先進認証 (オンライン トポロジとハイブリッド トポロジを含む) をサポートする Skype for Business へのユーザー サインイン。 サポートされているトポロジについて詳しくは、[こちら](https://technet.microsoft.com/library/mt803262.aspx)をご覧ください。
- Azure AD ドメインの Windows 10 デバイスへの参加。
- Multi-Factor Authentication のアプリ パスワード。

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

次のシナリオはサポートされて_いません_。

- Outlook を除く、従来の Office クライアント アプリケーションへのユーザー サインイン (前述の「**サポートされるシナリオ**」を参照してください): Office 2010、および Office 2013 (先進認証 _"なし"_)。 組織は、可能であれば最新の認証に切り替えることが推奨されます。 先進認証により、パススルー認証のサポートが可能になります。 Azure Multi-factor Authentication などの[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)機能を使用して、ユーザー アカウントをセキュリティで保護することもできます。
- Office 2010 上でのみ、Exchange ハイブリッド環境で予定表の共有と空き時間情報にアクセスします。
- Skype for Business クライアント アプリケーションへのユーザー サインイン (先進認証_なし_)。
- PowerShell バージョン 1.0 へのユーザー サインイン。 PowerShell バージョン 2.0 を使用することをお勧めします。
- [資格情報が漏洩した](../active-directory-reporting-risk-events.md#leaked-credentials)ユーザーの検出。
- Azure AD Domain Services を使用するには、パスワード ハッシュの同期をテナントで有効にする必要があります。 そのため、パススルー認証_のみ_を使用するテナントは、Azure AD Domain Services を必要とするシナリオに対応していません。
- パススルー認証は [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) にはTS統合されていません。
- iOS 設定アシスタントを使用している Apple Device Enrollment Program (Apple DEP) で、最新の認証がサポートされていません。 この場合、パススルー認証を使用して、管理対象ドメインの Apple DEP デバイスを Intune に登録できません。 代わりに、[ポータル サイト アプリ](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/)の使用を検討してください。

>[!IMPORTANT]
>サポートされていないシナリオに対処する場合に_のみ_、Azure AD Connect ウィザードの [[オプション機能]](active-directory-aadconnect-get-started-custom.md#optional-features) ページでパスワード ハッシュ同期を有効にします。

>[!NOTE]
パスワード ハッシュ同期を有効にすると、オンプレミスのインフラストラクチャが中断された場合に認証をフェールオーバーするオプションが提供されます。 このパススルー認証から Active Directory パスワード ハッシュ同期へのフェールオーバーは自動的には行われません。 Azure AD Connect を使用して手動でサインイン方法を切り替える必要があります。 Azure AD Connect を実行しているサーバーで障害が発生した場合、パススルー認証をオフにするには、Microsoft サポートに対処方法をお問い合わせください。

## <a name="next-steps"></a>次の手順
- [クイック スタート](active-directory-aadconnect-pass-through-authentication-quick-start.md): Azure AD パススルー認証を起動および実行します。
- [スマート ロックアウト](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): ユーザー アカウントを保護するようにテナントのスマート ロックアウト機能を構成する方法を確認します。
- [技術的な詳細](active-directory-aadconnect-pass-through-authentication-how-it-works.md): パススルー認証機能のしくみを理解します。
- [よく寄せられる質問](active-directory-aadconnect-pass-through-authentication-faq.md): パススルー認証機能に関してよく寄せられる質問への回答を見つけます。
- [トラブルシューティング](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): パススルー認証機能に関する一般的な問題を解決する方法を確認します。
- [セキュリティの詳細](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): パススルー認証機能に関する詳細な技術情報を取得します。
- [Azure AD シームレス SSO](active-directory-aadconnect-sso.md): この補完的な機能の詳細を確認します。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory フォーラムで、新しい機能の要望を出します。
