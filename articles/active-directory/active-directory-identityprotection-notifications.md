---
title: "Azure Active Directory Identity Protection の通知 | Microsoft Docs"
description: "通知が調査作業にどのように役立つのかを説明します。"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: bea21439afef4fda453732edffc84c62667dfe38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection の通知

Azure AD Identity Protection では、ユーザーのリスクとリスク イベントの管理に役立つ 2 種類の自動通知電子メールが送信されます。

- 危険な状態のユーザーが検出された電子メール
- 週間ダイジェスト電子メール

この記事では、両方の通知メールの概要を説明します。


## <a name="users-at-risk-detected-email"></a>危険な状態のユーザーが検出された電子メール

危険な状態のアカウントが検出されると、Azure AD Identity Protection は **[Users at risk detected]\(危険な状態のユーザーが検出されました\)** という件名のアラート メールを生成します。 このメールには、**[リスクのフラグ付きユーザー](active-directory-reporting-security-user-at-risk.md)** レポートへのリンクが含まれます。 ベスト プラクティスとして、危険な状態のユーザーをすぐに調べる必要があります。

![危険な状態のユーザーが検出された電子メール](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>構成

管理者は以下を設定することができます。

- **このメールの生成をトリガーするリスク レベル** - 既定では、リスク レベルは "高" リスクに設定されます。
- **このメールの受信者** - 既定では、受信者にはすべてのグローバル管理者が含まれます。 グローバル管理者は、受信者として他のグローバル管理者、セキュリティ管理者、セキュリティ閲覧者を追加することもできます。  


関連するダイアログを開くには、**[Identity Protection]** ページの **[設定]** セクションの **[アラート]** をクリックします。

![危険な状態のユーザーが検出された電子メール](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>週間ダイジェスト電子メール

週間ダイジェスト電子メールには、新しいリスク イベントの概要が含まれます。  
次の情報が含まれます。

- リスクのあるユーザー

- 不審なアクティビティ

- 検出された脆弱性

- Identity Protection の関連するレポートへのリンク

    ![修復](./media/active-directory-identityprotection-notifications/400.png "Remediation")

### <a name="configuration"></a>構成

管理者は、週間ダイジェスト電子メールの送信を無効にできます。

![ユーザーのリスク](./media/active-directory-identityprotection-notifications/62.png "User risks")

関連するダイアログを開くには、**[Identity Protection]** ページの **[設定]** セクションの **[週間ダイジェスト]** をクリックします。

![危険な状態のユーザーが検出された電子メール](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>関連項目

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
