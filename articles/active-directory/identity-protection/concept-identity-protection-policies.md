---
title: Azure AD Identity Protection ポリシー
description: Identity Protection で有効になっている 3 つのポリシーの識別
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
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886842"
---
# <a name="identity-protection-policies"></a>Identity Protection ポリシー

Azure Active Directory Identity Protection には、管理者が有効にすることを選択できる 3 つの既定のポリシーが含まれています。 これらのポリシーには限定されたカスタマイズが含まれていますが、ほとんどの組織に適用できます。 すべてのポリシーで、[緊急時の管理者アカウント](../users-groups-roles/directory-emergency-access.md)などのユーザーを除外できます。

![Identity Protection ポリシー](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 登録ポリシー

Identity Protection を使用すると、組織は、サインイン時に登録を要求する条件付きアクセス ポリシーを使用して Azure Multi-Factor Authentication (MFA) をロールアウトできます。 このポリシーの有効化は、組織内の新しいユーザーに初日に MFA に確実に登録させるための優れた方法です。 多要素認証は、Identity Protection 内のリスク イベントの自己修復方法の 1 つです。 自己修復機能を使用すると、ユーザーは自分でアクションを実行でき、ヘルプデスクへの問い合わせを減らすことができます。

この記事で説明した Azure Multi-Factor Authentication の詳細については、「[しくみ: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)」を参照してください。

## <a name="sign-in-risk-policy"></a>サインインのリスク ポリシー

Identity Protection では、リアルタイムとオフラインの両方で各サインインからのシグナルを分析し、ユーザーによるサインインが行われなかった確率に基づいてリスク スコアが計算されます。 管理者は、このリスク スコア シグナルに基づいて決定を行い、組織の要件を強制することができます。 管理者は、アクセスをブロックする、アクセスを許可する、またはアクセスを許可するが多要素認証を要求することを選択できます。

リスクが検出された場合、ユーザーは、多要素認証を実行して自己修復し、危険なサインイン イベントを閉じて、管理者に対する不要なノイズが発生しないようにすることができます。

> [!NOTE] 
> サインイン リスク ポリシーをトリガーする前に、ユーザーが Azure Multi-Factor Authentication に登録済みである必要があります。

### <a name="custom-conditional-access-policy"></a>カスタム条件付きアクセス ポリシー

管理者は、割り当て条件としてサインイン リスクが含まれるカスタム条件付きアクセス ポリシーを作成することもできます。 条件付きアクセスの詳細については、「[条件付きアクセスとは](../conditional-access/overview.md)」を参照してください。

![カスタム条件付きアクセスのサインイン リスク ポリシー](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>ユーザー リスクのポリシー

Identity Protection では、ユーザーの標準的な行動であると確信できるものは何かを判断し、それを使用してユーザーのリスクに関する意思決定を行うことができます。 ユーザー リスクとは、ID が侵害されている確率の計算です。 管理者は、このリスク スコア シグナルに基づいて決定を行い、組織の要件を強制することができます。 管理者は、アクセスをブロックする、アクセスを許可する、またはアクセスを許可するが [Azure AD のパスワード リセットのセルフサービス](../authentication/howto-sspr-deployment.md)を使用してパスワードを変更する必要があることを選択できます。

リスクが検出された場合、ユーザーは、パスワード リセットのセルフサービスを実行して自己修復し、ユーザーのリスク イベントを閉じて、管理者に対する不要なノイズが発生しないようにすることができます。

> [!NOTE] 
> ユーザー リスク ポリシーをトリガーする前に、ユーザーがパスワード リセットのセルフサービスに登録済みである必要があります。

## <a name="next-steps"></a>次の手順

- [Azure AD のパスワード リセットのセルフサービスを有効にする](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication を有効にする](../authentication/howto-mfa-getstarted.md)

- [Azure Multi-Factor Authentication 登録ポリシーを有効にする](howto-identity-protection-configure-mfa-policy.md)

- [サインインとユーザー リスク ポリシーを有効にする](howto-identity-protection-configure-risk-policies.md)
