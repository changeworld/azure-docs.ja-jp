---
title: Azure MFA のアクセスおよび使用状況レポート | Microsoft Docs
description: ここでは、Azure Multi-Factor Authentication 機能のレポートを使用する方法について説明します。
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 3a29d1b17924b519167f362e6116f405e182bf75
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication のレポート

Azure Multi-Factor Authentication は、Azure Portal からアクセスでき、個人や組織が使用できるいくつかのレポートを提供します。 次の表は使用できるレポートの一覧です。

| レポート | 場所 | [説明] |
|:--- |:--- |:--- |
| ユーザーのブロックの履歴 | [Azure AD] > [MFA サーバー] > [ユーザーのブロック/ブロック解除] | ユーザーのブロックまたはブロック解除の要求履歴を表示します。 |
| 利用状況と不正アクセス アラート | [Azure AD] > [サインイン数] | 全体的な利用状況、ユーザーの概要、およびユーザーの詳細に関する情報を提供します。また、指定した日付範囲の間に送信された不正アクセス アラートの履歴も提供します。 |
| オンプレミス コンポーネントの利用状況 | [Azure AD] > [MFA サーバー] > [アクティビティ レポート] | NPS の拡張機能、AD FS、MFA サーバーを介した MFA の全体的な利用状況に関する情報を提供します。 |
| ユーザーの認証バイパスの履歴 | [Azure AD] > [MFA サーバー] > [ワンタイム バイパス] | ユーザーの Multi-Factor Authentication バイパスの要求履歴を提供します。 |
| サーバーの状態 | [Azure AD] > [MFA サーバー] > [サーバーの状態] | アカウントに関連付けられている Multi-Factor Authentication Server の状態を示します。 |

## <a name="view-reports"></a>レポートを表示する 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で **[Azure Active Directory]** > **[MFA サーバー]** を選択します。
3. 表示するレポートを選択します。

   <center>![クラウド](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>PowerShell のレポート

以下の PowerShell を使用して、MFA に登録しているユーザーを識別します。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

以下の PowerShell を使用して、MFA に登録されていないユーザーを識別します。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>次の手順

* [ユーザー向け](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md)
* [デプロイする場所](concept-mfa-whichversion.md)
