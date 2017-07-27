---
title: "Azure AD Connect: パススルー認証 - 現在の制限事項 | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) パススルー認証の現在の制限事項について説明します。"
services: active-directory
keywords: "Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 3ed2ee8c996628caabefd60b5ecff7528402eb52
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017

---

# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory パススルー認証: 現在の制限事項

>[!IMPORTANT]
>Azure AD パススルー認証は現在プレビュー段階です。 これは無料の機能であり、この機能を使用するために Azure AD の有料エディションは不要です。 パススルー認証は、世界中の Azure AD のインスタンスでのみ使用できます。[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) と [Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/)では使用できません。

## <a name="supported-scenarios"></a>サポートされるシナリオ

プレビュー期間中、次のシナリオが完全にサポートされています。

- すべての Web ブラウザー ベースのアプリケーションへのユーザー サインイン。
- [最新の認証](https://aka.ms/modernauthga)をサポートする Office 365 クライアント アプリケーションへのユーザー サインイン。

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

プレビュー期間中、次のシナリオはサポート_されていません_。

- 従来の Office クライアント アプリケーションと Exchange ActiveSync (モバイル デバイス上のネイティブ電子メール アプリケーション) へのユーザー サインイン。 組織は、可能であれば最新の認証に切り替えることが推奨されます。 最新の認証により、パススルー認証のサポートが可能になるだけでなく、Multi-Factor Authentication (MFA) などの[条件付きアクセス](../active-directory-conditional-access.md)機能を使用して ID をセキュリティで保護することもできます。
- Skype for Business クライアント アプリケーションへのユーザー サインイン。
- PowerShell v1.0 へのユーザー サインイン。 代わりに、PowerShell v2.0 を使用することをお勧めします。
- Windows 10 デバイスの Azure AD Join。

>[!IMPORTANT]
>サポートされていないシナリオに対処するために、Azure AD Connect ウィザードの [[オプション機能]](active-directory-aadconnect-get-started-custom.md#optional-features) ページでパスワード ハッシュ同期を有効にします。 パスワード ハッシュ同期は、前に示したシナリオのフォールバックとして "_のみ_" 機能します (パススルー認証への汎用フォールバックとしては機能 "_しません_")。 こうしたシナリオを必要としない場合は、パスワード ハッシュ同期をオフにします。

## <a name="next-steps"></a>次のステップ
- [**クイック スタート**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Azure AD パススルー認証を起動および実行します。
- [**技術的な詳細**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - この機能のしくみを確認します。
- [**よく寄せられる質問**](active-directory-aadconnect-pass-through-authentication-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**Azure AD のシームレスな SSO**](active-directory-aadconnect-sso.md) - この補完的な機能の詳細を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

