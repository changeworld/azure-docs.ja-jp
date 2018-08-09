---
title: 'Azure AD Connect: パススルー認証 - 現在の制限事項 | Microsoft Docs'
description: この記事では、Azure Active Directory (Azure AD) パススルー認証の現在の制限事項について説明します
services: active-directory
keywords: Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a112e2f201109b71b7bab1c2b344ec4fcf2a851c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627646"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory パススルー認証: 現在の制限事項

>[!IMPORTANT]
>Azure Active Directory (Azure AD) のパススルー認証は無料の機能で、使用するために Azure AD の有料エディションは必要ありません。 パススルー認証は、Azure AD のワールドワイド インスタンスでのみ使用できます。[Microsoft Azure Germany クラウド](http://www.microsoft.de/cloud-deutschland)や [Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/)では使用できません。

## <a name="supported-scenarios"></a>サポートされるシナリオ

次のシナリオがサポートされています。

- Web ブラウザー ベースのアプリケーションへのユーザー サインイン。
- Exchange ActiveSync、EAS、SMTP、POP、IMAP などの従来のプロトコルを使用した Outlook クライアントへのユーザー サインイン。
- [先進認証](https://aka.ms/modernauthga)をサポートする旧 Office クライアント アプリケーションと Office アプリケーションへのユーザー サインイン: Office 2010、2013、2016 バージョン。
- PowerShell バージョン 1.0 など、旧プロトコル アプリケーションへのユーザー サインイン。
- Azure AD の Windows 10 デバイスへの参加。
- Multi-Factor Authentication のアプリ パスワード。

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

次のシナリオはサポートされて_いません_。

- [資格情報が漏洩した](../reports-monitoring/concept-risk-events.md#leaked-credentials)ユーザーの検出。
- Azure AD Domain Services を使用するには、パスワード ハッシュの同期をテナントで有効にする必要があります。 そのため、パススルー認証_のみ_を使用するテナントは、Azure AD Domain Services を必要とするシナリオに対応していません。
- パススルー認証は [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) にはTS統合されていません。

>[!IMPORTANT]
>サポートされていないシナリオ_のみ_の対処法として (Azure AD Connect Health 統合を除く)、Azure AD Connect ウィザードの [[オプション機能]](active-directory-aadconnect-get-started-custom.md#optional-features) ページでパスワード ハッシュ同期を有効にします。 ユーザーが "サポートされていないシナリオ" セクションにリストされているアプリケーションにサインインすると、それぞれのサインイン要求はパススルー認証エージェントで処理され_ない_ため、[パススルー認証ログ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)に記録されません。

>[!NOTE]
パスワード ハッシュ同期を有効にすると、オンプレミスのインフラストラクチャが中断された場合に認証をフェールオーバーするオプションが提供されます。 パススルー認証からパスワード ハッシュ同期へのこのフェールオーバーは自動的には行われません。 Azure AD Connect を使用して手動でサインイン方法を切り替える必要があります。 Azure AD Connect を実行しているサーバーで障害が発生した場合、パススルー認証をオフにするには、Microsoft サポートに対処方法をお問い合わせください。

## <a name="next-steps"></a>次の手順
- [クイック スタート](active-directory-aadconnect-pass-through-authentication-quick-start.md): Azure AD パススルー認証を起動および実行します。
- [AD FS からパススルー認証への移行](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) - AD FS (または他のフェデレーション テクノロジ) からパススルー認証に移行するための詳細なガイドです。
- [スマート ロックアウト](../authentication/howto-password-smart-lockout.md): ユーザー アカウントを保護するようにテナントのスマート ロックアウト機能を構成する方法を確認します。
- [技術的な詳細](active-directory-aadconnect-pass-through-authentication-how-it-works.md): パススルー認証機能のしくみを理解します。
- [よく寄せられる質問](active-directory-aadconnect-pass-through-authentication-faq.md): パススルー認証機能に関してよく寄せられる質問への回答を見つけます。
- [トラブルシューティング](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): パススルー認証機能に関する一般的な問題を解決する方法を確認します。
- [セキュリティの詳細](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): パススルー認証機能に関する詳細な技術情報を取得します。
- [Azure AD シームレス SSO](active-directory-aadconnect-sso.md): この補完的な機能の詳細を確認します。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory フォーラムで、新しい機能の要望を出します。
