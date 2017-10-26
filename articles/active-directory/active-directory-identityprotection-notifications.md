---
title: "Azure Active Directory Identity Protection の通知 | Microsoft Docs"
description: "通知が調査作業にどのように役立つのかを説明します。"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: abc0f3926905295a9cf239146cce7fc57da7eb29
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection の通知
Azure AD Identity Protection では、ユーザーのリスクとリスク イベントの管理に役立つ 2 種類の自動通知電子メールが送信されます。

* ユーザー侵害アラート電子メール
* 週間ダイジェスト電子メール

## <a name="user-compromised-alert-email"></a>ユーザー侵害アラート電子メール
ユーザー侵害電子メール アラートは、Azure AD Identity Protection がアカウントの侵害を識別すると生成されます。 この電子メールには、Identity Protection ダッシュボードのリスクのフラグ付きユーザー レポートへのリンクが含まれます。 侵害されたアカウントに関する通知はすぐに調査することをお勧めします。

## <a name="weekly-digest-email"></a>週間ダイジェスト電子メール
週間ダイジェスト電子メールには、新しいリスク イベントの概要が含まれます。<br>
次の情報が含まれます。

* リスクのあるユーザー
* 不審なアクティビティ
* 検出された脆弱性
* Identity Protection の関連するレポートへのリンク

<br>
![修復](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br>

週間ダイジェスト電子メールの送信は無効化できます。
<br><br>
![ユーザーのリスク](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>

**関連する構成ダイアログ ボックスを開くには**:

1. **[Azure AD Identity Protection]** ブレードで、**[設定]** をクリックします。
   <br><br>
   ![ユーザーのリスク ポリシー](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
   <br>
2. **[全般]** セクションで **[通知]** をクリックします。
   <br><br>
   ![ユーザーのリスク ポリシー](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
   <br>

## <a name="see-also"></a>関連項目
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
