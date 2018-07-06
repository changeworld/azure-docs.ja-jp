---
title: 'Azure AD Connect: パススルー認証 - しくみ | Microsoft Docs'
description: この記事では、Azure Active Directory のパススルー認証のしくみについて説明します
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
ms.date: 01/24/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5fde0ea00aacbb791836fc1076b88dafd3728454
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063478"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory パススルー認証: 技術的な詳細
この記事は、Azure Active Directory (Azure AD) パススルー認証のしくみの概要です。 技術とセキュリティの詳細情報については、[セキュリティの詳細](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)に関する記事をご覧ください。

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory パススルー認証のしくみ

ユーザーが Azure AD で保護されているアプリケーションにサインインしようとし、テナントでパススルー認証が有効になっている場合、次の手順が発生します。

1. ユーザーが [Outlook Web アプリ](https://outlook.office365.com/owa/)などのアプリケーションへのアクセスを試みます。
2. まだサインインしていない場合、ユーザーは Azure AD の**ユーザー サインイン** ページにリダイレクトされます。
3. ユーザーが Azure AD サインイン ページにユーザー名を入力し、**[次へ]** ボタンを選択します。
4. ユーザーが Azure AD サインイン ページにパスワードを入力し、**[サインイン]** ボタンを選択します。
5. サインインの要求を受け取った Azure AD が、(認証エージェントの公開キーを使用して暗号化された) ユーザー名とパスワードをキューに入れます。
6. オンプレミス認証エージェントが、ユーザー名と暗号化されたパスワードをキューから取得します。 エージェントはキューの要求のために頻繁にポーリングしませんが、事前に確立された永続的な接続を介して要求を取得します。
7. エージェントがその秘密キーを使用してパスワードの暗号化を解除します。
8. エージェントは、標準の Windows API を使用して Active Directory に対してユーザー名とパスワードを検証しますが、これは Active Directory フェデレーション サービス (AD FS) が使用しているのと同様のメカニズムです。 ユーザー名には、オンプレミスの既定のユーザー名 (通常は `userPrincipalName`) か、Azure AD Connect (`Alternate ID` とも呼ばれる) で構成された別の属性を指定できます。
9. オンプレミスの Active Directory ドメイン コントローラー (DC) が要求を評価し、適切な応答をエージェントに返します (成功、失敗、パスワードの期限切れ、またはユーザーがロックアウト)。
10. 次に、認証エージェントがこの応答を Azure AD に返します。
11. Azure AD が応答を評価し、ユーザーに適宜応答します。 たとえば、Azure AD はすぐにユーザーをサインインさせるか、または Azure Multi-Factor Authentication を要求します。
12. ユーザーはサインインが成功すると、アプリケーションにアクセスできます。

次の図に、すべてのコンポーネントと必要な手順を示します。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>次の手順
- [現在の制限](active-directory-aadconnect-pass-through-authentication-current-limitations.md): サポートされているシナリオと、サポートされていないシナリオを確認します。
- [クイック スタート](active-directory-aadconnect-pass-through-authentication-quick-start.md): Azure AD パススルー認証を起動および実行します。
- [スマート ロックアウト](../authentication/howto-password-smart-lockout.md): ユーザー アカウントを保護するようにテナントのスマート ロックアウト機能を構成します。
- [よく寄せられる質問](active-directory-aadconnect-pass-through-authentication-faq.md): よく寄せられる質問とその回答です。
- [トラブルシューティング](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): パススルー認証機能に関する一般的な問題を解決する方法を確認します。
- [セキュリティの詳細](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): パススルー認証機能に関する詳細な技術情報を取得します。
- [Azure AD シームレス SSO](active-directory-aadconnect-sso.md): この補完的な機能の詳細を確認します。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory フォーラムで、新しい機能の要望を出します。

