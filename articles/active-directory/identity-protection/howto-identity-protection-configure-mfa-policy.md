---
title: MFA 登録ポリシーを構成する - Azure Active Directory Identity Protection
description: Azure AD Identity Protection 多要素認証登録ポリシーを構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382139"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>方法:Azure Multi-Factor Authentication 登録ポリシーを構成する

Azure AD Identity Protection を使用すると、多要素認証 (MFA) 登録を要求する条件付きアクセス ポリシーを構成することによって、サインインする先進認証アプリに関係なく、MFA 登録の展開を管理できます。

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Azure Multi-Factor Authentication 登録ポリシーとは

Azure Multi-Factor Authentication は、ユーザー名とパスワードに加えて、その他の要素を使用することでユーザーを確認する手段を提供します。 ユーザーのサインインに第 2 のセキュリティ層を提供します。ユーザーが MFA プロンプトに応答できるようにするには、まず Azure Multi-Factor Authentication に登録する必要があります。

次のような理由から、ユーザーのサインインに対して Azure Multi-Factor Authentication を要求することをお勧めします。

- 幅広い検証オプションを通して強力な認証が提供されます。
- 組織が Identity Protection でのリスク検出から自己修復するための準備において重要な役割を果たします。

Azure Multi-Factor Authentication の詳細については、[Azure Multi-Factor Authentication の概要](../authentication/howto-mfa-getstarted.md)に関するページをご覧ください。

## <a name="policy-configuration"></a>ポリシーの構成

1. [Azure Portal](https://portal.azure.com) に移動します。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[Identity Protection]**  >  **[MFA 登録ポリシー]** を参照します。
   1. **[割り当て]** で:
      1. **[ユーザー]** - **[すべてのユーザー]** を選択します。ロールアウトを制限する場合は **[個人と グループの選択]** を選択します。
         1. 必要に応じて、ポリシーからユーザーを除外できます。
   1. **[コントロール]** で:
      1. **[Azure MFA への登録を必須とする]** チェックボックスがオンであることを確認し、 **[選択]** を選択します。
   1. **[ポリシーの適用]**  -  **[オン]**
   1. **および**

## <a name="user-experience"></a>ユーザー エクスペリエンス

Azure Active Directory Identity Protection によって、次回対話形式でサインインするときに、ユーザーに登録を求めるプロンプトが表示されます。ユーザーは、14 日の間に登録を完了します。 この 14 日間は登録をパイバスできますが、この期間の最後に、サインイン プロセスを完了するには登録することが要求されされます。

関連するユーザー エクスペリエンスの概要については、以下を参照してください。

- [Azure AD Identity Protection を使用したサインイン エクスペリエンス](concept-identity-protection-user-experience.md)。  

## <a name="next-steps"></a>次のステップ

- [サインインとユーザー リスク ポリシーを有効にする](howto-identity-protection-configure-risk-policies.md)

- [Azure AD のセルフサービス パスワード リセット を有効にする](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication を有効にする](../authentication/howto-mfa-getstarted.md)
