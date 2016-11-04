---
title: Azure Active Directory Identity Protection の通知 | Microsoft Docs
description: 通知が調査作業にどのように役立つのかを説明します。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2016
ms.author: markvi

---
# Azure Active Directory Identity Protection の通知
Azure AD Identity Protection では、ユーザーのリスクとリスク イベントの管理に役立つ 2 種類の自動通知電子メールが送信されます。

* ユーザー侵害アラート電子メール
* 週間ダイジェスト電子メール

## ユーザー侵害アラート電子メール
ユーザー侵害電子メール アラートは、Azure AD Identity Protection がアカウントの侵害を識別すると生成されます。この電子メールには、Identity Protection ダッシュボードのリスクのフラグ付きユーザー レポートへのリンクが含まれます。侵害に関する通知はすぐに調査することをお勧めします。

## 週間ダイジェスト電子メール
週間ダイジェスト電子メールには、新しいリスク イベントの概要が含まれます。<br> 次の情報が含まれます。

* リスクのあるユーザー
* 不審なアクティビティ
* 検出された脆弱性
* Identity Protection の関連するレポートへのリンク

<br> ![修復](./media/active-directory-identityprotection-notifications/400.png "修復") <br>

週間ダイジェスト電子メールの送信は無効化できます。 <br><br> ![ユーザーのリスク](./media/active-directory-identityprotection-notifications/62.png "ユーザーのリスク") <br>

**関連する構成ダイアログ ボックスを開くには**:

1. **[Azure AD Identity Protection]** ブレードで、**[設定]** をクリックします。 <br><br> ![ユーザー リスクのポリシー](./media/active-directory-identityprotection-notifications/401.png "ユーザー リスクのポリシー") <br>
2. **[全般]** セクションで **[通知]** をクリックします。 <br><br> ![ユーザー リスクのポリシー](./media/active-directory-identityprotection-notifications/405.png "ユーザー リスクのポリシー") <br>

## 関連項目
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

<!---HONumber=AcomDC_0803_2016-->